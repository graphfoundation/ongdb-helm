# ONgDB

[ONgDB](https://graphfoundation.org/ongdb) is a highly scalable native graph database that
leverages data relationships as first-class entities, helping enterprises build
intelligent applications to meet today’s evolving data challenges.

## TL;DR;

```bash
$ helm install stable/ongdb
```

## Introduction

This chart bootstraps a [ONgDB](https://github.com/graphfoundation/docker-ongdb-publish)
deployment on a [Kubernetes](http://kubernetes.io) cluster using the
[Helm](https://helm.sh) package manager.

## Prerequisites

* Kubernetes 1.6+ with Beta APIs enabled
* PV provisioner support in the underlying infrastructure

## Installing the Chart

To install the chart with the release name `ongdb-helm`:

```bash
$ helm install --name ongdb-helm stable/ongdb --set ongdbPassword=mySecretPassword
```

The command deploys ONgDB on the Kubernetes cluster in the default configuration
but with the password set to `mySecretPassword`. The
[configuration](#configuration) section lists the parameters that can be
configured during installation.

> **Tip**: List all releases using `helm list`

## Uninstalling the Chart

To uninstall/delete the `ongdb-helm` deployment:

```bash
$ helm delete ongdb-helm --purge
```

The command removes all the Kubernetes components associated with the chart and
deletes the release.

## Configuration

The following table lists the configurable parameters of the ONgDB chart and
their default values.

| Parameter                             | Description                                                                                                                             | Default                                         |
| ------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------- |
| `image`                               | ONgDB image                                                                                                                             | `ongdb`                                         |
| `imageTag`                            | ONgDB version                                                                                                                           | `{VERSION}`                                     |
| `imagePullPolicy`                     | Image pull policy                                                                                                                       | `IfNotPresent`                                  |
| `podDisruptionBudget`                 | Pod disruption budget                                                                                                                   | `{}`                                            |
| `authEnabled`                         | Is login/password required?                                                                                                             | `true`                                          |
| `ongdbPassword`                       | Password to log in the ONgDB database if password is required                                                                           | (random string of 10 characters)                |
| `core.numberOfServers`                | Number of machines in CORE mode                                                                                                         | `3`                                             |
| `core.sideCarContainers`              | Sidecar containers to add to the core pod. Example use case is a sidecar which identifies and labels the leader when using the http API | `{}`                                            |
| `core.initContainers`                 | Init containers to add to the core pod. Example use case is a script that installs the APOC library                                     | `{}`                                            |
| `core.persistentVolume.enabled`       | Whether or not persistence is enabled                                                                                                   | `true`                                          |
| `core.persistentVolume.storageClass`  | Storage class of backing PVC                                                                                                            | `standard` (uses beta storage class annotation) |
| `core.persistentVolume.size`          | Size of data volume                                                                                                                     | `10Gi`                                          |
| `core.persistentVolume.mountPath`     | Persistent Volume mount root path                                                                                                       | `/data`                                         |
| `core.persistentVolume.subPath`       | Subdirectory of the volume to mount                                                                                                     | `nil`                                           |
| `core.persistentVolume.annotations`   | Persistent Volume Claim annotations                                                                                                     | `{}`                                            |
| `readReplica.numberOfServers`         | Number of machines in READ_REPLICA mode                                                                                                 | `0`                                             |
| `readReplica.autoscaling.enabled`  | Enable horizontal pod autoscaler  | `false`  |
| `readReplica.autoscaling.targetAverageUtilization`  | Target CPU utilization  | `70`  |
| `readReplica.autoscaling.minReplicas` | Min replicas for autoscaling  | `1`  |
| `readReplica.autoscaling.maxReplicas`  | Max replicas for autoscaling  | `3` |
| `readReplica.initContainers`          | Init containers to add to the replica pod. Example use case is a script that installs the APOC library                                  | `{}`                                            |
| `resources`                           | Resources required (e.g. CPU, memory)                                                                                                   | `{}`                                            |
| `clusterDomain`                       | Cluster domain                                                                                                                          | `cluster.local`                                 |

The above parameters map to the env variables defined in the
[ONgDB docker image](https://github.com/ongdb/docker-ongdb).

Specify each parameter using the `--set key=value[,key=value]` argument to `helm
install`. For example,

```bash
$ helm install --name ongdb-helm --set core.numberOfServers=5,readReplica.numberOfServers=3 stable/ongdb
```

The above command creates a cluster containing 5 core servers and 3 read
replicas.

Alternatively, a YAML file that specifies the values for the parameters can be
provided while installing the chart. For example,

```bash
$ helm install --name ongdb-helm -f values.yaml stable/ongdb
```

> **Tip**: You can use the default [values.yaml](values.yaml)

Once you have all 3 pods in running, you can run the "test.sh" script in this directory, which will verify the role attached to each pod and also test recovery of a failed/deleted pod. This script requires that the $RELEASE_NAME environment variable be set, in order to access the pods, if you have specified a custom `namespace` or `replicas` value when installing you can set those via `RELEASE_NAMESPACE` and `CORE_REPLICAS` environment variables for this script.

