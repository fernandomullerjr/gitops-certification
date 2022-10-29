
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



~~~~bash

root@kubernetes-vm:~/workdir# kubectl get pods -A
NAMESPACE              NAME                                         READY   STATUS      RESTARTS   AGE
kubernetes-dashboard   dashboard-metrics-scraper-856586f554-6nrll   1/1     Running     0          3m40s
kube-system            coredns-7448499f4d-skhn9                     1/1     Running     0          3m40s
kube-system            local-path-provisioner-5ff76fc89d-jl6jm      1/1     Running     0          3m40s
kube-system            metrics-server-86cbb8457f-x2j7g              1/1     Running     0          3m40s
kubernetes-dashboard   kubernetes-dashboard-5949b5c856-cmht4        1/1     Running     0          3m40s
kube-system            helm-install-traefik-crd-jw4m5               0/1     Completed   0          3m41s
kube-system            helm-install-traefik-pq9kk                   0/1     Completed   2          3m41s
argocd                 argocd-redis-5b6967fdfc-5q2xf                1/1     Running     0          2m45s
argocd                 argocd-repo-server-98598b6c7-g4nvv           1/1     Running     0          2m45s
argocd                 argocd-application-controller-0              1/1     Running     0          2m44s
argocd                 argocd-dex-server-5fc596bcdd-8fvh7           1/1     Running     0          2m45s
kube-system            svclb-traefik-hp9bd                          2/2     Running     0          2m36s
kube-system            traefik-97b44b794-q5cvn                      1/1     Running     0          2m36s
argocd                 argocd-server-5b4b7b868b-fw7xr               1/1     Running     0          2m45s
root@kubernetes-vm:~/workdir# 

root@kubernetes-vm:~/workdir# kubectl get ns
NAME                   STATUS   AGE
default                Active   6m40s
kube-system            Active   6m40s
kube-public            Active   6m40s
kube-node-lease        Active   6m40s
kubernetes-dashboard   Active   6m35s
argocd                 Active   5m24s
root@kubernetes-vm:~/workdir# 


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
~~~~














# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Importante
You need a Gateway or a Service Mesh for traffic splits in Canary deployments
















# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
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


~~~~bash
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# kubectl get pods -A
NAMESPACE              NAME                                         READY   STATUS      RESTARTS   AGE
kubernetes-dashboard   dashboard-metrics-scraper-856586f554-6nrll   1/1     Running     0          9m58s
kube-system            coredns-7448499f4d-skhn9                     1/1     Running     0          9m58s
kube-system            local-path-provisioner-5ff76fc89d-jl6jm      1/1     Running     0          9m58s
kube-system            metrics-server-86cbb8457f-x2j7g              1/1     Running     0          9m58s
kubernetes-dashboard   kubernetes-dashboard-5949b5c856-cmht4        1/1     Running     0          9m58s
kube-system            helm-install-traefik-crd-jw4m5               0/1     Completed   0          9m59s
kube-system            helm-install-traefik-pq9kk                   0/1     Completed   2          9m59s
argocd                 argocd-redis-5b6967fdfc-5q2xf                1/1     Running     0          9m3s
argocd                 argocd-repo-server-98598b6c7-g4nvv           1/1     Running     0          9m3s
argocd                 argocd-application-controller-0              1/1     Running     0          9m2s
argocd                 argocd-dex-server-5fc596bcdd-8fvh7           1/1     Running     0          9m3s
kube-system            svclb-traefik-hp9bd                          2/2     Running     0          8m54s
kube-system            traefik-97b44b794-q5cvn                      1/1     Running     0          8m54s
argocd                 argocd-server-5b4b7b868b-fw7xr               1/1     Running     0          9m3s
argo-rollouts          argo-rollouts-588d458875-dgxrw               1/1     Running     0          3m10s
ambassador             ambassador-agent-7fc5b7f889-fhv2r            1/1     Running     0          66s
ambassador             ambassador-redis-64b7c668b9-gwlrs            1/1     Running     0          66s
ambassador             ambassador-7776cbd9b4-qlzcc                  1/1     Running     0          65s
ambassador             ambassador-7776cbd9b4-pb7zq                  1/1     Running     0          65s
ambassador             ambassador-7776cbd9b4-bhbqh                  1/1     Running     0          66s
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# kubectl get svc -A
NAMESPACE              NAME                        TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
default                kubernetes                  ClusterIP      10.43.0.1       <none>        443/TCP                      10m
kubernetes-dashboard   kubernetes-dashboard        ClusterIP      10.43.99.255    <none>        443/TCP                      10m
kube-system            kube-dns                    ClusterIP      10.43.0.10      <none>        53/UDP,53/TCP,9153/TCP       10m
kubernetes-dashboard   dashboard-metrics-scraper   ClusterIP      10.43.116.70    <none>        8000/TCP                     10m
kube-system            metrics-server              ClusterIP      10.43.78.128    <none>        443/TCP                      10m
argocd                 argocd-dex-server           ClusterIP      10.43.172.19    <none>        5556/TCP,5557/TCP,5558/TCP   9m9s
argocd                 argocd-metrics              ClusterIP      10.43.163.16    <none>        8082/TCP                     9m9s
argocd                 argocd-redis                ClusterIP      10.43.232.32    <none>        6379/TCP                     9m9s
argocd                 argocd-repo-server          ClusterIP      10.43.119.52    <none>        8081/TCP,8084/TCP            9m9s
argocd                 argocd-server               ClusterIP      10.43.172.32    <none>        80/TCP,443/TCP               9m9s
argocd                 argocd-server-metrics       ClusterIP      10.43.165.127   <none>        8083/TCP                     9m9s
argocd                 argocd-server-nodeport      NodePort       10.43.172.138   <none>        8080:30443/TCP               9m1s
kube-system            traefik                     LoadBalancer   10.43.189.28    10.5.0.144    80:31823/TCP,443:32513/TCP   9m
argo-rollouts          argo-rollouts-metrics       ClusterIP      10.43.52.14     <none>        8090/TCP                     3m17s
ambassador             ambassador-redis            ClusterIP      10.43.242.210   <none>        6379/TCP                     74s
ambassador             ambassador-admin            ClusterIP      10.43.121.163   <none>        8877/TCP,8005/TCP            74s
ambassador             ambassador                  NodePort       10.43.176.217   <none>        80:32080/TCP,443:32443/TCP   73s
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# date
Sat Oct 29 22:21:19 UTC 2022
root@kubernetes-vm:~/workdir# 
~~~~










