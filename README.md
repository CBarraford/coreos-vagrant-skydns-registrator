# CoreOS Vagrant + Skydns + Registrator

This repo provides a template Vagrantfile to create a CoreOS virtual machine using the VirtualBox software hypervisor.
After setup is complete you will have a three node cluster with skydns and registrator already setup and working.

### What is Skydns?

[Skydns](https://github.com/skynetservices/skydns) is basically a dns service with etcd as its backend/db. You edit dns entries by adding/removing data from etcd in a specific format. See [their repo](https://github.com/skynetservices/skydns) for more info.

### What is Registrator?
[Registrator](https://github.com/progrium/registrator) is a serivce discovery tool. It monitors the docker events API to see when a container comes up or goes down and edits a database of choice ([etcd](https://github.com/coreos/etcd) in our case, but also supports [consul](https://consul.io/)).

### Put them together and what do you get??
Skydns serves dns requests, while registrator automatically adds and removes dns entries via etcd when you start/stop docker containers. You could use another tool similar to registrator called [skydock](https://github.com/crosbymichael/skydock), but at this time, it only supports Skydns verison 1 and not the latest, version 2. There is talk about [merging skydock and skydns](https://github.com/skynetservices/skydns/issues/94), which would be awesome.

## More README

See the original source code [README](https://github.com/coreos/coreos-vagrant/blob/master/README.md) for more info about how to setup vagrant, virtualbox, etc, etc. Almost everything in there applies here as well.

### Example Usage

Once the cluster is up, launching containers should automagically create DNS records for the services in that container so its dynamically available to all nodes within the cluster. Of course, thats assuming that skydns and registrator are running properly. We can easily check if all skydns and registrator are up on all hosts by running `fleetctl list-units` on any node in the clude. 

Now that we've confirmed everything is running ok, here is an example of using dynamic DNS across nodes.

1. Generate a new etcd key and update the `user-data` file (`curl https://discovery.etcd.io/new`). You need to do this step everytime you do `vagrant destroy`
2. Run `vagrant up`
3. Launch a redis server on `core-01` (`docker run -d -p 6379:6379 --name redis -e "SERVICE_NAME=redis/db1" redis`)
4. Launch another redis server on `core-02` (`docker run -d -p 6379:6379 --name redis -e "SERVICE_NAME=redis/db2" redis`)
5. On `core-03`, connect to redis via `redis-cli` (`docker run --rm -it crosbymichael/redis-cli -h db1.redis.skydns.local`)
  You can also connect to one of the redis nodes randomly (`docker run --rm -it crosbymichael/redis-cli -h redis.skydns.local`).
  SRV records have also been created, so you can dynamically discover ports (`dig SRV redis.skydns.local`).

### Thanks
Special thanks to the guys at [CoreOs](https://coreos.com/), [Jeff Lindsay](https://github.com/progrium) for his work on registrator, and to the guys at [skynetservices](https://github.com/skynetservices) for building skydns.
