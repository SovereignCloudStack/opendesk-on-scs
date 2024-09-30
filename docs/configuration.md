# Configuration

openDesk has many configuration options. You can view them and their default values in your copy of openDesk:

## Basic components

 For the configuration of the basic components please have a look at the `.yaml` files in `helmfile/environments/default`.

 ```bash
 $ ls helmfile/environments/default
cache.yaml
certificate.yaml
charts.yaml
cluster.yaml
database.yaml
debug.yaml
enterprise.yaml
functional.yaml
global.generated.yaml
global.gotmpl
_helper.yaml
images.yaml
ingress.yaml
monitoring.yaml
objectstores.yaml
opendesk_main.gotmpl
persistence.yaml
replicas.yaml
repositories.yaml
resources.yaml
secrets.gotmpl
security.yaml
selinux.yaml
smtp.gotmpl
theme.gotmpl
turn.gotmpl
```

To overwrite a specific default setting, copy the snippet into you own `values.yaml.gotmpl` file and change the values to your need.

## Apps

The settings of the apps reside underneath `helmfile/apps` (just like their helmfiles). As you see, the different apps have different numbers of values files. Review them carefully and edit your own `values.yaml.gotmpl` to overwrite any default setting.

```bash
$ tree helmfile/apps/
helmfile/apps/
├── collabora
│   ├── helmfile-child.yaml.gotmpl
│   ├── helmfile.yaml.gotmpl
│   └── values.yaml.gotmpl
├── cryptpad
│   ├── helmfile-child.yaml.gotmpl
│   ├── helmfile.yaml.gotmpl
│   └── values.yaml.gotmpl
├── element
│   ├── helmfile-child.yaml.gotmpl
│   ├── helmfile.yaml.gotmpl
│   ├── values-element.yaml.gotmpl
│   ├── values-synapse-web.yaml.gotmpl
│   ├── values-synapse.yaml.gotmpl
│   └── values-well-known.yaml.gotmpl
├── intercom-service
│   ├── helmfile-child.yaml.gotmpl
│   ├── helmfile.yaml.gotmpl
│   └── values.yaml.gotmpl
├── jitsi
│   ├── helmfile-child.yaml.gotmpl
│   ├── helmfile.yaml.gotmpl
│   └── values-jitsi.yaml.gotmpl
├── migrations-post
│   ├── helmfile-child.yaml.gotmpl
│   ├── helmfile.yaml.gotmpl
│   └── values.yaml.gotmpl
├── migrations-pre
│   ├── helmfile-child.yaml.gotmpl
│   ├── helmfile.yaml.gotmpl
│   └── values.yaml.gotmpl
├── nextcloud
│   ├── helmfile-child.yaml.gotmpl
│   ├── helmfile.yaml.gotmpl
│   ├── values-nextcloud-mgmt.yaml.gotmpl
│   └── values-nextcloud.yaml.gotmpl
├── nubus
│   ├── helmfile-child.yaml.gotmpl
│   ├── helmfile.yaml.gotmpl
│   ├── values-nubus.yaml.gotmpl
│   ├── values-opendesk-customization.yaml.gotmpl
│   ├── values-opendesk-images.yaml.gotmpl
│   └── values-opendesk-keycloak-bootstrap.yaml.gotmpl
├── openproject
│   ├── helmfile-child.yaml.gotmpl
│   ├── helmfile.yaml.gotmpl
│   └── values.yaml.gotmpl
├── openproject-bootstrap
│   ├── helmfile-child.yaml.gotmpl
│   ├── helmfile.yaml.gotmpl
│   └── values.yaml.gotmpl
├── open-xchange
│   ├── helmfile-child.yaml.gotmpl
│   ├── helmfile.yaml.gotmpl
│   ├── values-dovecot.yaml.gotmpl
│   ├── values-openxchange-bootstrap.yaml.gotmpl
│   ├── values-openxchange-enterprise-contact-picker.yaml.gotmpl
│   └── values-openxchange.yaml.gotmpl
├── provisioning
│   ├── helmfile-child.yaml.gotmpl
│   ├── helmfile.yaml.gotmpl
│   └── values-oxconnector.yaml.gotmpl
├── services
│   ├── helmfile-child.yaml.gotmpl
│   ├── helmfile.yaml.gotmpl
│   ├── values-certificates.yaml.gotmpl
│   ├── values-clamav-distributed.yaml.gotmpl
│   ├── values-clamav-simple.yaml.gotmpl
│   ├── values-dkimpy.yaml.gotmpl
│   ├── values-home.yaml.gotmpl
│   ├── values-mariadb.yaml.gotmpl
│   ├── values-memcached.yaml.gotmpl
│   ├── values-minio.yaml.gotmpl
│   ├── values-otterize.yaml.gotmpl
│   ├── values-postfix.yaml.gotmpl
│   ├── values-postgresql.yaml.gotmpl
│   └── values-redis.yaml.gotmpl
└── xwiki
    ├── helmfile-child.yaml.gotmpl
    ├── helmfile.yaml.gotmpl
    └── values.yaml.gotmpl


```
