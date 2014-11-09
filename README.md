# CoreOS Vagrant + Skydns + Registrator

This repo provides a template Vagrantfile to create a CoreOS virtual machine using the VirtualBox software hypervisor.
After setup is complete you will have a three node cluster with skydns and registrator already setup and working.

### What is Skydns?

[Skydns](https://github.com/skynetservices/skydns) is basically a dns service with etcd as its backend/db. You edit dns entries by adding/removing data from etcd in a specific format. See [their repo](https://github.com/skynetservices/skydns) for more info.

### What is Registrator
Registrator is a serivce discovery tool. It monitors the docker events API to see when a container comes up or goes down and edits a database of choice ([etcd](https://github.com/coreos/etcd) in our case, but also supports [consul](https://consul.io/)).

### Put them together and what do you get??
Skydns serves dns requests, while registrator automatically adds and removes dns entries via etcd when you start/stop docker containers. You could use another tool similar to registrator called [skydock](https://github.com/crosbymichael/skydock), but at this time, it only supports Skydns verison 1 and not the latest, version 2. There is talk about [merging skydock and skydns](https://github.com/skynetservices/skydns/issues/94), which would be awesome.

## More README

See the original source code [README](https://github.com/coreos/coreos-vagrant/blob/master/README.md) for more info about how to setup vagrant, virtualbox, etc, etc. Almost everything in there applies here as well.
