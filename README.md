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


### Helm Diff Plugin
```bash
helm plugin install https://github.com/databus23/helm-diff
helm history release-name -n learning
helm diff revision release-name 1 2
helm diff revision hello 1 2 -n learning
```

### Additional Helm Functionalities
- Bitnami Repositories: helm repo add bitnami https://charts.bitnami.com/bitnami
- Viewing READMEs: helm show readme bitnami/wordpress --version 10.0.3
- Searching Versions: helm search repo wordpress --versions
- Viewing Chart Values:

```bash
helm show values bitnami/wordpress --version 10.0.3 
# Set custom values like wordpressUsername, wordpressPassword, etc.
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