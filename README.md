# Helm

- Helm Installation
- https://helm.sh/docs/intro/install/

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
helm version
helm version --short
```

- Note: Above Install the latest version of Helm 3

- To Install a specific version of Helm 3

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh -v v3.5.4
```

- Which environment variable can be used to indicate whether or not Helm is running in Debug mode?
    
```bash
echo $HELM_DEBUG
```

- What is a command line flag that can be used to enable verbose output
```bash
helm install --debug
```

- helm --help

- Note: Above command will list all the commands available in helm

- Helm Artifact hub
- https://artifacthub.io/

- helm search hub wordpress

- helm repo add bitnami https://charts.bitnami.com/bitnami

- helm repo --help

- helm history release-name -n learning

Note: Above command will list all the revisions of the release


### Basic Helm Commands

```bash
helm ls -A
helm repo ls
helm template dirname
```

### Helm Repository Management
-  Adding and Updating Jenkins Helm Repository
```bash
helm repo add jenkins https://charts.jenkins.io
helm repo update
helm repo ls
helm search repo jenkins
helm search repo jenkins/jenkins --versions
helm show values jenkins/jenkins --version 4.3.20
mkdir -p jenkins/values
helm show values jenkins/jenkins --version 4.3.20 > jenkins/values/jenkins.yaml
helm upgrade --install jenkins --namespace jenkins --create-namespace jenkins/jenkins --version 4.3.20 -f jenkins/values/jenkins.yaml --wait
helm ls -A
```

### Helm Syntax Checking with Helm Lint
```bash
helm lint dirname direname2
helm lint hello-0.1.0.tgz
```

- Basic Sample Release
```bash
helm create hello
cd hello
helm install hello ./ -f values.yaml --namespace datree --create-namespace --debug --dry-run
cd ..
helm template -f hello/values.yaml --namespace datree --create-namespace hello/
helm install hello ./ -f values.yaml --namespace datree --create-namespace --debug
helm upgrade --install hello ./ -f values.yaml --namespace datree --create-namespace --debug
helm ls -A
```

### Helm Template vs Dry Run
- Template: Outputs pure YAML, no Kubernetes connectivity needed.
- Dry Run: Outputs not pure YAML, needs Kubernetes API connectivity.

```bash
helm template dirname
helm template -f env.yaml helloworld 
helm template -f env.yaml ./
helm template -f helloworld/env.yaml helloworld/
helm upgrade --install nginx-alpine -f custom-values.yaml -f env.yaml ./ --namespace default --create-namespace
helm template -f hello/values.yaml --namespace datree --create-namespace hello/
helm template -f hello/values.yaml --namespace datree --create-namespace hello/ > pureyaml.yaml
helm template -f hello/values.yaml --namespace datree --create-namespace hello/ --dry-run
```

### helm Rollback
```bash
helm -n default rollback release-name revision-number
helm -n default rollback hello 1
helm -n default rollback hello 2
helm -n default rollback hello 1 --dry-run
```

### Verfiying te Helm Chart

- Helm lint

```bash
helm lint dirname
helm lint hello-0.1.0.tgz
```

- Helm lint defines the helm chart and yaml format is correct or not

- Helm template

```bash
helm template -f hello/values.yaml --namespace datree --create-namespace hello/
helm template -f hello/values.yaml --namespace datree --create-namespace hello/ > pureyaml.yaml
helm template -f hello/values.yaml --namespace datree --create-namespace hello/ --dry-run
```

- Helm template defines the helm chart and yaml format is correct or not

- Helm Dry Run

```bash
helm upgrade --install nginx-alpine -f custom-values.yaml -f env.yaml ./ --namespace default --create-namespace --dry-run
```

- Helm dry run test with kubernetes api connectivity and helm chart is correct or not

- Helm template --debug command is very hand to spot and detect the errors

```bash
helm template --debug -f hello/values.yaml --namespace datree --create-namespace hello/
```

- Helm Chart API Version

```bash
apiVersion: v2
```

Note: Above apiVersion is helm chart api version for helm 3



#### Helm functions

- https://helm.sh/docs/chart_template_guide/function_list/

- Example 1

```bash
{{ .Values.image.repository | default .Chart.Name | printf "%s" .Chart.Name }}
```

- Example 2
    
