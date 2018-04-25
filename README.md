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

Deploy the same thing, but tell Redis to use a Persistent Volume

Test scaling and autorecovery

Run `redis-cli cluster nodes` in the redis Terminal

## Production Demos

*WARNING: NOT FOR USE IN PRODUCTION*

Install the template (NOT IN PRODUCTION):

Launch your Redis Cluster using the Service Catalog

In a Web Terminal, run: `redis-cli cluster nodes`

Join the cluster: 

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
