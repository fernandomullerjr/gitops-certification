# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Git push

git status
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git add .
git commit -m "Aula 03 - Declarative Setup"
git push
git status


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Declarative Setup

In the previous sections, we have seen how to use the Argo CD UI to manually create applications or sync/delete them. The Argo CD CLI also has similar capabilities.

But the whole point of GitOps is to have ALL changes/updates stored in Git and not just the Kubernetes manifests. Using the Argo CD UI for creating applications is great for experimentation and onboarding. However, in a production environment, you should use Git for all operations instead.

To achieve this goal, Argo CD comes with its own custom resources that can be stored in Git, and applied in a cluster using kubectl or even better Argo CD itself.

For example when you create an application in Argo CD, essentially you are creating a grouping between:

    A Git repository that holds your manifest
    An Argo CD project
    A cluster
    Several other custom settings (extra parameters, target namespace etc.)


Even though the action is performed by the UI by clicking buttons, it is better if you create the following Argo CD application:

~~~~yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: canary-demo
spec:
  destination:
    server: 'https://kubernetes.default.svc' 
    namespace: demo
  project: default
  source:
    repoURL: 'https://github.com/kostis-codefresh/summer-of-k8s-app-manifests
    path: ./
    targetRevision: HEAD
~~~~



This file has the same information as defined from the Argo CD UI. But since it is a standard Kubernetes resource, you can use all your favorite tools for Kubernetes resources including committing it to Git and managing it with Argo CD.

For the full details of all Argo CD objects, you should consult
https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/

Apart from applications, all other Argo CD entities have the respective Kubernetes resource such as:

    Projects
    Secrets
    RBAC configuration
    Git repositories
    Etc.


This means that in all scenarios, it is much better to fully adopt GitOps and work exclusively via Kubernetes resources and git commits instead of GUI actions. The GUI of Argo CD is best used for verifying and inspecting resources instead of changing them by hand.