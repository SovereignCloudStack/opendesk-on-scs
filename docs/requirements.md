# Requirements

You can find the general [requirements](https://gitlab.opencode.de/bmi/opendesk/deployment/opendesk/-/blob/main/docs/requirements.md) in the openDesk documentation. Please study them before beginning. This howto only touches on requirements concerning the rollout on an SCS cluster.

## Preparation of your local environment

### Command line tools

You will need the following command line tools on your machine:

- [kubectl](https://kubernetes.io/de/docs/tasks/tools/install-kubectl/)
- [helm](https://helm.sh/)
- [helmfile](https://helmfile.readthedocs.io/en/latest/)
- [helmDiff](https://github.com/databus23/helm-diff)

If you run into any problems, please check the openDesk [requirements](https://gitlab.opencode.de/bmi/opendesk/deployment/opendesk/-/blob/main/docs/requirements.md) for the required software versions of these tools.

### KUBECONFIG

Store the cluster access information for your SCS cluster in `~/.kube/config`. Test the access to the cluster on your command line with a `kubectl` command, e.g. `kubectl get nodes`.

## Preparation of the nameserver

openDesk consists of a number of resources with different virtual host names and subdomains. The proposed procedure deploys openDesk on a subdomain that matches the name of the namespace you choose. You will thus get URLs like `portal.dev.example.org` or `wiki.prod.example.org`.

Therefore it is recommended to create a wildcard A record for the domain in your nameserver (e.g. `*.example.org`) and point it to your cluster's public IP address.

It is of course possible to create individual A records for all resources.

The choice of your nameserver has an impact: It is recommended to use a nameserver that gives you API access: In this howto, TLS certificates are fetched from the certification authority [Letsencrypt](https://letsencrypt.org/). Because of the number of different services, a wildcard certificate comes in handy. Note that you can only request wildcard certificates from Letsencrypt via the [dns-01 challenge](https://letsencrypt.org/docs/challenge-types/). To do so you need a nameserver with API access. If your nameserver does not support this, you can delegate the zone to a different nameserver and fetch an API access token from there.

## Preparation of the Kubernetes cluster

### Install cert-manager

Install cert-manager in your cluster. It is required to include the custom resource definitions (`--set installCRDs=true`).

```bash
helm repo add jetstack https://charts.jetstack.io --force-update
helm repo update
helm upgrade -i cert-manager jetstack/cert-manager --namespace cert-manager --create-namespace --version v1.14.3 --set installCRDs=true
```

### Deploy a certificate issuer

The openDesk helmfiles are based on the assumption that you - as the cluster admin - take care of a certificate issuer that can just be used then. For Letsencrypt's ACME dns-01 challenge add a [cluster issuer](https://cert-manager.io/docs/concepts/issuer/) by defining it in a file, e.g. `clusterIssuer.yaml` and applying it to the cluster. Check the documentation for your respective DNS provider to put together the relevant configuration details.

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: my-dns-provider
data:
  secret-access-key: my-generated-access-token
---
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    privateKeySecretRef:
      name: example-account-key
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    solvers:
      - dns01:
          my-dns-provider:
            region: eu-west-1
            accessKeyID: my-key-id
            secretAccessKeySecretRef:
              name: my-dns-provider
              key: secret-access-key
        selector:
          dnsNames:
            - '*.example.org'
---
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    privateKeySecretRef:
      name: example-account-key
    server: https://acme-v02.api.letsencrypt.org/directory
    solvers:
      - dns01:
          my-dns-provider:
            region: eu-west-1
            accessKeyID: my-key-id
            secretAccessKeySecretRef:
              name: my-dns-provider
              key: secret-access-key
        selector:
          dnsNames:
            - '*.example.org'
```

### Ingress Controller

[Ingress-nginx](https://github.com/kubernetes/ingress-nginx) is the only tested ingress controller. The installation must support snippet annotations (`allowSnippetAnnotations=true`). You can install and update it as shown below. Note that for production environments it is recommended to reconsider the HSTS setting given here.

```bash
helm upgrade --install ingress-nginx ingress-nginx --repo https://kubernetes.github.io/ingress-nginx  --namespace ingress-nginx --create-namespace --set controller.allowSnippetAnnotations=true --set controller.config.hsts=false
```

### Prometheus

Prometheus is a requirement:

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm upgrade -i prometheus prometheus-community/kube-prometheus-stack --namespace prometheus --create-namespace
```

### Storage

Clarify with your SCS cluster operator what kind of storage your K8s cluster provides. Your configuration will need the according information. You can query the available storage classes like so:

```yaml
kubectl get storageclasses.storage.k8s.io
```

You can hand over the storage class in your `values.yaml.gotmpl` (see [Getting Started](getting_started.md)).

### Optional: Credentials for a mail relay

If you want your openDesk deployment to be able to send outgoing emails, have the hostname and credentials of an SMTP relay at hand.
