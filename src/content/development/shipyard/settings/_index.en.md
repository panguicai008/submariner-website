---
title: "Customizing Deployments"
date: 2020-05-05T09:11:00+0300
weight: 10
---

Shipyard supports specifying different settings for each deployed cluster.
The settings are specified using a **`SETTINGS`** variable, which must be set in the `Makefile` of each consuming project.

## Using Custom Settings

Set the **`SETTINGS`** variable to deploy with your custom settings file (must be inside the project's directory structure), e.g.:

```shell
make deploy SETTINGS=<path/to/settings>.yaml
```

## Deployment Settings File

The settings are specified in a YAML file, where default and per cluster settings can be provided.
All clusters are listed under the **`clusters`** key, and each cluster can have specific deployment settings.
All cluster specific settings can be specified on the root of the settings file to determine defaults.

The possible settings are:

* Global settings:
  * **`broker`**: Special key to mark the broker, set to anything to select a broker (defaults to the first cluster).
  * **`cluster_count`**: Can be used to quickly deploy multiple clusters with an identical topology.
  * **`clusters`**: Map of clusters to deploy. Each key is the cluster name and the values are cluster specific settings.
* Global and/or cluster specific:
  * **`cni`**: Which CNI to deploy on the cluster, currently supports **`weave`** and **`ovn`**.
  * **`nodes`**: A space separated list of nodes to deploy, supported types are **`control-plane`** and **`worker`**.
  * **`submariner`**: If Submariner should be deployed, set to **`true`**. Otherwise, leave unset (or set to **`false`** explicitly).

## Settings File Examples

For example, a basic settings file that deploys a couple of clusters with weave CNI:

```yaml
cni: weave
submariner: true
nodes: control-plane worker worker
clusters:
  cluster1:
  cluster2:
```

The following settings file deploys two clusters with one control node and two workers, with weave and Submariner.
The third cluster will host the broker and as such needs no CNI, only a worker node, and no Submariner deployment:

```yaml
cni: weave
submariner: true
nodes: control-plane worker worker
clusters:
  cluster1:
  cluster2:
  cluster3:
    broker: true
    cni:
    submariner: false
    nodes: control-plane
```
