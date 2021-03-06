# **MagicMirror²**

is an open source modular smart mirror platform. For more info visit the [project website](https://github.com/MichMich/MagicMirror).

# Introduction

This helm chart bootstraps a MagicMirror instance.

> 👉 The project is very young so don't expect everything works already ...

# Restrictions

A normal MagicMirror setup uses a raspberry pi as hardware. If you want to run the application without output on the screen of the pi, you can run it [server only](https://docs.magicmirror.builders/getting-started/installation.html#usage) and open MagicMirror with a web browser.

As the most k8s clusters are not running on raspberry pi's, here the `server only` setup of MagicMirror is used.

> ❌ This implies that no modules can be used which need raspberry pi specific hardware (e.g. GPIO).

# Prerequisites

* Running Kubernetes Cluster
* Helm

# Installing the chart

To install the chart:

```bash
$ git clone https://gitlab.com/khassel/magicmirror-helm.git
$ cd magicmirror-helm
$ helm upgrade magicmirror -i .
```

The above command deploys MagicMirror on the Kubernetes cluster in a default configuration (with default modules).

# Uninstalling the chart

To uninstall/delete the deployment:

```bash
$ helm ls
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
magicmirror     default         3               2020-06-03 21:27:57.417308079 +0000 UTC deployed        magicmirror-1.0.0       1.0

$ helm delete magicmirror
```

# Configuration

The following table lists the configurable parameters of the MagicMirror chart and their default values. Some default values which are present in every deployment are omitted.

| Parameter                             | Description                                                                  | Default                                        |
| ------------------------------------- | ---------------------------------------------------------------------------- | ---------------------------------------------- |
| `image`                               | image with tag                                                               | `karsten13/magicmirror:alpine`                 |
| `imagePullPolicy`                     | Image pull policy                                                            | `Always`                                       |
| `ingress.enabled`                     | Flag for enabling ingress                                                    | false                                          |
| `ingress.type`                        | traefik or nginx                                                             | `traefik`                                      |
| `ingress.hostname`                    | hostname running MagicMirror                                                 |                                                |
| `ingress.path`                        | subPath running MagicMirror                                                  | `/mm`                                          |
| `ingress.tls`                         | Flag for enabling tls                                                        | false                                          |
| `service.type`                        | service type                                                                 | `ClusterIP`                                    |
| `service.port`                        | service port                                                                 | `8080`                                         |
| `env`                                 | list of environment variables                                                | `[]`                                           |
| `modules.install`                     | list of (foreign) modules to install                                         | `[]`                                           |
| `config`                              | the config part of MagicMirror, part of `config.js` file                     | content of default `config.js` file            |
| `css`                                 | custom css, normally defined in `custom.css` file                            | empty                                          |
| `persistence.enabled`                 | enables persistent volume for modules                                        | false                                          |

For overriding variables see: [Customizing the chart](https://helm.sh/docs/intro/using_helm/#customizing-the-chart-before-installing)

The current setup uses [traefik](https://github.com/containous/traefik-helm-chart) as ingress, for using nginx-ingress you have to change `ingress.type`.
You can also run without ingress, then you have to disable ingress (`ingress.enabled=false`) and setup the service as `LoadBalancer`, see example in `values.yaml`.
With the default setup your MagicMirror ist running under `http://<your-ip-address-or-hostname>/mm/`.

# MagicMirror configuration: Config, custom CSS and Modules

The configuration normally found in a `config.js` is done in the `values.yaml` in the `config` section. You find more information [here](https://docs.magicmirror.builders/getting-started/configuration.html#general).

The css configuration normally found in a `custom.css` file is done in the `values.yaml` in the `css` section. This is used to override your modules' appearance. CSS basics are documented
[here](https://forum.magicmirror.builders/topic/6808/css-101-getting-started-with-css-and-understanding-how-css-works), among many other places.

Foreign modules are installed by editing the `modules.install` section in the `values.yaml`, the default modules are described [here](https://docs.magicmirror.builders/modules/introduction.html).

It is a good idea to not edit the `values.yaml` but bring your own yaml file with your changes only as in the following example.

The [`example.yaml`](./example.yaml) has a default config without the weather modules and 2 foreign modules.

For starting with this example use `helm upgrade magicmirror -i -f example.yaml .`.

# Persistence for Modules

The above approach of installing modules does the install on every start, so with every start a `git clone` and `npm install` is run for every module. So you have always the newest versions of the modules, but the start needs additional time.
If you want to maintain the modules yourself on a persistent volume, you can use the `persistence` section in the `values.yaml`.