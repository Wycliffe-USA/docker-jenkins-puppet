# docker-jenkins-puppet

A dockerized version of the Jenkins image with rbenv dependencies pre-installed. 

### Usage

`docker pull wusa/jenkins-puppet` will fetch the container. Because the Jenkins user runs as UID 1000, mounting a directory into the container with proper read and write permissions can be challenging, since UID 1000 is almost always already occupied by another user. 

Rather than use a UID in the range typically reserved for service accounts, this image assumes that UID 10000 is available on your system. A UID can be mapped into a container like so:

`docker run -d -it -u 10000 -v /srv/jenkins/:/var/jenkins_home -p 8080:8080 --name jenkins wusa/jenkins-puppet`

This example assumes that `/srv/jenkins/` is owned by a limited-privilege user (such as a jenkins account) on the host. The drawback of this method is that the container will complain that the mapped uid (10000 in our case) does not exist when attempting to do certain things, like use SSH for git activities. This container's solution is to create a dummy user with UID 10000 inside, but not use it directly for anything.

All other usage should follow the existing [Jenkins documentation](https://hub.docker.com/_/jenkins/).

### Running rbenv

[Rbenv](https://github.com/rbenv/rbenv) is a Ruby version management tool. Jenkins has a nice, simple plugin for it which this container is designed to leverage. To install the plugin, simply navigate to Manage Jenkins -> Manage Plugins, then under the 'available' tab, search for rbenv, check, install, and let Jenkins restart itself. 

To use rbenv in a job, build a new or configure an existing job, then under the 'Build Environment' section of the job, check the box for 'rbenv build wrapper' and select your Ruby version. You can also preinstall gems here, so this is a good place to make sure Bundler is installed so that you can `bundle install` from a Gemfile prior to launching your tests.
