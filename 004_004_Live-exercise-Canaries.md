
# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Git push

git status
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git add .
git commit -m "Aula 004 - 004 -10 Canary deployments"
git push
git status


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# 10 Canary deployments

Canary deployments with Argo Rollouts

Welcome

Install the Argo Rollouts controller

Install Ambassador

The first deployment

Canary deployments











# Progressive delivery with Argo Rollouts

The example application is at https://github.com/codefresh-contrib/gitops-certification-examples/tree/main/canary-app.
Objectives

In this track, this is what you'll learn:

    Installation of the Argo Rollouts controller
    How to install an API gateway
    How to convert a Deployment to a Rollout
    How to perform canary deployments
    How to monitor the state of the deployment

Please wait while we boot the VM for you and start Kubernetes.








# Welcome

Our example application can be found at https://github.com/codefresh-contrib/gitops-certification-examples/tree/main/canary-app.

It is a very simple application with one deployment and one service. Notice that the deployment has been converted to a Rollout and has an extra section about the canary deployment options.

You can also see the full source code at source-code-canary if you want to understand how the application is rendering a web page.

Once you are ready to proceed, press Next.







# Install the Argo Rollouts controller

Before we get started with progressive delivery we need to install the Argo Rollouts controller on the cluster.

We installed Argo CD for you and you can login in the UI tab.

The UI starts empty because nothing is deployed on our cluster. Click the "New app" button on the top left and fill the following details:

    application name : argo-rollouts-controller
    project: default
    SYNC POLICY: automatic
    AUTO-CREATE Namespace: enabled
    repository URL: https://github.com/codefresh-contrib/gitops-certification-examples
    path: ./argo-rollouts-controller
    Cluster: https://kubernetes.default.svc (this is the same cluster where ArgoCD is installed)
    Namespace: argo-rollouts

Leave all the other values empty or with default selections. Finally click the Create button. The controller will be installed on the cluster.

Notice that we are not using the default namespace, but a brand new one. It is imperative that you select the "auto-create namespace" option.

auto-create

If you don't select this option, ArgoCD will not find the namespace and deployment will fail. Delete the application and create it again if you have a deployment issue.

You can also manually do if you forgot it in the UI:

kubectl create namespace argo-rollouts

Try syncing again the application.

You can see that GitOps is not only useful for your own applications but also for other supporting applications as well.

Wait some time until the controller is fully synced and the deployment is marked as Healthy.

Once you are ready to proceed, press Check.




root@kubernetes-vm:~/workdir# kubectl get all -n argocd
NAME                                     READY   STATUS    RESTARTS   AGE
pod/argocd-redis-5b6967fdfc-vdngf        1/1     Running   0          2m59s
pod/argocd-dex-server-5fc596bcdd-dprjh   1/1     Running   0          2m59s
pod/argocd-repo-server-98598b6c7-2hd6c   1/1     Running   0          2m58s
pod/argocd-application-controller-0      1/1     Running   0          2m57s
pod/argocd-server-5b4b7b868b-rjhwm       1/1     Running   0          2m58s

NAME                             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
service/argocd-dex-server        ClusterIP   10.43.177.214   <none>        5556/TCP,5557/TCP,5558/TCP   2m59s
service/argocd-metrics           ClusterIP   10.43.210.35    <none>        8082/TCP                     2m59s
service/argocd-redis             ClusterIP   10.43.85.139    <none>        6379/TCP                     2m59s
service/argocd-repo-server       ClusterIP   10.43.66.74     <none>        8081/TCP,8084/TCP            2m59s
service/argocd-server            ClusterIP   10.43.159.95    <none>        80/TCP,443/TCP               2m59s
service/argocd-server-metrics    ClusterIP   10.43.25.95     <none>        8083/TCP                     2m59s
service/argocd-server-nodeport   NodePort    10.43.114.166   <none>        8080:30443/TCP               2m52s

NAME                                 READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/argocd-redis         1/1     1            1           2m59s
deployment.apps/argocd-dex-server    1/1     1            1           2m59s
deployment.apps/argocd-repo-server   1/1     1            1           2m59s
deployment.apps/argocd-server        1/1     1            1           2m58s

