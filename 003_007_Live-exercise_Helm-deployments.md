


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Git push

git status
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git add .
git commit -m "Aula 003 - 007 - Live-exercise_Helm-deployments"
git push
git status


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
#  003_007_Live-exercise_Helm-deployments

07 Using Helm with ArgoCD

Learn how to deploy and sync Helm applications with Argo CD

Welcome

Using the ArgoCD GUI

Using ArgoCD CLI











Because GitOps is all about Git, we will use Github for this exercise.

The example application is at https://github.com/codefresh-contrib/gitops-certification-examples/tree/main/helm-app
Objectives

In this track, this is what you'll learn:

    How the Argo CD UI works
    How the Argo CD CLI works
    How to deploy an application with a Helm chart

Please wait while we boot the VM for you and start Kubernetes.







# Because GitOps is all about Git, we will use Github for this exercise.

The example application is at https://github.com/codefresh-contrib/gitops-certification-examples/tree/main/helm-app
Objectives

In this track, this is what you'll learn:

    How the Argo CD UI works
    How the Argo CD CLI works
    How to deploy an application with a Helm chart

Please wait while we boot the VM for you and start Kubernetes.










# Using the ArgoCD GUI

We installed Argo CD for you and you can login in the UI tab.

The UI starts empty because nothing is deployed on our cluster. Click the "NEW APP" button on the top left and fill the following details:

    application name : helm-gitops-example
    project: default
    sync policy: automatic
    repository URL: https://github.com/codefresh-contrib/gitops-certification-examples
    path: ./helm-app/
    Cluster: https://kubernetes.default.svc (this is the same cluster where ArgoCD is installed)
    Namespace: default

Leave all the other values empty or with default selections. Note that in the case of Helm charts you can also override specific values at the bottom of the dialog. This is completely optional as our Helm chart already has the correct values.yaml file.

Finally click the Create button. The application entry will appear in the main dashboard. Click on it.

Congratulations! Your have setup your first Helm application with GitOps. You should see the following:

Helm app

You can verify the deployment with

kubectl get deployment

Note that if you use any of the helm commands such as helm list you will not see anything at all. A Helm chart deployed by ArgoCD no longer registers as a Helm deployment. This is because ArgoCD doesn't include the Helm payload information. When deploying a Helm application, Argo CD runs "helm template" and deploys the resulting manifests.



~~~~bash
root@kubernetes-vm:~/workdir# kubectl get deployment
NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
helm-gitops-example-helm-example   1/1     1            1           16s
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# helm list
NAME    NAMESPACE       REVISION        UPDATED STATUS  CHART   APP VERSION
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# helm ls
NAME    NAMESPACE       REVISION        UPDATED STATUS  CHART   APP VERSION
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# date
Sat Oct 15 15:05:33 UTC 2022
root@kubernetes-vm:~/workdir# 
~~~~




You can manage your Argo CD installations either from the Web Interface or using the Command Line.
















# Using ArgoCD CLI
Step 1

Apart from the UI, ArgoCD also has a CLI. We have installed already the cli for you and authenticated against the instance.

Try the following commands

argocd app list
argocd app get helm-gitops-example
argocd app history helm-gitops-example

Let's delete the application and deploy it again but from the CLI this time.

First delete the app

argocd app delete helm-gitops-example

Confirm the deletion by answering yes in the terminal. The application will disappear from the Argo CD dashboard after some minutes.

Now deploy it again.

argocd app create demo \
--project default \
--repo https://github.com/codefresh-contrib/gitops-certification-examples \
--path "./helm-app/" \
--sync-policy auto \
--dest-namespace default \
--dest-server https://kubernetes.default.svc

The application will appear again in the ArgoCD dashboard.

Confirm the deployment

kubectl get all

Finish

If you've confirmed the deployment, click Check to finish this track.





~~~~bash
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# argocd app list
NAME                 CLUSTER                         NAMESPACE  PROJECT  STATUS  HEALTH   SYNCPOLICY  CONDITIONS  REPO                                                                PATH         TARGET
helm-gitops-example  https://kubernetes.default.svc  default    default  Synced  Healthy  Auto        <none>      https://github.com/codefresh-contrib/gitops-certification-examples  ./helm-app/  HEAD
root@kubernetes-vm:~/workdir# argocd app get helm-gitops-example
Name:               helm-gitops-example
Project:            default
Server:             https://kubernetes.default.svc
Namespace:          default
URL:                https://localhost:30443/applications/helm-gitops-example
Repo:               https://github.com/codefresh-contrib/gitops-certification-examples
Target:             HEAD
Path:               ./helm-app/
SyncWindow:         Sync Allowed
Sync Policy:        Automated
Sync Status:        Synced to HEAD (c89b02b)
Health Status:      Healthy

GROUP  KIND            NAMESPACE  NAME                              STATUS  HEALTH   HOOK  MESSAGE
       ServiceAccount  default    helm-gitops-example-helm-example  Synced                 serviceaccount/helm-gitops-example-helm-example created
       Service         default    helm-gitops-example-helm-example  Synced  Healthy        service/helm-gitops-example-helm-example created
apps   Deployment      default    helm-gitops-example-helm-example  Synced  Healthy        deployment.apps/helm-gitops-example-helm-example created
root@kubernetes-vm:~/workdir# argocd app history helm-gitops-example
ID  DATE                           REVISION
0   2022-10-15 15:05:08 +0000 UTC  HEAD (c89b02b)
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# argocd app delete helm-gitops-example
Are you sure you want to delete 'helm-gitops-example' and all its resources? [y/n]
y
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# argocd app create demo \
> --project default \
> --repo https://github.com/codefresh-contrib/gitops-certification-examples \
> --path "./helm-app/" \
> --sync-policy auto \
> --dest-namespace default \
> --dest-server https://kubernetes.default.svc
application 'demo' created
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
NAME                                     READY   STATUS    RESTARTS   AGE
pod/demo-helm-example-5546f55d74-tftln   1/1     Running   0          7s

NAME                        TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
service/kubernetes          ClusterIP   10.43.0.1       <none>        443/TCP   7m56s
service/demo-helm-example   ClusterIP   10.43.146.169   <none>        80/TCP    7s

NAME                                READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/demo-helm-example   1/1     1            1           7s

NAME                                           DESIRED   CURRENT   READY   AGE
replicaset.apps/demo-helm-example-5546f55d74   1         1         1       7s
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
~~~~