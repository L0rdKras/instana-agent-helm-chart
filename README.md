# Instana

[Instana](https://www.instana.com/) is a Dynamic APM for Microservice Applications

## Introduction

This chart adds the Instana Agent to all scheduluable nodes (e.g. by default, not masters) in your cluster via a `DaemonSet`.

## Prerequisites

Kubernetes 1.8+ or OpenShift 3.7+.

Working `helm` and `tiller`.

_Note:_ Tiller may need a service account and role binding if RBAC is enabled in your cluster. [1]

## Installing the Chart

To configure the installation you can either specify the options on the command line using the **--set** switch, or you can edit **values.yaml**. Either way you should ensure that you set values for:

* agent.key
* zone.name

If you're in the EU, you'll probably also want to set the regional equivalent values for:

* agent.endpointHost
* agent.endpointPort

Optionally, if your infrastructure uses a proxy, you should ensure that you set values for:

* agent.pod.proxyHost
* agent.pod.proxyPort
* agent.pod.proxyProtocol
* agent.pod.proxyUser
* agent.pod.proxyPassword
* agent.pod.proxyUseDNS

Optionally, if your infrastructure has multiple networks defined, you might need to allow the agent to listen on all addresses (typically with value set to '*'):

* agent.listenAddress

_Note:_ Check the values for the endpoint entries in the [agent backend configuration](https://docs.instana.io/quick_start/agent_configuration/#backend).

To install the chart with the release name `instana-agent` and set the values on the command line run:

```bash
$ helm install . --name instana-agent --namespace instana-agent \
--set agent.key=INSTANA_AGENT_KEY \
--set agent.endpointHost=HOST \
--set agent.endpointPort=PORT \
--set zone.name=CLUSTER_NAME \
--set agent.proxyHost=INSTANA_AGENT_PROXY_HOST \
--set agent.proxyPort=INSTANA_AGENT_PROXY_PORT \
--set agent.proxyProtocol=INSTANA_AGENT_PROXY_PROTOCOL \
--set agent.proxyUser=INSTANA_AGENT_PROXY_USER \
--set agent.proxyPassword=INSTANA_AGENT_PROXY_PASSWORD \
--set agent.proxyUseDNS=INSTANA_AGENT_PROXY_USE_DNS \
--set agent.listenAddress=INSTANA_AGENT_HTTP_LISTEN
```

To install the chart with the release name `instana-agent` after editing the **values.yaml** file, run:

```bash
$ helm install . --name instana-agent --namespace instana-agent
```

## Uninstalling the Chart

To uninstall/delete the `instana-agent` daemon set:

```bash
$ helm del --purge instana-agent
```

## Configuration

### Helm Chart

The following table lists the configurable parameters of the Instana chart and their default values.

|             Parameter              |            Description                                                  |                    Default                |
|------------------------------------|-------------------------------------------------------------------------|-------------------------------------------|
| `agent.key`                        | Your Instana Agent key                                                  | `Nil` You must provide your own key       |
| `zone.name`                        | Instana zone/cluster name                                               | `Nil` You must provide your own zone name |
| `rbac.create`                      | Whether to create & use RBAC resources                                  | `true`                                    |
| `agent.image.name`                 | The image name to pull                                                  | `instana/agent`                           |
| `agent.image.tag`                  | The image tag to pull                                                   | `latest`                                  |
| `agent.image.pullPolicy`           | Image pull policy                                                       | `IfNotPresent`                            |
| `agent.leaderElectorPort`          | Instana leader elector sidecar port                                     | `42655`                                   |
| `agent.endpointHost`               | Instana agent backend endpoint host                                     | `saas-us-west-2.instana.io`               |
| `agent.endpointPort`               | Instana agent backend endpoint port                                     | `443`                                     |
| `agent.pod.annotations`            | Additional annotations to apply to the pod                              | `{}`                                      |
| `agent.pod.schedulerTolerations`   | Tolerations for pod assignment                                          | `[]`                                      |
| `agent.pod.proxyHost`              | Hostname/address of a proxy                                             | `nil`                                     |
| `agent.pod.proxyPort`              | Port of a proxy                                                         | `nil`                                     |
| `agent.pod.proxyProtocol`          | Proxy protocol (Supported proxy types are "http", "socks4", "socks5")   | `nil`                                     |
| `agent.pod.proxyUser`              | Username of the proxy auth                                              | `nil`                                     |
| `agent.pod.proxyPassword`          | Password of the proxy auth                                              | `nil`                                     |
| `agent.pod.proxyUseDNS`            | Boolean if proxy also does DNS                                          | `nil`                                     |
| `agent.listenAddress`              | List of addresses to listen on, or "*" for all interfaces               | `nil`                                     |
| `agent.pod.requests.memory`        | Container memory requests in MiB                                        | `512`                                     |
| `agent.pod.requests.cpu`           | Container cpu requests in cpu cores                                     | `0.5`                                     |
| `agent.pod.limits.memory`          | Container memory limits in MiB                                          | `512`                                     |
| `agent.pod.limits.cpu`             | Container cpu limits in cpu cores                                       | `1.5`                                     |

### Agent

There is a [config map](templates/configmap.yaml) which you can edit to configure the agent. This configuration will be used for all instana agents on all nodes.

#### Notes

[1] - [https://docs.helm.sh/using_helm/#installing-helm](https://docs.helm.sh/using_helm/#installing-helm)

Most Kubernetes clusters have RBAC enabled, therefore, create a service account for helm to use, for example:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

```bash
$ kubectl create -f tiller-service-account.yaml
```