```bash
{{- if .Values.image.pullPolicy }}
{{- printf "{\"imagePullPolicy\": \"%s\"}" .Values.image.pullPolicy | quote }}
{{- else }}
{{- printf "{}" | quote }}
{{- end }}
```

- Q Which function takes a list of values and returns the first non-empty one?
- A coalesce

- Q Which function can be used to generate a random password that uses only letters a-zA-Z?
- A randAlpha

- Q We have decided that all labels have the first letter in upper case. which function can we use to achieve this?
- A title

- Q which function convert all letters to lower case?
- A lower

#### Helm pipeline

- Chaining together multiple functions to express a series of transformations is known as a pipeline.

- Example 1

```bash
{{ .Values.image.repository | default .Chart.Name | printf "%s" | quote }}
```

- Example 2

```bash
{{- if .Values.image.pullPolicy }}
{{- printf "{\"imagePullPolicy\": \"%s\"}" .Values.image.pullPolicy | quote | indent 2 }}
{{- else }}
{{- printf "{}" | quote }}
{{- end }}
```

#### Helm Conditions

```bash
{{- if .Values.image.pullPolicy }}
{{- printf "{\"imagePullPolicy\": \"%s\"}" .Values.image.pullPolicy | quote | indent 2 }}
{{- else }}
{{- printf "{}" | quote }}
{{- end }}
```

- Note: - means remove the white space, + means add the white space
- Note: if condition is true then only it will execute the code
```explain
If .Values.image.pullPolicy is set (i.e., it has a non-null value), it will print a JSON object like {"imagePullPolicy": "value"}, where value is replaced with the actual pullPolicy.

If .Values.image.pullPolicy is not set, it will print an empty JSON object {}.
The use of | quote wraps the output in quotes, which is often necessary in JSON structures.
| indent 2 is used to indent the output by two spaces, which can be useful for formatting, especially in YAML files.
The - in the template tags ({{- and -}}) ensures that the output does not have leading or trailing whitespaces or newlines that could disrupt the structure of the resultant YAML or JSON.
```

- if with eq
```explain
if conditional in Helm templates can be combined with the eq function to compare values. The eq function is used to check for equality between two values. Here's an example of using if with eq in a Helm template:

Suppose you have a Helm chart where you want to perform an action only if a certain value equals a specific string or number. Let's say you have a value named environment in your values.yaml file, and you want to do something specific when environment is set to "production".
```
- Example 1
```explain
{{- if eq .Values.environment "production" }}
# Commands or template expressions to execute when environment is production
{{- printf "Environment is set to production." | quote }}
{{- else }}
# Commands or template expressions to execute when environment is not production
{{- printf "Environment is not production." | quote }}
{{- end }}
```

#### Helm Ranges (loops)

- Q What is range in helm?
- A The range function in Helm is used to iterate over a list of values. It allows you to perform a set of actions for each value in the list within a Helm template. This is particularly useful for creating repetitive elements in Kubernetes manifests based on a list of values provided, such as generating configuration for multiple pods or services.


- Test1
```bash
update the configmap.yaml in such a way that the APP_COLOR should take the values based on the environment defined in values.yaml .


if environment is production, value should be pink.

else if environment is development, value should be darkblue.

else, value should be green.

Note: Only make the necessary changes in the configmap.yaml file.
```

- Solution
- configmap.yaml
```yaml
apiVersion: v1
metadata:
  name: {{ .Values.configMap.name }}
  namespace: default
kind: ConfigMap
data:
  {{- if eq .Values.environment "production" }}
    APP_COLOR: pink
  {{- else if eq .Values.environment "development" }}
    APP_COLOR: darkblue
  {{- else }}
    APP_COLOR: green
  {{- end }}
```

- values.yaml
```yaml
environment: production
```

- Templates
```explain
The text you've provided is mostly correct but could benefit from some clarifications and a correction in the example to accurately reflect how Helm processes files and templates. Here's a revised version:

Note: In Helm, any files within the templates directory that start with an underscore (_) are treated as helper files and are not rendered directly as Kubernetes manifests.

Example: The file named _helpers.tpl is considered a helper template and will be ignored by Helm in terms of direct rendering into Kubernetes objects.

To utilize the contents or define reusable snippets in the _helpers.tpl file, the include function is used in Helm templates.
```

- Example 1
```bash
{{- include "labels" . -}}
```

