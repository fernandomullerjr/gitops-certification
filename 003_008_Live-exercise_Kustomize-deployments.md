


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Git push

git status
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git add .
git commit -m "Aula 003 - 008 - 08 Using Kustomize with ArgoCD"
git push
git status


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# 08 Using Kustomize with ArgoCD

Learn how to deploy and sync Kustomize applications with Argo CD

Welcome

Using the ArgoCD GUI

Using ArgoCD CLI









Because GitOps is all about Git, we will use Github for this exercise.

The example application is at https://github.com/codefresh-contrib/gitops-certification-examples/tree/main/kustomize-app
Objectives

In this track, this is what you'll learn:

    How to deploy a Kustomize application using both the ArgoCD UI and argocd CLI

Please wait while we boot the VM for you and start Kubernetes.











# Welcome

Our example application can be found at https://github.com/codefresh-contrib/gitops-certification-examples/tree/main/kustomize-app

Take a look at the base and overlays folders to understand what we will deploy. It is a very simple application with 2 environments to deploy to: staging and production.

The environments differ in several aspects such as number of replicas, database used, networking etc.

Once you are ready to proceed, press Next.















# Using the ArgoCD GUI

We installed Argo CD for you and you can login in the UI tab.

The UI starts empty because nothing is deployed on our cluster. Click the "NEW APP" button on the top left and fill the following details:

    application name : kustomize-gitops-example
    project: default
    sync policy: automatic
    repository URL: https://github.com/codefresh-contrib/gitops-certification-examples
    path: ./kustomize-app/overlays/staging
    Cluster: https://kubernetes.default.svc (this is the same cluster where ArgoCD is installed)
    Namespace: default

Leave all the other values empty or with default selections. Finally click the Create button. The application entry will appear in the main dashboard. Click on it.

Congratulations! Your have setup your first Kustomize application with GitOps.

You should see the following. synced app

Wait some time for the application to start up and then visit the "Deployed App" to see it running. You will see that it has loaded the staging value for the database.








You can manage your Argo CD installations either from the Web Interface or using the Command Line.











# Using ArgoCD CLI
Step 1

Apart from the UI, ArgoCD also has a CLI. We have installed already the cli for you and authenticated against the instance.

Try the following commands

argocd app list
argocd app get kustomize-gitops-example
argocd app history kustomize-gitops-example

Let's delete the application and deploy it again but from the CLI this time.

First delete the app

argocd app delete kustomize-gitops-example

Confirm the deletion by answering yes in the terminal. The application will disappear from the Argo CD dashboard after some minutes.

Now deploy it again.

argocd app create demo \
--project default \
--repo https://github.com/codefresh-contrib/gitops-certification-examples \
--path ./kustomize-app/overlays/staging \
--sync-policy auto \
--dest-namespace default \
--dest-server https://kubernetes.default.svc

The application will appear in the ArgoCD dashboard.

Confirm the deployment

kubectl get all

Finish

If you've confirmed the deployment, click Check to finish this track




~~~~bash
root@kubernetes-vm:~/workdir# ls
admin-pass.txt
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# argocd app list
NAME                      CLUSTER                         NAMESPACE  PROJECT  STATUS  HEALTH   SYNCPOLICY  CONDITIONS  REPO                                                                PATH                              TARGET
kustomize-gitops-example  https://kubernetes.default.svc  default    default  Synced  Healthy  Auto        <none>      https://github.com/codefresh-contrib/gitops-certification-examples  ./kustomize-app/overlays/staging  HEAD
root@kubernetes-vm:~/workdir# argocd app get kustomize-gitops-example
Name:               kustomize-gitops-example
Project:            default
Server:             https://kubernetes.default.svc
Namespace:          default
URL:                https://localhost:30443/applications/kustomize-gitops-example
Repo:               https://github.com/codefresh-contrib/gitops-certification-examples
Target:             HEAD
Path:               ./kustomize-app/overlays/staging
SyncWindow:         Sync Allowed
Sync Policy:        Automated
Sync Status:        Synced to HEAD (c89b02b)
Health Status:      Healthy

GROUP  KIND        NAMESPACE  NAME                    STATUS  HEALTH   HOOK  MESSAGE
       ConfigMap   default    staging-the-map         Synced                 configmap/staging-the-map created
       Service     default    staging-demo            Synced  Healthy        service/staging-demo created
apps   Deployment  default    staging-the-deployment  Synced  Healthy        deployment.apps/staging-the-deployment created
root@kubernetes-vm:~/workdir# argocd app history kustomize-gitops-example
ID  DATE                           REVISION
0   2022-10-15 15:36:01 +0000 UTC  HEAD (c89b02b)
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# argocd app delete kustomize-gitops-example
Are you sure you want to delete 'kustomize-gitops-example' and all its resources? [y/n]
y
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# argocd app list
NAME                      CLUSTER                         NAMESPACE  PROJECT  STATUS     HEALTH   SYNCPOLICY  CONDITIONS  REPO                                                                PATH                              TARGET
kustomize-gitops-example  https://kubernetes.default.svc  default    default  OutOfSync  Missing  Auto        <none>      https://github.com/codefresh-contrib/gitops-certification-examples  ./kustomize-app/overlays/staging  HEAD
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# argocd app create demo \
> --project default \
> --repo https://github.com/codefresh-contrib/gitops-certification-examples \
> --path ./kustomize-app/overlays/staging \
> --sync-policy auto \
> --dest-namespace default \
> --dest-server https://kubernetes.default.svc
application 'demo' created
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# argocd app list
NAME  CLUSTER                         NAMESPACE  PROJECT  STATUS  HEALTH   SYNCPOLICY  CONDITIONS  REPO                                                                PATH                              TARGET
demo  https://kubernetes.default.svc  default    default  Synced  Healthy  Auto        <none>      https://github.com/codefresh-contrib/gitops-certification-examples  ./kustomize-app/overlays/staging  
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# kubectl get all
NAME                                          READY   STATUS    RESTARTS   AGE
pod/staging-the-deployment-85c5c7685f-kxgzr   1/1     Running   0          46s

NAME                   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
service/kubernetes     ClusterIP   10.43.0.1       <none>        443/TCP          12m
service/staging-demo   NodePort    10.43.174.230   <none>        8080:31000/TCP   46s

NAME                                     READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/staging-the-deployment   1/1     1            1           46s

NAME                                                DESIRED   CURRENT   READY   AGE
replicaset.apps/staging-the-deployment-85c5c7685f   1         1         1       46s
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
~~~~