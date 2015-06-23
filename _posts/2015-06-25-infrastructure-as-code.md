---
layout: post
title: "Infrastructure as Code"
date: "2015-06-25 09:00:00"
icon: wrench
tags: infrastructure, configuration management, continuous integration, jenkins
categories: tech-stories
authors: ["Jens Fischer", "Dirk Jablonski"]
---

Here at ePages we heavily rely on [Continuous Integration (CI)](http://www.martinfowler.com/articles/continuousIntegration.html) to make sure our code is always in a good shape. A mixed language stack mainly composed of `Perl`, `Java` and `JavaScript` spread over multiple teams brings in different build processes and toolings needed to produce the artifacts our software is composed of, e.g. `make`, `gradle` and `grunt`. [Jenkins](https://jenkins-ci.org/) is the software we use as our CI tool. For distributing the load created by each build process we leverage the [master/slave feature](https://wiki.jenkins-ci.org/display/JENKINS/Step+by+step+guide+to+set+up+master+and+slave+machines) provided by Jenkins. We do not run all build jobs on all Jenkins instances, but already spread out specific jobs to dedicated Jenkins instances. Nevertheless some teams still share their build infrastructure, thus lacking independance e.g. when it comes to introducing new Jenkins plugins or other tools needed for their build process.

# Jenkins configuration

Jenkins makes it very easy to introduce new functionality by installing one of the [many available plugins](https://wiki.jenkins-ci.org/display/JENKINS/Plugins). Unfortunately not every plugin turns out to work as expected or in the worst case can potentially bring down the whole Jenkins instance. It is a bad idea to evaluate unknown plugins by installing them into your production CI infrastructure. Even making changes to a job configuration without adding any new plugins can lead to broken build jobs, negatively affecting the productivity of whole teams.

## The solution

We were looking for ways to make it safe and easy to improve our build infrastructure. Using version control like [GitHub](https://github.com/) for all of our source code it was natural to treat our infrastructure the same way and be able to roll back to a previous version without any hassle. Versioning whole images of Jenkins virtual servers didn't sound like the best idea and plugins like [JobConfigHistory](https://wiki.jenkins-ci.org/display/JENKINS/JobConfigHistory+Plugin) didn't go far enough.


## Server provisioning using Ansible and Vagrant

We started by tackling the perceived root problem: the installation of the Jenkins master, including the required plugins.  The idea is to be able to set up a Jenkins master from scratch by just running a single script, while still being able to use the same facilities to update the installation, e.g. with new plugins, using the same technology. To achieve this, we decided to use [Ansible](http://www.ansible.com/home), mainly due to existing know-how.

Once the basic installation of Jenkins master and it's prerequisites is done, the interesting part begins: installing & configuring plugins. The installation of the additional plugins is done by utilizing the [Jenkins-CLI](https://wiki.jenkins-ci.org/display/JENKINS/Jenkins+CLI). This allows you to provide plugin names and versions to be installed. The tricky part is getting the configuring of the server right. For this, we have so far not found a more clever solution than just to create it locally (but templated) and copy it over to the master using the according Ansible task. The addition of nodes can be done via Jenkins-CLI again.

With the toolset described so far, the basic setup can be easily re-created any time. And since this is so easy now, we usually test our setup scripts by installing them to local [Vagrant](https://www.vagrantup.com/) machines. Especially for the global Jenkins configuration, this is an essential step, which is even easier and faster due to Ansible's capability of appliying only changes to an already provisioned system. Just re-apply the playbook, and changes in configuration are active - and you are sure they are based on your version-managed infrastructure code!

## Job provisioning using Job DSL

Usually, Jenkins jobs are created manually using the web UI, and are stored in XML configuration files. These files are quite verbose, and are not nice to edit directly, if you want to create a new job based on an existing one, but do this in an automated fashion. Here a very nice plugin comes to help: the [Job DSL Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Job+DSL+Plugin). This plugin allows the creation of jobs from simplified DSL descriptions, which are much nicer to read and write then the XML config files. All that is needed to create all your jobs definitions is a single so-called "seed" job, which (in our case) pulls the job definitions as Groovy DSL scripts from Git and creates these jobs.

The seed job can be run whenerver there are changes in the DSL scripts for the jobs, and updates the configuration of changed jobs accordingly. Existing build metadata, like next build number files, is not touched by such an update. Which brings us to the next topic...

## Handling of job metadata

The job metadata, which include the `nextBuildNumber` file, the workspace and the results of the last (kept) builds, is still a tricky topic. As these files are created or updated by the actual builds, they are not easily recreated. Therefore, we stick to doing some plain old backup job, which saves those parts, which are really relevant. For us, these are the `nextBuildNumber` files and the `builds` directory, which contains the information on the last builds. All other job metadata are either not really important, like the symlinks to the last successful build dir, or can will automatically be recreated with the next build, e.g. the workspace.

## Running Jenkins slaves on developer machines

Being able to run lots of build jobs in parallel is key to receiving fast feedback from our CI infrastructure. With modern desktops and laptops there is enough idle CPU and RAM resources available in our offices most of the time, which we want to use for this purpose by running Jenkins slaves. In order not to interfere with the operating system and tools installation on each developer's machine, we create virtual images (or maybe even [Docker](https://www.docker.com/) containers in the future) containing all the components needed for a Jenkins slave to run build jobs. Using the [Jenkins Swarm Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Swarm+Plugin) each slave registers itself at it's master and can be used to execute build jobs. Each build job is configured to carry one or more `label`s, which control that they get only executed on slaves which can support these kind of jobs. This way we can separate e.g. [Selenium](http://www.seleniumhq.org/) integration tests from unit tests.

## Outlook

We want to be able to offer each team an easy way to setup and manage their own dedicated CI infrastructure and tweak it to their specific needs. By spreading the load of executing build jobs to each developer's machine we can further shorten the time a team needs to wait for the [GitHub pull request builder plugin](https://wiki.jenkins-ci.org/display/JENKINS/GitHub+pull+request+builder+plugin) to check if a pull request is safe to merge into the main development line, thus preventing stale pull requests and later merge hell. We could also offload more slaves to e.g. [Amazon EC2](https://aws.amazon.com/ec2/), if we really need the compute power. The process of moving our custom Jenkins installations and job configurations to this new CI infrastructure has just started and we are eager to learn how this will turn out in the future.
