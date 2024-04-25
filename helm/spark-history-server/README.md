# spark-history-server

A Helm chart for the Spark-History-Server

## Introduction

This chart bootstraps a [Spark History Server](https://github.com/OKDP/) deployment using the [Helm](https://helm.sh) package manager.

## Prerequisites

- Helm >= 3
- Kubernetes >= 1.19

## Installing the chart

```shell

$ git clone repoUrl (TODO: switch to helm add)

$ helm install history-server .
```

This will create a release of `spark-history-server` in the default namespace. To install in a different one:

```shell
$ helm install history-server . -n spark
```

Note that `helm` will fail to install if the namespace doesn't exist. Either create the namespace beforehand or pass the `--create-namespace` flag to the `helm install` command.

## Uninstalling the chart

To uninstall `history-server`:

```shell
$ helm uninstall history-server -n spark
```

The command removes all the Kubernetes components associated with the chart and deletes the release.
## Configuration examples

### Using an S3-Compatible System for Log Storage

This example guides you through setting up the Spark History Server to store logs in an S3-compatible storage system.

#### Authentication using accessKey and secretKey

##### 1. Create a Kubernetes Secret for S3 Credentials

This secret will store the access key and secret key required to access your S3 bucket.

    kubectl create secret generic s3-secret \
      --from-literal=accessKey='XXXXX' \
      --from-literal=secretKey='XXXXX' \
      -n spark

Replace your-access-key and your-secret-key with your actual S3 credentials.

##### 2. Prepare Configuration Values (my-values.yaml):

    config:
      spark.hadoop.fs.s3a.endpoint: https://<S3 endpoint>
      spark.hadoop.fs.s3a.connection.ssl.enabled: true
      spark.history.fs.logDirectory: s3a://<your-bucket-name>/eventLogs
      spark.hadoop.fs.s3a.impl: org.apache.hadoop.fs.s3a.S3AFileSystem
      spark.hadoop.fs.s3a.path.style.access: true

    extraEnvs:
      - name: AWS_ACCESS_KEY_ID
        valueFrom:
          secretKeyRef:
            key: accessKey
            name: s3-secret
      - name: AWS_SECRET_ACCESS_KEY
        valueFrom:
          secretKeyRef:
            key: secretKey
            name: s3-secret
      # Disable Certificate Checking
      - name: SPARK_HISTORY_OPTS
        value: "-Dcom.amazonaws.sdk.disableCertChecking=true"

##### 3. Deploy the Helm Chart

    helm install history-server . --values my-values.yaml -n spark

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| affinity | object | `{}` |  |
| commonAnnotations | object | `{}` |  |
| commonLabels | object | `{}` |  |
| config."spark.history.custom.executor.log.url" | string | `""` |  |
| config."spark.history.custom.executor.log.url.applyIncompleteApplication" | bool | `true` |  |
| config."spark.history.fs.cleaner.enabled" | bool | `false` |  |
| config."spark.history.fs.cleaner.interval" | string | `"1d"` |  |
| config."spark.history.fs.cleaner.maxAge" | string | `"7d"` |  |
| config."spark.history.fs.cleaner.maxNum" | int | `2147483647` |  |
| config."spark.history.fs.driverlog.cleaner.enabled" | string | `"spark.history.fs.cleaner.enabled"` |  |
| config."spark.history.fs.driverlog.cleaner.interval" | string | `"spark.history.fs.cleaner.interval"` |  |
| config."spark.history.fs.driverlog.cleaner.maxAge" | string | `"spark.history.fs.cleaner.maxAge"` |  |
| config."spark.history.fs.endEventReparseChunkSize" | string | `"1m"` |  |
| config."spark.history.fs.eventLog.rolling.maxFilesToRetain" | int | `2147483647` |  |
| config."spark.history.fs.inProgressOptimization.enabled" | bool | `true` |  |
| config."spark.history.fs.logDirectory" | string | `"file:/tmp/spark-events"` |  |
| config."spark.history.fs.update.batchSize" | int | `2147483647` |  |
| config."spark.history.fs.update.interval" | string | `"10s"` |  |
| config."spark.history.kerberos.enabled" | bool | `false` |  |
| config."spark.history.kerberos.keytab" | string | `""` |  |
| config."spark.history.kerberos.principal" | string | `""` |  |
| config."spark.history.provider" | string | `"org.apache.spark.deploy.history.FsHistoryProvider"` |  |
| config."spark.history.retainedApplications" | int | `50` |  |
| config."spark.history.store.hybridStore.diskBackend" | string | `"ROCKSDB"` |  |
| config."spark.history.store.hybridStore.enabled" | bool | `false` |  |
| config."spark.history.store.hybridStore.maxMemoryUsage" | string | `"2g"` |  |
| config."spark.history.store.maxDiskUsage" | string | `"10g"` |  |
| config."spark.history.store.path" | string | `""` |  |
| config."spark.history.store.serializer" | string | `"JSON"` |  |
| config."spark.history.ui.maxApplications" | int | `2147483647` |  |
| config."spark.history.ui.port" | int | `18080` |  |
| containerSecurityContext.allowPrivilegeEscalation | bool | `false` |  |
| containerSecurityContext.capabilities.drop[0] | string | `"ALL"` |  |
| containerSecurityContext.readOnlyRootFilesystem | bool | `false` |  |
| containerSecurityContext.runAsUser | int | `185` |  |
| deploymentName | string | `nil` |  |
| envFrom | list | `[]` |  |
| extraEnvs | list | `[]` |  |
| extraVolumeMounts | list | `[]` |  |
| extraVolumes | list | `[]` |  |
| fullnameOverride | string | `""` |  |
| image.pullPolicy | string | `"IfNotPresent"` |  |
| image.repository | string | `"quay.io/okdp/spark"` |  |
| image.tag | string | `"spark-3.5.1-scala-2.12-java-17-2024-04-04-1.0.0"` |  |
| imagePullSecrets | list | `[]` |  |
| ingress.annotations | object | `{}` |  |
| ingress.enabled | bool | `false` |  |
| ingress.hosts[0].host | string | `"chart-example.local"` |  |
| ingress.hosts[0].paths[0].path | string | `"/"` |  |
| ingress.hosts[0].paths[0].pathType | string | `"Prefix"` |  |
| ingress.ingressClassName | string | `""` |  |
| ingress.labels | object | `{}` |  |
| ingress.name | string | `nil` |  |
| ingress.tls | list | `[]` |  |
| lifecycle | object | `{}` |  |
| livenessProbe | object | `{}` |  |
| logLevel | string | `"info"` |  |
| nameOverride | string | `""` |  |
| nodeSelector | object | `{}` |  |
| podAnnotations | object | `{}` |  |
| podLabels | object | `{}` |  |
| podSecurityContext.seccompProfile.type | string | `"RuntimeDefault"` |  |
| priorityClassName | string | `""` |  |
| readinessProbe | object | `{}` |  |
| replicaCount | int | `1` |  |
| resources | object | `{}` |  |
| service.annotations | object | `{}` |  |
| service.httpPortName | string | `"http"` |  |
| service.labels | object | `{}` |  |
| service.loadBalancerSourceRanges | list | `[]` |  |
| service.name | string | `nil` |  |
| service.nodePort | string | `""` |  |
| service.port | int | `18080` |  |
| service.type | string | `"ClusterIP"` |  |
| serviceAccount.annotations | object | `{}` |  |
| serviceAccount.create | bool | `false` |  |
| serviceAccount.name | string | `"default"` |  |
| startupProbe | object | `{}` |  |
| tolerations | object | `{}` |  |
| topologySpreadConstraints | list | `[]` |  |
| updateStrategy.type | string | `"RollingUpdate"` |  |

## Maintainers

| Name | Email | Url |
| ---- | ------ | --- |
| Moncef LAHOUAR | <moncef.lahouar@kubotal.io> |  |
