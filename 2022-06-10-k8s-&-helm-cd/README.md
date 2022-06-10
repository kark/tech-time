## Objectives

Getting familiar with helm chart templating

### Findings

1. Playing with helm chart syntax

- for metadata name we might use `{{ .Chart.Name }}` (hardcoded chart name) or `{{ .Release.Name }}`
- conditional templating

```yaml
# _templates.tpl
{{/*
Create a default fully qualified app name.
We truncate at 63 chars because some Kubernetes name fields are limited to this (by the DNS naming spec).
If release name contains chart name it will be used as a full name.
*/}}
{{- define "app.fullname" -}}
{{- if .Values.fullnameOverride -}}
  {{- .Values.fullnameOverride | trunc 63 | trimSuffix "-" -}}
{{- else -}}
  {{- $name := default .Chart.Name .Values.nameOverride -}}
  {{- if contains $name .Release.Name -}}
    {{- .Release.Name | trunc 63 | trimSuffix "-" -}}
  {{- else -}}
    {{- printf "%s-%s" .Release.Name $name | trunc 63 | trimSuffix "-" -}}
  {{- end -}}
  {{- end -}}
{{- end -}}
```

and then use it in the config yaml like:

```yaml
metadata:
  name: { { template "app.fullname" . } }
```

- conditionals (flags)

```yaml
# values.yaml
loggingFlag:
  enabled: true
```

and then use it in the config yaml like:

```yaml
{{- if (.Values.loggingFlag.enabled) }}
#...
spec:
  logging:
    enable: {{ .Values.loggingFlag.enabled }}
{{- end }}
```

- conditionaly add ingress resource

```yaml
# values.yaml
ingress:
  enabled: false
```

and then:

```yaml
{{ if (.Values.ingress.enabled) }}
  # ...
{{ end }}
```

- names and labels

  - https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#names
  - https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/

Useful links:

- https://helm.sh/docs/chart_template_guide/
- https://banzaicloud.com/blog/creating-helm-charts/
- https://banzaicloud.com/blog/creating-helm-charts-part-2/

2. [deployment of react app in K8s cluster via AWS EKS](https://medium.com/bb-tutorials-and-thoughts/aws-deploying-react-app-with-nginx-on-eks-2d3384247cda)
