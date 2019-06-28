# spaces

[spaces](https://github.com/qlik-trial/spaces) is the responsible for managing spaces resource (and `/v1/spaces`).

## Introduction

This chart bootstraps a spaces deployment on a [Kubernetes](http://kubernetes.io) cluster using the [Helm](https://helm.sh) package manager.

## Installing the Chart

To install the chart with the release name `my-release`:

```console
helm install --name my-release qlik/spaces
```

The command deploys spaces on the Kubernetes cluster in the default configuration. The [configuration](#configuration) section lists the parameters that can be configured during installation.

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```console
helm delete my-release
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Configuration

The following tables lists the configurable parameters of the spaces chart and their default values.

| Parameter                         | Description                                                                                               | Default                                                                                |
| --------------------------------- | --------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| `image.registry`                  | Image registry                                                                                            | `qliktech-docker.jfrog.io`                                                             |
| `image.repository`                | Image repo                                                                                                | `spaces`                                                                               |
| `image.tag`                       | Image version                                                                                             | `1.2.1`                                                                                |
| `image.pullPolicy`                | Image pull policy                                                                                         | `Always` if `image.tag` is `latest`, else `IfNotPresent`                               |
| `imagePullSecrets`                | A list of secrets names for accessing private image registries                                            | `artifactory-docker-secret`                                                            |
| `replicaCount`                    | Number of spaces replicas                                                                                 | `1`                                                                                    |
| `terminationGracePeriodSeconds`   | Number of seconds to wait during pod termination after sending SIGTERM until SIGKILL.                     | `30`                                                                                   |
| `service.type`                    | Service type                                                                                              | `ClusterIP`                                                                            |
| `service.port`                    | Spaces service listen port                                                                                | `6080`                                                                                 |
| `ingress.enabled`                 | Enable spaces ingress                                                                                     | `true`                                                                                 |
| `ingress.class`                   | The `kubernetes.io/ingress.class` to use                                                                  | `nginx`                                                                                |
| `ingress.authURL`                 | The URL to use for nginx's `auth-url` configuration to authenticate `/api` requests                       | `http://{.Release.Name}-edge-auth.{.Release.Namespace}.svc.cluster.local:8080/v1/auth` |
| `ingress.annotations`             | Ingress additional annotations                                                                            | `[]`                                                                                   |
| `metrics.prometheus.enabled`      | Enable prometheus metrics                                                                                 | `true`                                                                                 |
| `config.logLevel`                 | Sets service log level                                                                                    | `info`                                                                                 |
| `config.rollbar.enabled`          | Enable Rollbar alerts to track server errors                                                              | `false`                                                                                |
| `config.rollbar.token`            | The Rollbar token                                                                                         |                                                                                        |
| `config.auth.enabled`             | Toggle JWT validation using retrieved keys from the configured JWKS endpoint.                             | `true`                                                                                 |
| `config.auth.jwksURI`             | The endpoint to retrieve the JWKS                                                                         | `http://{.Release.Name}-keys:8080/v1/keys/qlik.api.internal`                           |
| `config.auth.jwtAud`              | The expected `audience` value within the JWT claims                                                       | `qlik.api.internal`                                                                    |
| `config.auth.jwtIss`              | The expected `issuer` value within the JWT claims                                                         | `qlik.api.internal`                                                                    |
| `config.token.privateKey`         | The private key that corresponds to the JWKS in the authentication service                                | See [values](./values.yaml)                                                            |
| `config.token.kid`                | The key ID that corresponds to the JWKS in the authentication service                                     | `j3MbBtOiEib3tLcVbvscwehV3K0WAZVFBp3ysfsdksg`                                          |
| `config.token.uri`                | The URI to the authentication service to get an internal token                                            | `http://{{ .Release.Name }}-edge-auth:8080/v1/internal-tokens`                         |
| `config.messaging.enabled`        | Toggle for enabling messaging functionalities                                                             | `true`                                                                                 |
| `config.messaging.podLabel.key`   | Pod label key to pass network policy requirements to connect to messaging                                 | `{release.Name}-nats-client`                                                           |
| `config.messaging.podLabel.value` | Pod label value to pass network policy requirements to connect to messaging                               | `true`                                                                                 |
| `config.messaging.nats.addr`      | NATS server address                                                                                       | `nats://{{ .Release.Name }}-nats-client:4222`                                          |
| `config.messaging.stan.clusterID` | NATS Streaming cluster ID                                                                                 | `{{ .Release.Name }}-nats-streaming-cluster`                                           |
| `config.accessControl.enabled`    | Toggle access control enforcement using retrieved queries and evaluations from `policy-decision-service`. | `true`                                                                                 |
| `config.accessControl.pdsURI`     | The endpoint to reach the `policy-decision-service`                                                       | `http://{.Release.Name}-policy-decisions:5080`                                         |
| `mongodb.enabled`                 | Enable Mongodb as a chart dependency for local development, testing and CI purposes                       | `false`                                                                                |
| `mongodb.uri`                     | If the mongodb chart dependency isn't used, specify the URI path to mongo                                 |                                                                                        |
| `mongodb.uriSecretName`           | Name of secret to mount for mongo URI. The secret must have the `mongodb-uri` key                         | `{release.Name}-mongoconfig`                                                           |
| `hpa.enabled`                     | Toggle horizontal pod autoscaler.                                                                         | `false`                                                                                |

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`.

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart. For example,

```console
helm install --name my-release -f values.yaml qlik/spaces
```

> **Tip**: You can use the default [values.yaml](values.yaml)