# 
Installing the Argo Rollouts CLI...















# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
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

~~~~bash
root@kubernetes-vm:~/workdir# kubectl argo rollouts list rollouts
No resources found.
root@kubernetes-vm:~/workdir# kubectl argo rollouts status simple-rollout
Error: rollout.argoproj.io "simple-rollout" not found
root@kubernetes-vm:~/workdir# kubectl argo rollouts get rollout simple-rollout
Error: rollout.argoproj.io "simple-rollout" not found
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# kubectl get rs
No resources found in default namespace.
root@kubernetes-vm:~/workdir# 
~~~~


- DEPOIS

~~~~bash
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

NAME                                        KIND        STATUS     AGE   INFO
⟳ simple-rollout                            Rollout     ✔ Healthy  109s  
└──# revision:1                                                          
   └──⧉ simple-rollout-67df66795d           ReplicaSet  ✔ Healthy  109s  stable
      ├──□ simple-rollout-67df66795d-s27zr  Pod         ✔ Running  109s  ready:1/1
      ├──□ simple-rollout-67df66795d-t7v2w  Pod         ✔ Running  109s  ready:1/1
      ├──□ simple-rollout-67df66795d-z6kmn  Pod         ✔ Running  109s  ready:1/1
      ├──□ simple-rollout-67df66795d-4v6n4  Pod         ✔ Running  106s  ready:1/1
      ├──□ simple-rollout-67df66795d-55flq  Pod         ✔ Running  106s  ready:1/1
      ├──□ simple-rollout-67df66795d-7kxxg  Pod         ✔ Running  106s  ready:1/1
      ├──□ simple-rollout-67df66795d-b4v6f  Pod         ✔ Running  106s  ready:1/1
      ├──□ simple-rollout-67df66795d-f79g4  Pod         ✔ Running  106s  ready:1/1
      ├──□ simple-rollout-67df66795d-gb6k8  Pod         ✔ Running  106s  ready:1/1
      └──□ simple-rollout-67df66795d-tvkd9  Pod         ✔ Running  106s  ready:1/1
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# kubectl get rs
NAME                        DESIRED   CURRENT   READY   AGE
simple-rollout-67df66795d   10        10        10      2m55s
root@kubernetes-vm:~/workdir# 
~~~~









# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# IMPORTANTE
The Argo Rollouts controller will only activate if a change happens in a Rollout resource.














# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
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


~~~~bash
root@kubernetes-vm:~/workdir# kubectl argo rollouts set image simple-rollout webserver-simple=docker.io/kostiscodefresh/gitops-canary-app:v2.0
rollout "simple-rollout" image updated
root@kubernetes-vm:~/workdir# kubectl argo rollouts get rollout simple-rollout
Name:            simple-rollout
Namespace:       default
Status:          ॥ Paused
Message:         CanaryPauseStep
Strategy:        Canary
  Step:          1/6
  SetWeight:     30
  ActualWeight:  30