```explain
This example demonstrates the use of the include function to incorporate the output of a template defined in _helpers.tpl (in this case, "labels") into another Helm template. The . symbol is used to pass the current context to the included template. The original text incorrectly used the template keyword, which is not typically used for accessing _helpers.tpl. The include keyword is the appropriate choice for this purpose.
```

- Note: indent function is used to fix the indentation

#### Helm hooks

```explain
Helm hooks are a powerful feature in Helm charts that allow you to control the lifecycle of your Kubernetes applications. They let you perform actions at certain points in the deployment process, such as installing, upgrading, or deleting resources. Helm hooks are managed through annotations in Kubernetes manifest files.

Types of Helm Hooks:
pre-install: Executes before any resources are installed into Kubernetes. Useful for setting up prerequisites or configurations needed for your application.

post-install: Executes after all resources are installed into Kubernetes. Often used for setup tasks that require the application to be running.

pre-delete: Executes before a chart is deleted. This can be useful for cleaning up resources that aren't managed directly by Helm.

post-delete: Executes after a chart is deleted. Used for final cleanup actions.

pre-upgrade: Executes before an upgrade of a release. It helps in preparing the environment for the upgrade.

post-upgrade: Executes after the upgrade of a release. Useful for tasks that should happen after the upgrade.

pre-rollback: Executes before a rollback. Useful for preparing the environment for a rollback.

post-rollback: Executes after a rollback is done. Often used for cleanup or reconfiguration tasks.

crd-install: Used to load Custom Resource Definitions before the rest of the charts are loaded. This is crucial for charts that rely on CRDs.

Example:
Imagine you have a Helm chart for deploying a web application. You want to ensure a database is ready before your application starts. You could use a pre-install hook to deploy a job that sets up the database schema.
```

- Example 1
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: "{{ .Release.Name }}-db-setup"
  annotations:
    "helm.sh/hook": pre-install
spec:
  template:
    spec:
      containers:
      - name: schema-setup
        image: mydb-setup-image:latest
        command: ["setup-database.sh"]
      restartPolicy: Never
```

```explain
In this example, the Job Kubernetes resource will be executed before your application is installed, ensuring the database schema is set up. The "helm.sh/hook": pre-install annotation tells Helm to execute this job as a pre-install hook.
```

- Note: In kubernetes we setup a job to setup the database schema, for this we add annotation "helm.sh/hook": pre-install

### Helm Diff Plugin
``` plugin install https://github.com/databus23/helm-diff
helm history release-name -n learning
helm diff revision release-name 1 2
helm diff revision hello 1 2 -n learning
```

### Additional Helm Functionalities
- Bitnami Repositories: helm repo add bitnami https://charts.bitnami.com/bitnami
- Viewing READMEs: helm show readme bitnami/wordpress --version 10.0.3
- Searching Versions: helm search repo wordpress --versions
- Viewing Chart Values:

```bashbash
helm
helm show values bitnami/wordpress --version 10.0.3 
# Set custom values like wordpressUsername, wordpressPassword, etc.
```


- Helm Package Management
```bash
helm package dirname
helm package test 
helm upgrade --install test ./test-0.1.0.tgz --namespace test --create-namespace --wait
helm ls -A
```

- Installation and Monitoring
```bash
Installation: helm upgrade --install wordpress bitnami/wordpress --values=wordpress-values.yaml --namespace wordpress --create-namespace --version 10.0.3 --debug
Monitoring: watch -x kubectl get all --namespace wordpress
Advanced Features: Helm Secrets, Rollbacks, History, and More
This section covers advanced Helm functionalities like Helm Secrets management, rollbacks, and viewing Helm history. Due to the extensive details, these are elaborated separately in the extended guide.
```

- Troubleshooting Helm Releases
- Address common issues like stuck upgrades or pending states.
- Utilize Kubernetes secrets for state management.
- Implement rollback strategies for Helm releases.
- For detailed troubleshooting steps and more information, refer to the Helm documentation or community resources.

- Note: This guide is intended to get you started with Helm. For comprehensive understanding, always refer to the official Helm documentation.


- Important Note:
```explain
Remember, once a Deployment is created, you cannot change its spec.selector.matchLabels field. If you need to change this field, you will have to delete and recreate the Deployment, or use a different strategy for labeling your resources.
```