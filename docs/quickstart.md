# Quickstart

To get started quickly, you can use the [wrapper script](../helper/deploy-openDesk) that does parts of the work described in [Getting started](getting_started.md). Please see that page for details nevertheless. # noqa: [relative-link-path]

- [Get a Sovereign Cloud Stack cluster](https://docs.scs.community/docs/faq/).
- Go through the openDesk [requirements](requirements.md).
- Clone the [openDesk repository](https://gitlab.opencode.de/bmi/opendesk/deployment/opendesk):

```bash
git clone https://gitlab.opencode.de/bmi/opendesk/deployment/opendesk
cd opendesk
```

- Create a folder for the settings of your environment: `mkdir -p helmfile/environments/example-env/dev/`
- Add one or more files with your configuration, for example `values.yaml.gotmpl` (see [Getting Started](getting_started.md) for an example).
- Reference the environment you want to deploy in `helmfile.yaml`:

```bash
cat helmfile.yaml  <<__EOF__
example-env:
  values:
    - "helmfile/environments/example-env/dev/*.yaml.gotmpl"
__EOF__
```

- Download the [wrapper script](../helper/deploy-openDesk). Make it executable.
- Edit the variables at the top of the script to fit your needs.
- Run it.