NAME                                           DESIRED   CURRENT   READY   AGE
replicaset.apps/argocd-redis-5b6967fdfc        1         1         1       2m59s
replicaset.apps/argocd-dex-server-5fc596bcdd   1         1         1       2m59s
replicaset.apps/argocd-repo-server-98598b6c7   1         1         1       2m58s
replicaset.apps/argocd-server-5b4b7b868b       1         1         1       2m58s

NAME                                             READY   AGE
statefulset.apps/argocd-application-controller   1/1     2m58s
root@kubernetes-vm:~/workdir# 











# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Importante
You need a Gateway or a Service Mesh for traffic splits in Canary deployments
















# Install Ambassador

Blue/Green deployments can work on any vanilla Kubernetes cluster. But for Canary deployments you need a smart service layer that can gradually shift traffic to the canary pods while still keeping the rest of the traffic to the old/stable pods.

Argo Rollouts supports several service meshes and gateways for this purpose.

In this lesson we will use the popular Ambassador API Gateway for Kubernetes to split live traffic between the canary and old/stable version.

We installed Argo CD for you and you can login in the UI tab.

Click the "New app" button on the top left and fill the following details:

    application name : ambassador
    project: default
    SYNC POLICY: automatic
    AUTO-CREATE Namespace: enabled
    repository URL: https://github.com/codefresh-contrib/gitops-certification-examples
    path: ./ambassador-chart
    Cluster: https://kubernetes.default.svc (this is the same cluster where ArgoCD is installed)
    Namespace: ambassador

Leave all the other values empty or with default selections. Finally click the Create button. The Ambassador Gateway will be installed on the cluster.

Notice that we are not using the default namespace, but a brand new one. It is imperative that you select the "auto-create namespace" option.

auto-create

If you don't select this option, ArgoCD will not find the namespace and deployment will fail. Delete the application and create it again if you have a deployment issue.

You can also manually do if you forgot it in the UI:

kubectl create namespace ambassador

Try syncing again the application.

Again, you can see that GitOps is not only useful for your own applications but also for other supporting applications as well.

Wait some time until the gateway is fully synced and the deployment is marked as Healthy.

Once you are ready to proceed, press Check.















Installing the Argo Rollouts CLI...














# The first deployment

The Argo Rollouts controller is running now on the cluster and it is ready to handle deployments.

Lets deploy our application. Since this is the first deployment, there is no previous version and thus a normal deployment will take place.

We installed Argo CD for you and you can login in the UI tab.

Click the "New app" button on the top left and fill the following details

    application name : demo
    project: default
    SYNC POLICY: Manual
    repository URL: https://github.com/codefresh-contrib/gitops-certification-examples
    path: ./canary-app
    Cluster: https://kubernetes.default.svc (this is the same cluster where ArgoCD is installed)
    Namespace: default

Leave all the other values empty or with default selections.

Finally click the Create button. The application entry will appear in the main dashboard. Click on it.

The application will be initially "Out of Sync". Press the sync button to sync it and wait until it is healthy.

The application is deployed and you can see it in the "Live traffic" tab. You should see the following.

version1

Argo Rollouts also has an optional CLI that can be used for monitoring and promoting deployments

We have already installed kubectl argo rollouts for you in this exercise. And we can use it to monitor the first deployment

Run the following

kubectl argo rollouts list rollouts
kubectl argo rollouts status simple-rollout
kubectl argo rollouts get rollout simple-rollout

The last command shows the status of the rollout. Since this is the first version there is only one replicaset with ten pods.

You can also see this with

kubectl get rs

Once you are ready to proceed, press Check.


- ANTES:

root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# kubectl argo rollouts list rollouts
No resources found.
root@kubernetes-vm:~/workdir# kubectl argo rollouts status simple-rollout
Error: rollout.argoproj.io "simple-rollout" not found
root@kubernetes-vm:~/workdir# kubectl argo rollouts get rollout simple-rollout
Error: rollout.argoproj.io "simple-rollout" not found
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# kubectl get rs
No resources found in default namespace.
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 



- DEPOIS

root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# kubectl argo rollouts list rollouts
NAME            STRATEGY   STATUS        STEP  SET-WEIGHT  READY  DESIRED  UP-TO-DATE  AVAILABLE
simple-rollout  Canary     Healthy       6/6   100         10/10  10       10          10       
root@kubernetes-vm:~/workdir# kubectl argo rollouts status simple-rollout

