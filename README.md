# RedisConf 2018 Demos

Slides: http://bit.ly/redis-devtestprod

To follow along with these examples, you'll need access to an OpenShift environment. Minishift is one way to get started: http://github.com/minishift/minishift

## Dev Demos
Redis-powered web solutions can be assembed piece by piece using the Service Catalog.

Use the Service Catalog to lanch "*Redis (Ephemeral)*", then try building this repo using the Service Catalog's *NodeJS* runtime.

**What Happens?**

Access the container environments via the web-based *Terminal* to investigate.

Show realtime changes via the web container

Use a rolling deployment to show outage-free updates

## Test Demos

Deploy the same thing in a new project, while choosing the other (non-ephemeral) "Redis" service from the catalog.

Test scaling and autorecovery.  Scale the web instances up to 6, then kill four of them. Show autorecovery. Reload the web page to show that kubernetes can automatically route around and restore the availability of these types of services without any visible disruption for external users.

Run `redis-cli cluster nodes` in the Terminal of the redis pod, to confirm that we are *not* configured to run Redis as a cluster.

## Production Demos

*WARNING: NOT FOR USE IN PRODUCTION*

Install the template (NOT IN PRODUCTION):

```bash
oc create -f https://raw.githubusercontent.com/ryanj/redisconf-2018/master/redis-cluster-template.yml
```

This should install a new `Redis Cluster` option to your Service Catalog!

Use your web browser to deploy the Redis Cluster in a new project.

After one of the pods is deployed, run: `redis-cli cluster nodes` in it's web terminal.

From your laptop, run the following to join the cluster: 

```bash
kubectl exec -it redis-cluster-0 -- redis-trib create --replicas 1 \
$(kubectl get pods -l app=redis-cluster -o jsonpath='{range.items[*]}{.status.podIP}:6379 ')
```

Test Autorecovery:

1. In a Web Terminal, run: `watch redis-cli cluster nodes`

2. Delete two nodes and observe the result: 

```bash
kubectl delete po/redis-cluster-1 po/redis-cluster-2
```
