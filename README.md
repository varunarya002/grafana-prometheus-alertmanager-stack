# grafana-prometheus-alertmanager-stack
Setting up grafana, prometheus and alertmanager on kubernetes using helm

This project depends on 
[kube-prometheus-stack](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack)

## Prerequisites

- Kubernetes 1.16+
- Helm 3+

## Get Repo Info

```console
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```
_See [helm repo](https://helm.sh/docs/helm/helm_repo/) for command documentation._

## Install Chart

```console
# Helm
helm install --namespace monitoring sample -f alertmanager/values.yaml -f grafana/values.yaml -f prometheus/values.yaml  prometheus-community/kube-prometheus-stack 
```
#Rules Alertmanager
kubectl edit prometheusrules sample-kube-prometheus-st-alertmanager.rules -n monitoring
copy rules from prometheus/rules.yaml
kubectl apply -f alertmanager-secret-k8s.yaml
_See [configuration](#configuration) below._

_See [helm install](https://helm.sh/docs/helm/helm_install/) for command documentation._

## Dependencies

By default this chart installs additional, dependent charts:

- [prometheus-community/kube-state-metrics](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-state-metrics)
- [prometheus-community/prometheus-node-exporter](https://github.com/prometheus-community/helm-charts/tree/main/charts/prometheus-node-exporter)
- [grafana/grafana](https://github.com/grafana/helm-charts/tree/main/charts/grafana)

To disable dependencies during installation, see [multiple releases](#multiple-releases) below.

_See [helm dependency](https://helm.sh/docs/helm/helm_dependency/) for command documentation._

## Upgrading Chart

```console
# Helm
helm upgrade  --namespace monitoring sample -f alertmanager/values.yaml -f grafana/values.yaml -f prometheus/values.yaml  prometheus-community/kube-prometheus-stack
```

## Configuring alertmanager
```console
kubectl apply -f secrets/alert-config.yaml
```
## Promethus alert rules
```
kubectl apply -f rna-kube-prometheus-stack/prometheus/rules.yaml
```

## Uninstall Chart

```console
# Helm
helm uninstall sample -n monitoring
```

This removes all the Kubernetes components associated with the chart and deletes the release.

_See [helm uninstall](https://helm.sh/docs/helm/helm_uninstall/) for command documentation._

CRDs created by this chart are not removed by default and should be manually cleaned up:

```console
kubectl delete crd alertmanagerconfigs.monitoring.coreos.com -n monitoring
kubectl delete crd alertmanagers.monitoring.coreos.com -n monitoring
kubectl delete crd podmonitors.monitoring.coreos.com -n monitoring
kubectl delete crd probes.monitoring.coreos.com -n monitoring
kubectl delete crd prometheuses.monitoring.coreos.com -n monitoring
kubectl delete crd prometheusrules.monitoring.coreos.com -n monitoring
kubectl delete crd servicemonitors.monitoring.coreos.com -n monitoring
kubectl delete crd thanosrulers.monitoring.coreos.com -n monitoring
```
