+++
title = "Pods"
subtitle = "Openshift pods by example"
date = "2017-05-14"
url = "/pods/"
+++

A pod is a collection of containers sharing a network and mount namespace
and is the basic unit of deployment in Openshift. All containers in a pod
are scheduled on the same node.

To launch a pod using the container [image](https://hub.docker.com/r/openshift/hello-openshift/)
`openshift/hello-openshift:latest` and exposing a HTTP service on port `8080`, execute:

```bash
$ oc run myhello --image=openshift/hello-openshift:latest --port=8080
```

We can now see that the pod is running:

```bash
$ oc get pods
NAME              READY     STATUS    RESTARTS   AGE
myhello-1-hr9mf   1/1       Running   0          13s

$ oc describe pod myhello-1-hr9mf | grep IP:
IP:                     172.17.0.3
```

From within the cluster this pod is accessible via the pod IP `172.17.0.3`,
which we've learned from the `kubectl describe` command above:

```bash
[cluster] $ curl 172.17.0.3:8080
Hello OpenShift!
```

Note that `oc run` creates a [DeploymentConfig](/deploymentconfig/), so in order to
get rid of the pod you have to execute `oc delete dc myhello`.

Alternatively you can create a pod can from a configuration file. In our case
the [pod](https://github.com/redhat-italy/obe/blob/master/specs/labels/pod.yaml) is
running the already known `myhello` image from above along with
a generic `CentOS` container:


#/Arrived here.


```bash
$ oc create -f https://raw.githubusercontent.com/redhat-italy/obe/master/specs/pods/pod.yaml

$ oc get pods
NAME                      READY     STATUS    RESTARTS   AGE
twocontainers             2/2       Running   0          7s
```

Now we can exec into the `CentOS` container and access the `myhello`
on localhost:

```bash
$ oc exec twocontainers -c shell -i -t -- bash
[root@twocontainers /]# curl localhost:9876/info
{"host": "localhost:9876", "version": "0.5.0", "from": "127.0.0.1"}
```

Specify the `resources` field in the pod to influence how much CPU and/or RAM a
container in a [pod](https://github.com/mhausenblas/kbe/blob/master/specs/pods/constraint-pod.yaml)
can use (here: `64MB` of RAM and `0.5` CPUs):

```bash
$ kubectl create -f https://raw.githubusercontent.com/mhausenblas/kbe/master/specs/pods/constraint-pod.yaml

$ kubectl describe pod constraintpod
...
Containers:
  sise:
    ...
    Limits:
      cpu:      500m
      memory:   64Mi
    Requests:
      cpu:      500m
      memory:   64Mi
...
```

Learn more about resource constraints in Kubernetes via the docs [here](https://kubernetes.io/docs/tasks/configure-pod-container/assign-cpu-ram-container/)
and [here](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/).

To sum up, launching one or more containers (together) in Kubernetes is simple,
however doing it directly as shown above comes with a serious limitation: you have to
manually take care of keeping them running in case of a failure. A better way
to supervise pods is to use [replication controllers](/rcs/), or even better
[deployments](/deployments), giving you much more control.
