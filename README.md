# Digit Helm Deployment Common Chart

To create a kubernetes helm chart for deployments, we need the key attributes [Replicas, image, init containers, etc..] 
and its values to be passed for each service mandatorily.  
The common templates has the required files to generate deployment manifest, service with common variables and values based on user input.

## Requirements

The values template file [values.yaml](https://github.com/egovernments/eGov-infraOps/blob/helm/helm/charts/common/values.yaml) has the common attributes and its values required for all manifest files.

The service template file [_service.yaml](https://github.com/egovernments/eGov-infraOps/blob/helm/helm/charts/common/templates/_service.yaml) has the attributes and values for generating service manifest.

The ingress template file [_ingress.yaml](https://github.com/egovernments/eGov-infraOps/blob/helm/helm/charts/common/templates/_ingress.yaml) has the attributes and values for generating ingress manifest.

The deployment template file [_deployment.yaml](https://github.com/egovernments/eGov-infraOps/blob/helm/helm/charts/common/templates/_deployment.yaml) has the attributes and values for generating deployment manifest.

## Values template

Parameter | Description | Default
--- | --- | ---
`namespace` | Default namespace for the service | `egov`
`replicas` | Number of Pods to be created | `1`
`httpPort` | Default port number for the service | `8080`
`appType` | Default application type, java-spring if nothing specified | ` `
`ingress.enabled` | To add ingress controller for the service  | `false`
`zuul` | To add zuul configuration for the service | `false` 
`kubernetes.io/ingress.class` | Target of the ingress Controller | `nginx`
`nginx.ingress.kubernetes.io/use-regex` | Indicates if the paths defined on an Ingress use regular expressions | `"true"`
`nginx.ingress.kubernetes.io/app-root` | Defines the Application Root that the Controller must redirect if it's in '/' context | `"true"`
`waf.enabled` | Enable Web Application Firewall for the service | `true`
`nginx.ingress.kubernetes.io/lua-resty-waf` | Enable the WAF for the nginx ingress  | `active`
`nginx.ingress.kubernetes.io/lua-resty-waf-debug` | Enable the WAF for the nginx ingress in debug mode | `true`
`nginx.ingress.kubernetes.io/lua-resty-waf-allow-unknown-content-types` | Default allowed contents were "text/html", "text/json", "application/json". Set the value to True, to enable WAF for all contents type | `true`
`nginx.ingress.kubernetes.io/lua-resty-waf-score-threshold` | Sets the threshold for anomaly scoring. When the threshold is reached, WAF will deny the request | `10`
`nginx.ingress.kubernetes.io/lua-resty-waf-process-multipart-body` | Processing of multipart/form-data request bodies | `false`
`image.pullPolicy` |  To pull a Docker image from Docker repository, By default skip pulling an image if it already exists | `IfNotPresent`
`image.tag` | Docker image version to pull from the Docker repository | `latest`
`affinity.preferSpreadAcrossAZ` | To select multiple availability zones to schedule the pods | `true`
`initContainers.dbMigration.enabled` | To add DB migration as part of deployment | `false`
`initContainers.dbMigration.image.pullPolicy` | Pulls the DB migration docker images from Docker repository | `IfNotPresent`
`initContainers.dbMigration.image.tag` | Docker image version to pull from the Docker repository | `latest`
`initContainers.dbMigration.env.name` | Environment variables to be overridden for the Flyway migration [DB_URL, FLYWAY_USER, FLYWAY_PASSWORD, etc..] | ` `
`initContainers.dbMigration.env.value` | Environment variable values to be overridden for the Flyway migration [DB_URL, FLYWAY_USER, FLYWAY_PASSWORD, etc..] | ` `
`gitSync.enabled` | To pull the latest code from the repository to the desired location | `false`
`gitSync.image.repository` | Docker image location for git-sync | `k8s.gcr.io/git-sync`
`gitSync.image.tag` | Docker image tag for git-sync | `v3.1.1`
`gitSync.image.pullPolicy` |  To pull a Docker image from Docker repository, By default skip pulling an image if it already exists | `IfNotPresent`
`gitSync.env.name` | Environment variables to be overridden for Git repository, Branch, Location, etc.. | ` `
`gitSync.env.value` | Environment variable Values to be overridden for Git repository, Branch, Location, etc.. | ` `
`healthChecks.enabled` | To enable/disable healthchecks [Liveness probes and Readiness probes] for a pod | `false`
`healthChecks.livenessProbe.httpGet.path` | Context path of the service to check the liveness of a pod | `{{ .Values.healthChecks.livenessProbePath }}`
`healthChecks.livenessProbe.httpGet.port` | Port number of the service to check the liveness of a pod | `{{ .Values.httpPort }}`
`healthChecks.livenessProbe.initialDelaySeconds` | Number of seconds after the container has started before liveness probe is initated | `60`
`healthChecks.livenessProbe.timeoutSeconds` | Number of seconds after which the liveness probe times out | `3`
`healthChecks.livenessProbe.periodSeconds` | How often (in seconds) to perform the liveness probe |`60`
`healthChecks.livenessProbe.successThreshold` | Minimum consecutive successes for the liveness probe to be considered successful after having failed. | `1`
`healthChecks.livenessProbe.failureThreshold` | When a Pod starts and the liveness probe fails, Kubernetes will try failureThreshold times before restarting pod | `5`
`healthChecks.readinessProbe.httpGet.path` | Context path of the service to check the readiness of a pod | `{{ .Values.healthChecks.readinessProbePath }}`
`healthChecks.readinessProbe.httpGet.port` | Port number of the service to check the readiness of a pod | `{{ .Values.httpPort }}`
`healthChecks.readinessProbe.initialDelaySeconds` | Number of seconds after the container has started before readiness probe is initated | `60`
`healthChecks.readinessProbe.timeoutSeconds` | Number of seconds after which the readiness probe times out | `3`
`healthChecks.readinessProbe.periodSeconds` | How often (in seconds) to perform the readiness probe |`30`
`healthChecks.readinessProbe.successThreshold` | Minimum consecutive successes for the probe to be considered successful after having failed. | `1`
`healthChecks.readinessProbe.failureThreshold` | When a Pod starts and the readiness probe fails, Kubernetes will try failureThreshold times before restarting pod | `5`
`lifecycle.preStop.exec.command` | Executes the command in the pod before stopping | `- sh`<br/> `- -c` <br/> `- "sleep 10"`
`memory_limits` | To set the memory limit for the pod to 512 MB | `512Mi`
`resources.requests.memory` | Requested memory for the pod | `{{ .Values.memory_limits \| quote }}`
`resources.limits.memory` | Memory limit for the pod on usage | `{{ .Values.memory_limits \| quote }}`
`extraEnv.java` | Addtional environment variables for Java application.  | `Environment variables from the reference files(dev.yml,qa.yml) [environment manifest file]`
`jaeger` | Jaeger API tracing environment variables to send traces to Jaeger Agent | `For Eg:` <br/> `jaeger: \|` <br/> &nbsp;  &nbsp; &nbsp;  &nbsp; `- name: JAEGER_AGENT_PORT` <br/> &nbsp;  &nbsp; &nbsp;  &nbsp; &nbsp;  &nbsp; `port: 6831` <br/> see [values.yaml](https://github.com/egovernments/eGov-infraOps/blob/helm/helm/charts/common/values.yaml)
`extraVolumes` | To add additional volumes and mounts 
```yaml
## Additional init containers
extraInitContainers: |

## Additional sidecar containers
extraContainers: |

## Add additional volumes and mounts, e. g. for custom themes
extraVolumes: |
extraVolumeMounts: |

additionalLabels: {}

podSecurityContext: {}
  # fsGroup: 2000

securityContext: {}
  # capabilities:
  #   drop:
  #   - ALL
  # readOnlyRootFilesystem: true
  # runAsNonRoot: true
  # runAsUser: 1000           
```
