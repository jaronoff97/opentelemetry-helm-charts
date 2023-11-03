# OpenTelemetry Operator Helm Chart

The Helm chart installs the [OpenTelemetry Operator](https://github.com/open-telemetry/opentelemetry-operator) in a Kubernetes cluster along with an instance of a collector for Kubernetes observability.
The OpenTelemetry Operator is an implementation of a [Kubernetes Operator](https://www.openshift.com/learn/topics/operators).

## Prerequisites

- Kubernetes 1.24+ is required for OpenTelemetry Operator installation
- Helm 3.9+

### TLS Certificate Requirement

In Kubernetes, in order for the API server to communicate with the webhook component, the webhook requires a TLS
certificate that the API server is configured to trust. There are a few different ways you can use to generate/configure the required TLS certificate.

  - The easiest and default method is to install the [cert-manager](https://cert-manager.io/docs/) and set `admissionWebhooks.certManager.create` to `true`.
    In this way, cert-manager will generate a self-signed certificate. _See [cert-manager installation](https://cert-manager.io/docs/installation/kubernetes/) for more details._
  - You can provide your own Issuer by configuring the `admissionWebhooks.certManager.issuerRef` value. You will need
    to specify the `kind` (Issuer or ClusterIssuer) and the `name`. Note that this method also requires the installation of cert-manager.
  - You can use an automatically generated self-signed certificate by setting `admissionWebhooks.certManager.enabled` to `false` and `admissionWebhooks.autoGenerateCert` to `true`. Helm will create a self-signed cert and a secret for you.
  - You can use your own generated self-signed certificate by setting both `admissionWebhooks.certManager.enabled` and `admissionWebhooks.autoGenerateCert` to `false`. You should provide the necessary values to `admissionWebhooks.cert_file`, `admissionWebhooks.key_file`, and `admissionWebhooks.ca_file`.
  - You can sideload custom webhooks and certificate by disabling `.Values.admissionWebhooks.create` and `admissionWebhooks.certManager.enabled` while setting your custom cert secret name in `admissionWebhooks.secretName`
  - You can disable webhooks altogether by disabling `.Values.admissionWebhooks.create` and setting env var to `ENABLE_WEBHOOKS: "false"`

## Add Repository

```console
$ helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
$ helm repo update
```

_See [helm repo](https://helm.sh/docs/helm/helm_repo/) for command documentation._

## Install Chart

```console
$ helm install \
  kube-otel-stack open-telemetry/kube-otel-stack
```

If you created a custom namespace, like in the TLS Certificate Requirement section above, you will need to specify the namespace with the `--namespace` helm option:

```console
$ helm install --namespace opentelemetry \
  kube-otel-stack open-telemetry/kube-otel-stack
```

If you wish for helm to create an automatically generated self-signed certificate, make sure to set the appropriate values when installing the chart:

```console
$ helm install  --set admissionWebhooks.certManager.enabled=false --set admissionWebhooks.certManager.autoGenerateCert=true \
  kube-otel-stack open-telemetry/kube-otel-stack
```

_See [helm install](https://helm.sh/docs/helm/helm_install/) for command documentation._

## Uninstall Chart

The following command uninstalls the chart whose release name is my-kube-otel-stack.

```console
$ helm uninstall kube-otel-stack
```

_See [helm uninstall](https://helm.sh/docs/helm/helm_uninstall/) for command documentation._

This will remove all the Kubernetes components associated with the chart and deletes the release.

The OpenTelemetry Collector CRD created by this chart won't be removed by default and should be manually deleted:

```console
$ kubectl delete crd opentelemetrycollectors.opentelemetry.io
```

## Upgrade Chart

```console
$ helm upgrade my-kube-otel-stack open-telemetry/kube-otel-stack
```

Please note that by default, the chart will be upgraded to the latest version. If you want to upgrade to a specific version,
use `--version` flag.

With Helm v3.0, CRDs created by this chart are not updated by default and should be manually updated.
Consult also the [Helm Documentation on CRDs](https://helm.sh/docs/chart_best_practices/custom_resource_definitions).

_See [helm upgrade](https://helm.sh/docs/helm/helm_upgrade/) for command documentation._

## Configuration

The following command will show all the configurable options with detailed comments.

```console
$ helm show values open-telemetry/kube-otel-stack
```
