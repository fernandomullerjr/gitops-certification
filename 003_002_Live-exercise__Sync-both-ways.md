
# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Git push
git status
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git add .
git commit -m "Aula 03 Sync both ways"
git push
git status


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# 03 Sync both ways

Learn how Argo CD works in both directions

Welcome

First deployment

Deploy a new version

Detecting cluster changes









You need to have a GitHub account for this exercise.

The example application is at https://github.com/codefresh-contrib/gitops-certification-examples/tree/main/simple-app. Fork this repository in your own account
Objectives

In this track, this is what you'll learn:

    How to change the application in Git and redeploy
    How Argo CD detects changes between the cluster and Git
    How to make changes in the cluster and see the diff

Please wait while we boot the VM for you and start Kubernetes.



- Efetuado o Fork:
<https://github.com/fernandomullerjr/gitops-certification-examples>







# Welcome

Our example application can be found at https://github.com/codefresh-contrib/gitops-certification-examples/tree/main/simple-app

Make sure to fork it to your own account and not down the URL. It should be something like:

https://github.com/<your user>/gitops-certification-examples/

Take a look at the Kubernetes manifests to understand what we will deploy. It is a very simple application with one deployment and one service

Once you are ready to proceed, press Next.







# First deployment

We installed Argo CD for you and you can login in the UI tab.

The UI starts empty because nothing is deployed on our cluster. Click the "New app" button on the top left and fill the following details:

    application name : demo
    project: default
    repository URL: https://github.com/<your user>/gitops-certification-examples
    path: ./simple-app
    Cluster: https://kubernetes.default.svc (this is the same cluster where ArgoCD is installed)
    Namespace: default

Leave all the other values empty or with default selections. Finally click the Create button. The application entry will appear in the main dashboard. Click on it.

You should see the following. unsynced app

Now sync the application to make sure that it is deployed. You should see the following: synced app

You can also verify the application from the command line with:

    kubectl get deployments

Once you are ready to proceed, press Check.




<https://github.com/fernandomullerjr/gitops-certification-examples>
https://github.com/fernandomullerjr/gitops-certification-examples




root@kubernetes-vm:~/workdir# kubectl get deployments
NAME                READY   UP-TO-DATE   AVAILABLE   AGE
simple-deployment   1/1     1            1           19s
root@kubernetes-vm:~/workdir# 






Let's deploy a second version by changing the Git state.









# Deploy a new version

We want to deploy another version of our application. We will change the Git and see how Argo CD detects the change.

Perform a commit in your simple-app/deployment.yml file in your own account (You can use GitHub on another tab for this) and change the container tag at line 18 from v1.0 to v2.0

Normally Argo CD checks the state between Git and the cluster every 3 minutes on its own. Just to speed things up you should click manually on the application in the Argo CD dashboard and press the "Refresh" button

You should see the following. git-change

Here Argo CD tells us that the Git state is no longer the same as the cluster state. From the yellow arrows you can see that the from all the Kubernetes components the deployment is now different. And thus the whole application is different.

You can click on the "App diff" button and see the exact change. Enable the "compact diff" checbox.

git-diff

Once you are ready to proceed, press Check.










- The Argo CD controller also detects cluster changes









# Detecting cluster changes
Step 1

Let's bring the cluster back to the same state as Git. Click the Sync button in the UI to make the application synced with the new version.

You can also execute the following from the CLI:

    argocd app sync demo
    argocd app wait demo

Detecting changes in Git and applying is a well known scenario. The big strength of GitOps, is that Argo CD works in the opposite direction as well. If you make any change in the cluster then Argo CD will detect it and again tell you that something is different between Git and your cluster.

Let's say that somebody changes manually the replicas of the deployment without creating an official Pull Request (a bad practice in general).

Execute the following

    kubectl scale --replicas=3 deployment simple-deployment

Normally Argo CD checks the state between Git and the cluster every 3 minutes on its own. Just to speed things up you should click manually on the application in the Argo CD dashboard and press the "Refresh" button. The click the "App diff" button and enable the "Compact Diff" checkbox.

You should see the following:

git-change

Argo CD again detects the change between the two states. This capability is very powerful and you can easily detect configuration drift between your environments.
Finish

Click Check to finish this track.


- Saída dos comandos:

~~~~bash
root@kubernetes-vm:~/workdir# argocd app sync demo
TIMESTAMP                  GROUP        KIND   NAMESPACE                  NAME    STATUS    HEALTH        HOOK  MESSAGE
2022-10-11T01:07:46+00:00            Service     default        simple-service    Synced   Healthy              
2022-10-11T01:07:46+00:00   apps  Deployment     default     simple-deployment  OutOfSync  Healthy              
2022-10-11T01:07:46+00:00            Service     default        simple-service    Synced   Healthy              service/simple-service unchanged
2022-10-11T01:07:46+00:00   apps  Deployment     default     simple-deployment  OutOfSync  Healthy              deployment.apps/simple-deployment configured
2022-10-11T01:07:46+00:00   apps  Deployment     default     simple-deployment    Synced  Progressing              deployment.apps/simple-deployment configured

Name:               demo
Project:            default
Server:             https://kubernetes.default.svc
Namespace:          default
URL:                https://localhost:30443/applications/demo
Repo:               https://github.com/fernandomullerjr/gitops-certification-examples
Target:             HEAD
Path:               ./simple-app
SyncWindow:         Sync Allowed
Sync Policy:        <none>
Sync Status:        Synced to HEAD (c7b5bc9)
Health Status:      Progressing

Operation:          Sync
Sync Revision:      c7b5bc961cb85b4a2b02a42e3cdd3744fc628407
Phase:              Succeeded
Start:              2022-10-11 01:07:46 +0000 UTC
Finished:           2022-10-11 01:07:46 +0000 UTC
Duration:           0s
Message:            successfully synced (all tasks run)

GROUP  KIND        NAMESPACE  NAME               STATUS  HEALTH       HOOK  MESSAGE
       Service     default    simple-service     Synced  Healthy            service/simple-service unchanged
apps   Deployment  default    simple-deployment  Synced  Progressing        deployment.apps/simple-deployment configured
root@kubernetes-vm:~/workdir# argocd app wait demo

Name:               demo
Project:            default
Server:             https://kubernetes.default.svc
Namespace:          default
URL:                https://localhost:30443/applications/demo
Repo:               https://github.com/fernandomullerjr/gitops-certification-examples
Target:             HEAD
Path:               ./simple-app
SyncWindow:         Sync Allowed
Sync Policy:        <none>
Sync Status:        Synced to HEAD (c7b5bc9)
Health Status:      Healthy

Operation:          Sync
Sync Revision:      c7b5bc961cb85b4a2b02a42e3cdd3744fc628407
Phase:              Succeeded
Start:              2022-10-11 01:07:46 +0000 UTC
Finished:           2022-10-11 01:07:46 +0000 UTC
Duration:           0s
Message:            successfully synced (all tasks run)

GROUP  KIND        NAMESPACE  NAME               STATUS  HEALTH   HOOK  MESSAGE
       Service     default    simple-service     Synced  Healthy        service/simple-service unchanged
apps   Deployment  default    simple-deployment  Synced  Healthy        deployment.apps/simple-deployment configured
root@kubernetes-vm:~/workdir# 
~~~~





- O Argo CD detecta novamente a mudança entre os dois estados. 
Esse recurso é muito poderoso e você pode detectar facilmente o desvio de configuração entre seus ambientes.

