+++
title = "Labels"
subtitle = "Openshift labels by example"
date = "2017-05-17"
url = "/labels/"
+++

Labels are the mechanism you use to organize, group and select Openshift objects. A label is a key-value
pair, as in the following example:

```
(`env=development`)
(`app=myApp`)
```

Labels have certain [restrictions](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set)
concerning length and allowed values but without any pre-defined meaning.


So you're free to choose labels as you see fit, for example, to express
environments such as 'this pod is running in production' or ownership,
like 'department X owns that pod'. Openshift use labels internally in various scenarios. As an example, services reference groups of pods by using labels.

Let's create a [pod](https://github.com/redhat-italy/obe/blob/master/specs/labels/pod.yaml)
that initially has one label (`env=development`):

```bash
$ oc create -f https://raw.githubusercontent.com/redhat-italy/obe/master/specs/labels/pod.yaml

$ oc get pods --show-labels
NAME       READY     STATUS    RESTARTS   AGE    LABELS
podlabeled    1/1       Running   0          10m    env=development
```
In above `get pods` command note the `--show-labels` option that output the
labels of an object in an additional column.

You can add a label to the pod like so:

```bash
$ oc label pods podlabeled owner=giuseppe

$ oc get pods --show-labels
NAME        READY     STATUS    RESTARTS   AGE    LABELS
podlabeled     1/1       Running   0          16m    env=development,owner=giuseppe
```

To use a label for filtering, for example to list only pods that have an
`owner` that equals `giuseppe`, use the `--selector` option:

```bash
$ oc get pods --selector owner=giuseppe
NAME      READY     STATUS    RESTARTS   AGE
podlabeled   1/1       Running   0          27m
```

The `--selector` option can be abbreviated to `-l`, so to select pods that are
labeled with `env=development`, do:

```bash
$ oc get pods -l env=development
NAME      READY     STATUS    RESTARTS   AGE
podlabeled   1/1       Running   0          27m
```

Oftentimes, Openshift objects also support set-based selectors.
Let's launch [another pod](https://github.com/redhat-italy/obe/blob/master/specs/labels/anotherpod.yaml)
that has two labels (`env=production` and `owner=giuseppe`):

```bash
$ oc create -f https://raw.githubusercontent.com/redhat-italy/obe/master/specs/labels/anotherpod.yaml
```

Now, let's list all pods that are either labelled with `env=development` or with
`env=production`:

```bash
$ oc get pods -l 'env in (production, development)'
NAME           READY     STATUS    RESTARTS   AGE
labelex        1/1       Running   0          43m
labelexother   1/1       Running   0          3m
```

Note that labels are not restricted to pods. In fact you can apply them to
all sorts of objects, such as nodes or services.
