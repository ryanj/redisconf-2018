# RedisConf 2018 Demos by RyanJ

Slides: http://bit.ly/redis-devtestprod

To follow along with these examples, you'll need access to an OpenShift environment. Minishift is one way to get started: http://github.com/minishift/minishift

## Dev Demos
Redis-powered web solutions can be assembed piece by piece using the Service Catalog.

Use the Service Catalog to lanch the "**Redis (Ephemeral)**" service. Make sure to choose the option to "create a secret" / binding during the setup. 

Next, try building *this repo* using the Service Catalog's *NodeJS* runtime: https://github.com/ryanj/redisconf-2018

When the build and deployment are completed, check OpenShift's dashboard Overview page to find the resulting web service address (or **Route**).  Open the link to see view the resulting service which echoes out the available redis integration variables and credentials.

**What do you find?**

Access the container environments via the web-based *Terminal* to investigate.

Show realtime changes via the web container: Edit `index.html` in a terminal, then reload the page to preview your changes

Use a rolling deployment to demo outage-free updates

## Test Demos

Deploy the same thing in a new project, while choosing the other (non-ephemeral) "Redis" service from the catalog.

Test scaling and autorecovery.  Scale the web instances up to 6, then kill four of them. Show autorecovery. Reload the web page to show that kubernetes can automatically route around and restore the availability of these types of services without any visible disruption for external users.

Run `redis-cli cluster nodes` in the Terminal of the redis pod, to confirm that we are *not* configured to run Redis as a cluster.

## Production Demos

*WARNING: NOT FOR USE IN PRODUCTION*

**Step1:** Install the template (NOT IN PRODUCTION!)

Switch to a new project, then use `oc create` to add a new `Redis Cluster` catalog item:

```bash
oc create -f https://raw.githubusercontent.com/ryanj/redisconf-2018/master/redis-cluster-template.yml
```

The above command should install a new `Redis Cluster` option to your Service Catalog!

**Step2:** Use your web browser to deploy the Redis Cluster in a new project

Use the OpenShift web console to switch to your latest project. Then use the *`Add to Project`* button to find the new Redis Cluster catalog entry.  Launch it using the available defaults.

After one of the first pod is deployed, use the available web terminal to run: `redis-cli cluster nodes`

The output should show that clustering capabilities are available, but that this node is not yet configured to talk to it's peers.

**Step3:** Configure the cluster

**After all 6 pods are deployed**, run the following **from your laptop** to join this cluster of Redis nodes: 

```bash
kubectl exec -it redis-cluster-0 -- redis-trib create --replicas 1 \
$(kubectl get pods -l app=redis-cluster -o jsonpath='{range.items[*]}{.status.podIP}:6379 ')
```

Choose "`yes`" to confirm your cluster configuration update.  

Run `kubectl exec -it redis-cluster-0 -- redis-cli cluster nodes` to confirm that all six pods have been successfully configured as a cluster.

**Step4:** Demo Autorecovery of DB tier:

1. In an OpenShift Web Terminal, run: `watch redis-cli cluster nodes`, or try `kubectl exec -it redis-cluster-0 -- watch redis-cli cluster nodes` from your laptop.  This should provide a dashboard of the current cluster state.

2. Delete two master Redis nodes, and observe the results in your other terminal:

```bash
kubectl delete po/redis-cluster-1 po/redis-cluster-2
```

### Wrap up

See [Sander Ploegsma's blog post on "Running Redis Cluster on Kubernetes"](https://sanderp.nl/running-redis-cluster-on-kubernetes-e451bda76cad) for additional Redis clustering examples.

If you are interested in learning about [Redis Operators for Kubernetes](https://product.spotahome.com/redis-operator-for-kubernetes-released-9c6775e7da9b), take a look at:

1. https://github.com/spotahome/redis-operator
2. https://github.com/jw-s/redis-operator

# Thank You!

Slides: http://bit.ly/redis-devtestprod
