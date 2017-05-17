+++
title = "Replication Controllers"
subtitle = "Openshift replication controllers by example"
date = "2017-05-17"
url = "/rcs/"
+++

A replication controller (RC) ensures that a given number of replicas of a pod is running at all times.
An RC will launch a specified number of pods (`replicas`) and makes
sure that they keep running. If a pod exit or is killed, the replication controller will instantiate more to match the desired number.
A replication controller is composed by the desired number of replicas, the pod definition, and a selector to identify managed pods.

The selector is a set of labels assigned to the pods that are managed by the replication controller. In order to check the number of instances of the pods running, the replication controller will use the selector.

To create an [RC](https://github.com/redhat-italy/obe/blob/master/specs/rcs/rc.yaml)
that ensures a single replica of a pod:

```bash
$ oc create -f https://raw.githubusercontent.com/redhat-italy/obe/master/specs/rcs/rc.yaml
```

You can see the RC and the pod it looks after like so:

```bash
$ oc get rc
NAME                DESIRED   CURRENT   READY     AGE
myrc                1         1         1         3m

$ oc get pods --show-labels
NAME         READY     STATUS    RESTARTS   AGE       LABELS
myrc-8ztg3   1/1       Running   0          27s       app=myhello
```

Note two things here:

- the supervised pod got a random name assigned
(`myrc-8ztg3`)
- the way the RC keeps track of its pods is via the label, here `app=myhello`

To scale up, that is, to increase the number of replicas, do:

```bash
$ oc scale --replicas=2 rc/myrc
$ oc get pods
NAME         READY     STATUS    RESTARTS   AGE
myrc-3fdq3   1/1       Running   0          15s
myrc-8ztg3   1/1       Running   0          1m
```

Finally, to get rid of the RC and the pods it is supervising, use:

```bash
$ oc delete rc/myrc
replicationcontroller "myrc" deleted
```