Healthy
root@kubernetes-vm:~/workdir# kubectl argo rollouts get rollout simple-rollout
Name:            simple-rollout
Namespace:       default
Status:          ✔ Healthy
Strategy:        Canary
  Step:          6/6
  SetWeight:     100
  ActualWeight:  100
Images:          docker.io/kostiscodefresh/gitops-canary-app:v1.0 (stable)
Replicas:
  Desired:       10
  Current:       10
  Updated:       10
  Ready:         10
  Available:     10

NAME                                        KIND        STATUS     AGE  INFO
⟳ simple-rollout                            Rollout     ✔ Healthy  84s  
└──# revision:1                                                         
   └──⧉ simple-rollout-67df66795d           ReplicaSet  ✔ Healthy  64s  stable
      ├──□ simple-rollout-67df66795d-m96w5  Pod         ✔ Running  64s  ready:1/1
      ├──□ simple-rollout-67df66795d-mbdb2  Pod         ✔ Running  64s  ready:1/1
      ├──□ simple-rollout-67df66795d-zpxk6  Pod         ✔ Running  64s  ready:1/1
      ├──□ simple-rollout-67df66795d-l6fgl  Pod         ✔ Running  63s  ready:1/1
      ├──□ simple-rollout-67df66795d-mjv4p  Pod         ✔ Running  63s  ready:1/1
      ├──□ simple-rollout-67df66795d-rgnp9  Pod         ✔ Running  63s  ready:1/1
      ├──□ simple-rollout-67df66795d-89pr9  Pod         ✔ Running  62s  ready:1/1
      ├──□ simple-rollout-67df66795d-k9fc2  Pod         ✔ Running  62s  ready:1/1
      ├──□ simple-rollout-67df66795d-m2tqg  Pod         ✔ Running  62s  ready:1/1
      └──□ simple-rollout-67df66795d-nhpqg  Pod         ✔ Running  62s  ready:1/1
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
root@kubernetes-vm:~/workdir# kubectl get rs
NAME                        DESIRED   CURRENT   READY   AGE
simple-rollout-67df66795d   10        10        10      75s
root@kubernetes-vm:~/workdir#











# IMPORTANTE
The Argo Rollouts controller will only activate if a change happens in a Rollout resource.














# Canary deployments

We are now ready to have a Canary deployment with the next version.

Change the container image of the rollout to the next version with:

kubectl argo rollouts set image simple-rollout webserver-simple=docker.io/kostiscodefresh/gitops-canary-app:v2.0

We are using kubectl just for illustration purposes. Normally you should follow the GitOps principles and perform a commit to the Git repo of the application. But just for this exercise we will do all actions manually so that you have time to see what happens.

Enter the following to see what Argo Rollouts is doing behind the scenes

kubectl argo rollouts get rollout simple-rollout

After you change the image the following things happen

    Argo Rollouts creates another replicaset with the new version
    The old version is still there and gets live/active traffic
    The canary version gets 30% of the live traffic.
    ArgoCD will mark the application as out-of-sync
    ArgoCD will also mark the health of the application as "suspended" because we have setup the new color to wait

Notice that even though the next version of our application is already deployed, live traffic goes to both new/old versions. You can verify this by looking at the "live traffic" tab.

version1

At this point the deployment is suspended because we have used the pause properties in the definition of the rollout.

To manually promote the deployment and switch 60% to the new version enter:

kubectl argo rollouts promote simple-rollout

Then monitor again the rollout with

kubectl argo rollouts get rollout simple-rollout --watch

Repeat the same process three more time to send 100% to the canary version. Notice that at each step you can also look at the "stable" and "unstable" tabs and you can see that you can keep both old and new versions in play. This is useful if for some reason you have other applications in the cluster that always need to be pointed to the old or new version while a canary is in progress.

After a while you should see the pods of the old version getting destroyed.

Now all live traffic goes to the new version as can be seen from the "live traffic" tab.

version1

The deployment has finished successfully now.
Finish

Once you are ready to finish the track, press Check.





# PENDENTE
- Rodar live denovo
- Pegar comandos



# DIA 29/10/2022

