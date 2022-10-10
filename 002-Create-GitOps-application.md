
# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Git push
git status
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git add .
git commit -m "Aula 02 Create GitOps application"
git status


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# 02 Create GitOps application

Learn how to deploy and sync applications with Argo CD

Welcome

Argo CD User interface

Syncing an app

Argo CD CLI







Because GitOps is all about Git, we will use Github for this exercise.

The example application is at https://github.com/codefresh-contrib/gitops-certification-examples/tree/main/simple-app
Objectives

In this track, this is what you'll learn:

    How the Argo CD UI works
    How the Argo CD CLI works
    How to deploy and sync applications

Please wait while we boot the VM for you and start Kubernetes.









# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
#  Welcome

Our example application can be found at https://github.com/codefresh-contrib/gitops-certification-examples/tree/main/simple-app

Take a look at the Kubernetes manifests to understand what we will deploy. It is a very simple application with one deployment and one service

Once you are ready to proceed, press Next.


- Service

~~~~yaml
apiVersion: v1
kind: Service
metadata:
  name: simple-service
spec:
  type: NodePort
  selector:
    app: trivial-go-web-app
  ports:
      - nodePort: 31000
        protocol: TCP
        port: 8080
~~~~

- Deployment

~~~~yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: simple-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: trivial-go-web-app
  template:
    metadata:
      labels:
        app: trivial-go-web-app
    spec:
      containers:
      - name: webserver-simple
        image: docker.io/kostiscodefresh/gitops-simple-app:v1.0
        ports:
        - containerPort: 8080
~~~~






# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Argo CD User interface

We installed Argo CD for you and you can login in the UI tab.

The UI starts empty because nothing is deployed on our cluster. Click the "New app" button on the top left and fill the following details:

    application name : demo
    project: default
    repository URL: https://github.com/codefresh-contrib/gitops-certification-examples
    path: ./simple-app
    Cluster: https://kubernetes.default.svc (this is the same cluster where ArgoCD is installed)
    Namespace: default

Leave all the other values empty or with default selections. Finally click the Create button. The application entry will appear in the main dashboard. Click on it.

You should see the following. unsynced app

Congratulations! Your have setup your first application with GitOps.

However if you check your cluster with

kubectl get deployments

You will see that nothing is deployed yet. The cluster is still empty. The "Out of sync" message means essentially this

    The cluster is empty
    The Git repository has an application
    Therefore the Git state and the cluster state are different.


~~~~bash
root@kubernetes-vm:~/workdir# kubectl get deployments
No resources found in default namespace.
root@kubernetes-vm:~/workdir# 
~~~~








Syncing in ArgoCD means matching the state described in Git and what is in the cluster





# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Syncing an app

We have created our application in Argo CD, but it is still not deployed as it only exists in Git right now. We need to tell Argo CD to make the Git state equal to the cluster state.

Visit the Argo CD UI tab and click on your application to see all the details.Click the "Sync button" and leave all the default options in all choices. Finally click the "Synchronize" button at the top.

ArgoCD sees that the cluster is empty and it will deploy your application in order to make the cluster have the same state as what is in Git

You should see the following. synced app

Your application is now deployed. You can check it with:

kubectl get deployments

and also visit the "Deployed App" tab.


- Acessando a aba "Deployed App":
    I am an application running in Kubernetes. Now at Version 1.0

~~~~bash
root@kubernetes-vm:~/workdir# kubectl get deployments
NAME                READY   UP-TO-DATE   AVAILABLE   AGE
simple-deployment   1/1     1            1           40s
root@kubernetes-vm:~/workdir# 
~~~~



- You can manage your Argo CD installations either from the Web Interface or using the Command Line.













# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Argo CD CLI

Step 1

Apart from the UI, ArgoCD also has a CLI. We have installed already the cli for you and authenticated against the instance.

Try the following commands

argocd app list
argocd app get demo
argocd app history demo

Let's delete the application and deploy it again but from the CLI this time.

First delete the app

argocd app delete demo

Confirm the deletion by answering yes in the terminal. The application will disappear from the Argo CD dashboard after some minutes.

