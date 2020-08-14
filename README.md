# kckn

## Description

kckn is a simple tool for managing per-shell Kubernetes contexts via
the [`KUBECONFIG`][kubeconfig-docs] env var. Like
[vital-software/kc](https://github.com/vital-software/kc/), it "works
around the lack of a [KUBECONTEXT][no-kubecontext-issue] environment
variable."

## Features

* kckn generates kubeconfigs that will even work with tools that don't
  respect `PATH`-style (colon-separated) `KUBECONFIG` values.
* kckn does not modify your base kubeconfig.
* Leave definitions for your clusters and users to you to manage
  yourself in your base kubeconfig.

## Usage

* `. kc <cluster or cluster alias>` to set clusters; 
* `. kn <namespace or namespace alias>` to set namespaces; and
* `. kc off` to revert changes made by kckn.

You can define aliases to avoid typing the `.`, e.g. `alias
kc='. kc'`.

## Installation

1. Make sure that these scipts are on your `PATH`.
2. Make sure that your local copies of these scripts are executable.
3. Make sure that your base kubeconfig (see
   [configuration](#Configuration) below) contains all of the users
   and contexts that will be used in the contexts generated by kckn.

`kc` and `kn` both have bash completion support; to enable completion,
source this repo's `completion` file: `source /path/to/kckn/completion`.

## How it works

At the highest level, kckn generates ephemeral kubeconfigs whose
`current-cluster` value is `<one of your clusters>/<some namespace>`. 

kckn starts by generating a small kubeconfig that defines a context
for your cluster/namespace/user combination. Then, kckn uses kubectl
to flatten that kubeconfig onto your base kubeconfig. The resulting
kubeconfig is written to a temporary file, and the path to that
temporary file becomes the value of your `KUBECONFIG` shell
environment variable.

## Prompt customization

Several env vars can be used to add kckn information to your shell prompt:

| env var                 | description             |
|-------------------------|-------------------------|
| `__KCKN`                | `<cluster>/<namespace>` |
| `__KCKN_KUBE_CLUSTER`   | `<cluster>`             |
| `__KCKN_KUBE_NAMESPACE` | `<namespace>`           |
|                         |                         |

## Configuration

| env var                  | default value    | description                                                                                                                                                                                       |
|--------------------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `KCKN_BASE_KUBECONFIG`   | `~/.kube/config` | path to your base kubeconfig file, which should contain all of the users and contexts that will be used in your cluster-namespace contexts                                                        |
| `KCKN_CLUSTER_ALIASES`   |                  | path to the simple file whose lines consist of `<cluster alias> <cluster>` pairs denoting short-names for cluste to be used with kc                                                               |
| `KCKN_NAMESPACE_ALIASES` |                  | path to the simple file whose lines consist of `<namespace alias> <namespace>` pairs denoting short-names for namespaces to be used with kn                                                       |
| `KCKN_CLUSTER_USERS`     |                  | path to the simple file whose lines consist of `<cluster> <user>` pairs denoting which user is to be used with which cluster (by default, the user is assumed to be the same as the cluster name) |
|                          |                  |                                                                                                                                                                                                   |

## Differences from kc

* Works with any tool that supports `KUBECONFIG` tools without
  aliasing those tools.

[kubeconfig-docs]: https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/#the-kubeconfig-environment-variable
[no-kubecontext-issue]: https://github.com/kubernetes/kubernetes/issues/27308
