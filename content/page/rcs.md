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
$ kubectl create -f https://raw.githubusercontent.com/mhausenblas/kbe/master/specs/rcs/rc.yaml
```

You can see the RC and the pod it looks after like so:

```bash
$ kubectl get rc
NAME                DESIRED   CURRENT   READY     AGE
rcex                1         1         1         3m

$ kubectl get pods --show-labels
NAME           READY     STATUS    RESTARTS   AGE    LABELS
rcex-qrv8j     1/1       Running   0          4m     app=sise
```

Note two things here:

- the supervised pod got a random name assigned
(`rcex-qrv8j`)
- the way the RC keeps track of its pods is via the label, here `app=sise`

To scale up, that is, to increase the number of replicas, do:

```bash
$ kubectl scale --replicas=3 rc/rcex

$ kubectl get pods -l app=sise
NAME         READY     STATUS    RESTARTS   AGE
rcex-1rh9r   1/1       Running   0          54s
rcex-lv6xv   1/1       Running   0          54s
rcex-qrv8j   1/1       Running   0          10m

```

Finally, to get rid of the RC and the pods it is supervising, use:

```bash
$ kubectl delete rc rcex
replicationcontroller "rcex" deleted
```

Note that, going forward, the RCs are called [replica sets](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) (RS), supporting set-based selectors. The RS are already in use in the context of [deployments](/deployments/).