Now deploy it again.

argocd app create demo2 \
--project default \
--repo https://github.com/codefresh-contrib/gitops-certification-examples \
--path "./simple-app" \
--dest-namespace default \
--dest-server https://kubernetes.default.svc

The application will appear in the ArgoCD dashboard.

Now sync it with

argocd app sync demo2

Finish

If you've viewed the dashboard, click Check to finish this track.



~~~~bash
root@kubernetes-vm:~/workdir# argocd app list
NAME  CLUSTER                         NAMESPACE  PROJECT  STATUS  HEALTH   SYNCPOLICY  CONDITIONS  REPO                                                                PATH          TARGET
demo  https://kubernetes.default.svc  default    default  Synced  Healthy  <none>      <none>      https://github.com/codefresh-contrib/gitops-certification-examples  ./simple-app  HEAD
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# argocd app get demo
Name:               demo
Project:            default
Server:             https://kubernetes.default.svc
Namespace:          default
URL:                https://localhost:30443/applications/demo
Repo:               https://github.com/codefresh-contrib/gitops-certification-examples
Target:             HEAD
Path:               ./simple-app
SyncWindow:         Sync Allowed
Sync Policy:        <none>
Sync Status:        Synced to HEAD (c89b02b)
Health Status:      Healthy

GROUP  KIND        NAMESPACE  NAME               STATUS  HEALTH   HOOK  MESSAGE
       Service     default    simple-service     Synced  Healthy        service/simple-service created
apps   Deployment  default    simple-deployment  Synced  Healthy        deployment.apps/simple-deployment created
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# argocd app history demo
ID  DATE                           REVISION
0   2022-10-10 00:01:40 +0000 UTC  HEAD (c89b02b)
root@kubernetes-vm:~/workdir# argocd app delete demo
Are you sure you want to delete 'demo' and all its resources? [y/n]
y
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# argocd app create demo2 \
> --project default \
> --repo https://github.com/codefresh-contrib/gitops-certification-examples \
> --path "./simple-app" \
> --dest-namespace default \
> --dest-server https://kubernetes.default.svc
application 'demo2' created
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# argocd app sync demo2
TIMESTAMP                  GROUP        KIND   NAMESPACE                  NAME    STATUS    HEALTH        HOOK  MESSAGE
2022-10-10T00:06:43+00:00            Service     default        simple-service  OutOfSync  Missing              
2022-10-10T00:06:43+00:00   apps  Deployment     default     simple-deployment  OutOfSync  Missing              
2022-10-10T00:06:43+00:00            Service     default        simple-service    Synced  Healthy              
2022-10-10T00:06:44+00:00   apps  Deployment     default     simple-deployment  OutOfSync  Missing              deployment.apps/simple-deployment created
2022-10-10T00:06:44+00:00            Service     default        simple-service    Synced   Healthy              service/simple-service created
2022-10-10T00:06:44+00:00   apps  Deployment     default     simple-deployment    Synced  Progressing              deployment.apps/simple-deployment created

Name:               demo2
Project:            default
Server:             https://kubernetes.default.svc
Namespace:          default
URL:                https://localhost:30443/applications/demo2
Repo:               https://github.com/codefresh-contrib/gitops-certification-examples
Target:             
Path:               ./simple-app
SyncWindow:         Sync Allowed
Sync Policy:        <none>
Sync Status:        Synced to  (c89b02b)
Health Status:      Progressing

Operation:          Sync
Sync Revision:      c89b02b34185626d631a1e91e888098e76c568ec
Phase:              Succeeded
Start:              2022-10-10 00:06:43 +0000 UTC
Finished:           2022-10-10 00:06:44 +0000 UTC
Duration:           1s
Message:            successfully synced (all tasks run)

GROUP  KIND        NAMESPACE  NAME               STATUS  HEALTH       HOOK  MESSAGE
       Service     default    simple-service     Synced  Healthy            service/simple-service created
apps   Deployment  default    simple-deployment  Synced  Progressing        deployment.apps/simple-deployment created
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
~~~~