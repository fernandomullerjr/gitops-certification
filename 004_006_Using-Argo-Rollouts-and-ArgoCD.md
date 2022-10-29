
# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Git push

git status
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git add .
git commit -m "Aula 004 - 006 - Using Argo Rollouts & Argo CD"
git push
git status


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Using Argo Rollouts & Argo CD
Argo Rollouts is an independent project that can work on its own. It also works great with Argo CD. Argo Rollouts will react to any manifest change regardless of how the manifest was changed. The manifest can be changed by a Git commit, an API call, another controller, or even a manual kubectl command. In the case of Argo CD, all changes to the cluster should happen via Git commits.

You can create an Argo Rollout application in Argo CD via all the ways already described in the previous section.

For example, you can create an Argo Rollout application declaratively with the following Argo CD application:

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
    repoURL: 'https://github.com/kostis-codefresh/summer-of-k8s-app-manifests'
    path: ./
    targetRevision: HEAD

We have already talked about how Argo CD supports health status for several custom Kubernetes resources, and Argo Rollouts is one of them. This means:

    Argo CD will correctly handle Argo Rollouts as Argo CD “applications”.
    Argo CD will show up Argo Rollouts applications (in UI and CLI ) with the appropriate health status.
    A paused Argo Rollout will show up as “suspended” in the ArgoCD.
    The Argo CD UI also integrates some parts of the Argo Rollouts UI, so you can see the rollout status from within Argo CD UI


Other than that, both Argo Rollouts and Argo CD can work fine on their own. You don’t need to install Argo CD in order to use Argo Rollouts (a very common misconception).