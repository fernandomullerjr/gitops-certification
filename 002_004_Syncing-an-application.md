

# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Git push
git status
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git add .
git commit -m "Aula 2 - Syncing an application"
git status


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Syncing an application

Synchronizing an Argo CD application in the UI
Initially, the application is in OutOfSync state since the application has yet to be deployed, and no Kubernetes resources have been created.

To synchronize/deploy the Argo CD app:

1. Choose the tile and then select SYNC

This will provide you options of what you want to synchronize.

2. Select the default options and synchronize all manifests

Once it's deployed, you will see the resources deployed in the UI and a Healthy status.
Synchronizing an Argo CD application with the argocd CLI
Since the application is in OutOfSync status once created because it hasn’t been deployed yet, we will then sync the application with a sync command.

argocd app sync {APP NAME}

This synchronizes the application. To confirm it’s running, you can execute a kubectl command.

kubectl -n {NAMESPACE} get all

The application will have a status “Running” if synchronized successfully.