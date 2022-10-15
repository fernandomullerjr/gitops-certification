


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Git push

git status
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git add .
git commit -m "Aula 003 - 008 - Deploying with Kustomize"
git push
git status


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
#  Deploying with Kustomize

Kustomize is a CLI configuration manager for Kubernetes objects with a kustomization.yaml file. It is integrated with kubectl and allows you to customize raw, template-free YAML files for multiple purposes declaratively. Kustomize relies on the following configuration layering to enable reusability:

    Base Layer: This layer specifies the most common resources.
    Overlays: This layer specifies use-case specific resources by utilizing patches to override other kustomization files and Kubernetes manifests.


See below an example of a Kustomize file structure including the base and overlays within an application:
$ ~/demoApp
├── base
│   ├── configMap.yaml
│   ├── deployment.yaml
│   ├── kustomization.yaml
│   └── service.yaml
└── overlays
      ├── production
      │   ├── deployment.yaml
      │   └── kustomization.yaml
      └── staging
            ├── kustomization.yaml
            └── map.yaml
The base folder holds common resources, such as the deployment.yaml, service.yaml, and configuration files. The overlays folder houses environment-specific overlays, which use patches to allow YAML files to be defined and overlaid on top of the base for any changes.

When using Kustomize, there are some powerful commands you will find useful.

You can generate a customized YAML file:

kustomize build name_of_application

This file/resources can then be applied to a cluster:

kustomize build name_of_application | kubectl apply -k or --kustomize

This then creates all 3 resources previously mentioned: ConfigMap, Deployment, and Service.

Both Kustomize and Argo CD are declarative tools for Kubernetes that follow the GitOps pattern and work well together. Argo CD supports Kustomize and has the ability to read a kustomization.yaml file. To deploy Kustomize with Argo CD, ensure the Kubernetes cluster is set up and you are logged into Argo CD so that these resources are provided and can be deployed.

Create a namespace in the cluster:

kubectl create ns kustomize

Reference the Git repository with the Kustomize file to create an Argo CD app:

argocd app create name_of_application --repo repo_url --revision kustomize --path path_for_your_kustomize_files --dest-server server-url --dest-namespace kustomize

Sync deployment managed by Argo CD:

argocd app sync application_name

Check status of deployment:

argocd app get application_name

Each time a new kustomize.yaml file is created or the file changes, Argo CD can detect those and make updates to the deployment.