Images:          docker.io/kostiscodefresh/gitops-canary-app:v1.0 (stable)
                 docker.io/kostiscodefresh/gitops-canary-app:v2.0 (canary)
Replicas:
  Desired:       10
  Current:       13
  Updated:       3
  Ready:         13
  Available:     13

NAME                                        KIND        STATUS     AGE    INFO
⟳ simple-rollout                            Rollout     ॥ Paused   5m25s  
├──# revision:2                                                           
│  └──⧉ simple-rollout-7f9d9f9f8c           ReplicaSet  ✔ Healthy  10s    canary
│     ├──□ simple-rollout-7f9d9f9f8c-cxqcn  Pod         ✔ Running  10s    ready:1/1
│     ├──□ simple-rollout-7f9d9f9f8c-kww7p  Pod         ✔ Running  10s    ready:1/1
│     └──□ simple-rollout-7f9d9f9f8c-th9c4  Pod         ✔ Running  10s    ready:1/1
└──# revision:1                                                           
   └──⧉ simple-rollout-67df66795d           ReplicaSet  ✔ Healthy  5m25s  stable
      ├──□ simple-rollout-67df66795d-s27zr  Pod         ✔ Running  5m25s  ready:1/1
      ├──□ simple-rollout-67df66795d-t7v2w  Pod         ✔ Running  5m25s  ready:1/1
      ├──□ simple-rollout-67df66795d-z6kmn  Pod         ✔ Running  5m25s  ready:1/1
      ├──□ simple-rollout-67df66795d-4v6n4  Pod         ✔ Running  5m22s  ready:1/1
      ├──□ simple-rollout-67df66795d-55flq  Pod         ✔ Running  5m22s  ready:1/1
      ├──□ simple-rollout-67df66795d-7kxxg  Pod         ✔ Running  5m22s  ready:1/1
      ├──□ simple-rollout-67df66795d-b4v6f  Pod         ✔ Running  5m22s  ready:1/1
      ├──□ simple-rollout-67df66795d-f79g4  Pod         ✔ Running  5m22s  ready:1/1
      ├──□ simple-rollout-67df66795d-gb6k8  Pod         ✔ Running  5m22s  ready:1/1
      └──□ simple-rollout-67df66795d-tvkd9  Pod         ✔ Running  5m22s  ready:1/1
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
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# kubectl argo rollouts get rollout simple-rollout
Name:            simple-rollout
Namespace:       default
Status:          ॥ Paused
Message:         CanaryPauseStep
Strategy:        Canary
  Step:          1/6
  SetWeight:     30
  ActualWeight:  30
Images:          docker.io/kostiscodefresh/gitops-canary-app:v1.0 (stable)
                 docker.io/kostiscodefresh/gitops-canary-app:v2.0 (canary)
Replicas:
  Desired:       10
  Current:       13
  Updated:       3
  Ready:         13
Name:            simple-rollout
Namespace:       default
Status:          ॥ Paused
Message:         CanaryPauseStep
Strategy:        Canary
  Step:          3/6
  SetWeight:     60
  ActualWeight:  60
Images:          docker.io/kostiscodefresh/gitops-canary-app:v1.0 (stable)
                 docker.io/kostiscodefresh/gitops-canary-app:v2.0 (canary)
Name:            simple-rollout
Namespace:       default
Status:          ॥ Paused
Message:         CanaryPauseStep
Strategy:        Canary
  Step:          3/6
  SetWeight:     60
  ActualWeight:  60
Images:          docker.io/kostiscodefresh/gitops-canary-app:v1.0 (stable)
                 docker.io/kostiscodefresh/gitops-canary-app:v2.0 (canary)
Replicas:
  Desired:       10
  Current:       16
  Updated:       6
  Ready:         16
  Available:     16

NAME                                        KIND        STATUS     AGE    INFO
⟳ simple-rollout                            Rollout     ॥ Paused   8m23s  
├──# revision:2                                                           
│  └──⧉ simple-rollout-7f9d9f9f8c           ReplicaSet  ✔ Healthy  3m8s   canary
│     ├──□ simple-rollout-7f9d9f9f8c-cxqcn  Pod         ✔ Running  3m8s   ready:1/1
Name:            simple-rollout
Namespace:       default
Status:          ◌ Progressing
Name:            simple-rollout
Namespace:       default
Status:          ◌ Progressing
Name:            simple-rollout
Namespace:       default
Status:          ◌ Progressing
Name:            simple-rollout
Namespace:       default
Status:          ◌ Progressing
Name:            simple-rollout
Namespace:       default
Status:          ॥ Paused
Name:            simple-rollout
Namespace:       default
Status:          ॥ Paused
Name:            simple-rollout
Namespace:       default
Status:          ॥ Paused
Name:            simple-rollout
Namespace:       default
Status:          ॥ Paused
Name:            simple-rollout
Namespace:       default
Status:          ॥ Paused
Name:            simple-rollout
Namespace:       default
Status:          ॥ Paused
Message:         CanaryPauseStep
Strategy:        Canary
  Step:          5/6
  SetWeight:     100
  ActualWeight:  100
