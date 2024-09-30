
# Getting started: Deployment from a local machine

Please note the general [Getting Started documentation](https://gitlab.opencode.de/bmi/opendesk/deployment/opendesk/-/blob/main/docs/getting-started.md) by openDesk.

## Choose your components

Decide which [components](https://gitlab.opencode.de/bmi/opendesk/deployment/opendesk/-/blob/main/docs/components.md) you want to deploy. It is recommended to get started with the core applications and to enable more apps later. With all apps enabled, the deployment takes more than half an hour.
In the example configuration below the apps Collabora, Cryptpad, Element, Intercom, Jitsi, Nextcloud, openproject, OX App Suite, OX Connector, and XWiki are disabled for the initial rollout. Without these apps, the initial deployment takes about 10 minutes.

## Basic configuration

You can use the following example settings as a starting point for your `helmfile/environments/example-env/dev/values.yaml.gotmpl` file. Please see the [configuration](docs/configuration.md) chapter for more details.

```yaml
{{/*
SPDX-FileCopyrightText: 2024 Zentrum für Digitale Souveränität der Öffentlichen Verwaltung (ZenDiS) GmbH
SPDX-FileCopyrightText: 2023 Bundesministerium des Innern und für Heimat, PG ZenDiS "Projektgruppe für Aufbau ZenDiS"
SPDX-License-Identifier: Apache-2.0
*/}}

---
grafana:
  dashboards:
    annotations:
      "grafana-dashboard-folder": "openDesk: {{ env "NAMESPACE" | default "Main" }}"
    enabled: true

functional:
  authentication:
    twoFactor:
      # Define a list of groups to enable 2FA for.
      # Note: Removing a group from the list will not disable 2FA for the removed group.
      groups: []

certificate:
  issuerRef:
    name: "letsencrypt-prod"
  wildcard: true

ingress:
  ingressClassName: "nginx"

persistence:
  storageClassNames:
    RWX: "csi-cinder-sc-delete"
    RWO: "csi-cinder-sc-delete"

smtp:
  host: "host.example-relay.org"
  username: "some-account"
  port: 587

turn:
  server:
    host: "turn.example.org"
    port: "3478"
  tls:
    host: "turn.example.org"
    port: "5349"

cluster:
  service:
    type: "ClusterIP"
  api:
    domain: "api.example.org"
    port: 6443
  networking:
    ingressGatewayIP: "YOUR-PUBLIC-IP"

prometheus:
  serviceMonitors:
    enabled: true
  podMonitors:
    enabled: true
  prometheusRules:
    enabled: true

security:
  clusterPostfix:
    namespace: "swp-cross-instance-mail"

debug:
  enabled: true

databases:
  oxAppsuite:
    host: "mariadb"
    # DB name has to set or else "configdb" will used while "openxchange" is created.
    name: "openxchange"
    username: "root"

collabora:
  enabled: false

cryptpad:
  enabled: false

element:
  enabled: false

intercom:
  enabled: false

jitsi:
  enabled: false

nextcloud:
  enabled: false

openproject:
  enabled: false

oxAppsuite:
  enabled: false

oxConnector:
  enabled: false

xwiki:
  enabled: false

...

```

## Namespace

Create a namespace in your SCS cluster:

```bash
kubectl create namespace your-namespace
```

## Deploy an ingress resource inside the namespace

Create an ingress resource:

```bash
cat >ingress-resource.yaml <<EOF
---
apiVersion: "networking.k8s.io/v1"
kind: "Ingress"
metadata:
  name: "example.org"
  annotations:
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
spec:
  ingressClassName: "nginx"
  tls:
    - hosts:
      - "example.org"
      - "*.example.org"
      secretName: "opendesk-certificates-tls"
  rules:
    - host: "*.example.org"
      http:
        paths:
          - pathType: "Prefix"
            path: "/"
            backend:
              service:
                name: "ums-portal-frontend"
                port:
                  number: 443
EOF
```

Deploy the ingress resource inside your namespace:

```bash
kubectl apply -f ingress-resource.yaml -n your-namespace
```

## Deployment

Trigger the deployment:

```bash
OPENDESK_SMTPRELAY_PASSWORD="************" OPENDESK_MASTER_PASSWORD="************" DOMAIN="example.org" helmfile apply -e example-env -n your-namespace

```

## First login

Two accounts, an admin and a user account, are automatically created for you.

The default admin user is called `default.admin`, you can retrieve its password by running:

```bash
kubectl -n your-namespace get secret ums-nubus-credentials -o json | jq -r '.data.admin_password' | base64 -d
```

The default user is called `default.user`, you can retrieve its password by running:

```bash
kubectl -n your-namespace get secret ums-nubus-credentials -o json | jq -r '.data.user_password' | base64 -d
```

Open the portal in your browser, e.g. `https://portal.example.org` and log in.

## Deploy the desired apps

To add an app later you don't have to run `helmfile` in the top-level directory. You can `cd` into the app's directory underneath `helmfile/apps` an execute `helmfile` in there. The advantage is that this is faster.