Images:          docker.io/kostiscodefresh/gitops-canary-app:v1.0 (stable)
                 docker.io/kostiscodefresh/gitops-canary-app:v2.0 (canary)
Replicas:
  Desired:       10
  Current:       20
  Updated:       10
  Ready:         20
  Available:     20

NAME                                        KIND        STATUS     AGE    INFO
Name:            simple-rollout
Namespace:       default
Status:          ◌ Progressing
Name:            simple-rollout
Namespace:       default
Status:          ◌ Progressing
Name:            simple-rollout
Namespace:       default
Name:            simple-rollout
Namespace:       default
Status:          ✔ Healthy
Strategy:        Canary
  Step:          6/6
  SetWeight:     100
  ActualWeight:  100
Images:          docker.io/kostiscodefresh/gitops-canary-app:v1.0
                 docker.io/kostiscodefresh/gitops-canary-app:v2.0 (stable)
Replicas:
  Desired:       10
  Current:       20
  Updated:       10
  Ready:         20
  Available:     20

NAME                                        KIND        STATUS     AGE    INFO
⟳ simple-rollout                            Rollout     ✔ Healthy  9m40s  
├──# revision:2                                                           
│  └──⧉ simple-rollout-7f9d9f9f8c           ReplicaSet  ✔ Healthy  4m25s  stable
│     ├──□ simple-rollout-7f9d9f9f8c-cxqcn  Pod         ✔ Running  4m25s  ready:1/1
│     ├──□ simple-rollout-7f9d9f9f8c-kww7p  Pod         ✔ Running  4m25s  ready:1/1
│     ├──□ simple-rollout-7f9d9f9f8c-th9c4  Pod         ✔ Running  4m25s  ready:1/1
│     ├──□ simple-rollout-7f9d9f9f8c-7p95p  Pod         ✔ Running  90s    ready:1/1
│     ├──□ simple-rollout-7f9d9f9f8c-8bv88  Pod         ✔ Running  90s    ready:1/1
│     ├──□ simple-rollout-7f9d9f9f8c-f55d9  Pod         ✔ Running  90s    ready:1/1
│     ├──□ simple-rollout-7f9d9f9f8c-4smvn  Pod         ✔ Running  26s    ready:1/1
│     ├──□ simple-rollout-7f9d9f9f8c-9sgzq  Pod         ✔ Running  26s    ready:1/1
│     ├──□ simple-rollout-7f9d9f9f8c-g6p8j  Pod         ✔ Running  26s    ready:1/1
│     └──□ simple-rollout-7f9d9f9f8c-jkn84  Pod         ✔ Running  26s    ready:1/1
└──# revision:1                                                           
   └──⧉ simple-rollout-67df66795d           ReplicaSet  ✔ Healthy  9m40s  
      ├──□ simple-rollout-67df66795d-s27zr  Pod         ✔ Running  9m40s  ready:1/1
      ├──□ simple-rollout-67df66795d-t7v2w  Pod         ✔ Running  9m40s  ready:1/1
      ├──□ simple-rollout-67df66795d-z6kmn  Pod         ✔ Running  9m40s  ready:1/1
      ├──□ simple-rollout-67df66795d-4v6n4  Pod         ✔ Running  9m37s  ready:1/1
      ├──□ simple-rollout-67df66795d-55flq  Pod         ✔ Running  9m37s  ready:1/1
      ├──□ simple-rollout-67df66795d-7kxxg  Pod         ✔ Running  9m37s  ready:1/1
      ├──□ simple-rollout-67df66795d-b4v6f  Pod         ✔ Running  9m37s  ready:1/1
      ├──□ simple-rollout-67df66795d-f79g4  Pod         ✔ Running  9m37s  ready:1/1
      ├──□ simple-rollout-67df66795d-gb6k8  Pod         ✔ Running  9m37s  ready:1/1
      └──□ simple-rollout-67df66795d-tvkd9  Pod         ✔ Running  9m37s  ready:1/1
^C
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# kubectl argo rollouts promote simple-rollout
rollout 'simple-rollout' promoted
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
~~~~





# PENDENTE
- Rodar live denovo
- Pegar comandos



# DIA 29/10/2022

