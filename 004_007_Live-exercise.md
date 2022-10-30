
# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Git push

git status
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git add .
git commit -m "Aula 004 - 11 Argo CD and Argo Rollouts"
git push
git status


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# 




11 Argo CD and Argo Rollouts

Perform Canary deployments with GitOps

Welcome

Install the Argo Rollouts controller

Install Ambassador

The first deployment

Canary deployments with GitOps












# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Progressive delivery with Argo Rollouts and Argo CD

You need to have a GitHub account for this exercise.

The example application is at https://github.com/codefresh-contrib/gitops-certification-examples/tree/main/canary-app-timed. Fork this repository in your own account.
Objectives

In this track, this is what you'll learn:

    Installation of the Argo Rollouts controller
    How to install an API gateway
    How to convert a Deployment to a Rollout
    How to perform canary deployments
    How to monitor the state of the deployment

Please wait while we boot the VM for you and start Kubernetes.








# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Welcome

Our example application can be found at https://github.com/codefresh-contrib/gitops-certification-examples/tree/main/canary-app-timed.

Make sure to fork it to your own account and note down the URL. It should be something like:

https://github.com/<your user>/gitops-certification-examples/

It is a very simple application with one deployment and one service. Notice that the deployment has been converted to a Rollout and has an extra section about the canary deployment options. It also has timed stages with the following pattern:

    30% of traffic will be sent to the canary
    After 2 minutes the canary will get 60% of traffic
    After 2 more minutes 100% of the traffic will be sent to the canary

You can also see the full source code at source-code-canary if you want to understand how the application is rendering a web page.
<https://github.com/codefresh-contrib/gitops-certification-examples/tree/main/source-code-canary>

Once you are ready to proceed, press Next.











# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
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
    repository URL: https://github.com/<your user>/gitops-certification-examples
    path: ./canary-app-timed
    Cluster: https://kubernetes.default.svc (this is the same cluster where ArgoCD is installed)
    Namespace: default


https://github.com/fernandomullerjr/gitops-certification-examples


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



~~~~bash
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# kubectl argo rollouts list rollouts
NAME            STRATEGY   STATUS        STEP  SET-WEIGHT  READY  DESIRED  UP-TO-DATE  AVAILABLE
simple-rollout  Canary     Healthy       5/5   100         10/10  10       10          10       
root@kubernetes-vm:~/workdir# kubectl argo rollouts status simple-rollout
Healthy
root@kubernetes-vm:~/workdir# kubectl argo rollouts get rollout simple-rollout
Name:            simple-rollout
Namespace:       default
Status:          ✔ Healthy
Strategy:        Canary
  Step:          5/5
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
⟳ simple-rollout                            Rollout     ✔ Healthy  62s  
└──# revision:1                                                         
   └──⧉ simple-rollout-67df66795d           ReplicaSet  ✔ Healthy  62s  stable
      ├──□ simple-rollout-67df66795d-h227c  Pod         ✔ Running  61s  ready:1/1
      ├──□ simple-rollout-67df66795d-tg85h  Pod         ✔ Running  61s  ready:1/1
      ├──□ simple-rollout-67df66795d-xxb9v  Pod         ✔ Running  61s  ready:1/1
      ├──□ simple-rollout-67df66795d-qhrvh  Pod         ✔ Running  60s  ready:1/1
      ├──□ simple-rollout-67df66795d-s8kzf  Pod         ✔ Running  60s  ready:1/1
      ├──□ simple-rollout-67df66795d-v7vmg  Pod         ✔ Running  60s  ready:1/1
      ├──□ simple-rollout-67df66795d-4tnpt  Pod         ✔ Running  59s  ready:1/1
      ├──□ simple-rollout-67df66795d-775mc  Pod         ✔ Running  59s  ready:1/1
      ├──□ simple-rollout-67df66795d-n9xpm  Pod         ✔ Running  59s  ready:1/1
      └──□ simple-rollout-67df66795d-vckgl  Pod         ✔ Running  59s  ready:1/1
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# kubectl get rs
NAME                        DESIRED   CURRENT   READY   AGE
simple-rollout-67df66795d   10        10        10      75s
root@kubernetes-vm:~/workdir# 
~~~~
















# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
#
The Argo Rollouts controller will only activate if a change happens in a Rollout resource.













# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Canary deployments with GitOps

We are now ready to have a Canary deployment with the next version.

Go to your Git repository and change the file gitops-certification-examples/blob/main/canary-app-timed/rollout.yaml Change line 19 and enter v2.0 as the container image. Commit and push your changes.

Go into the ArgoCD UI and press the sync button to sync it.

Argo Rollouts will follow the pattern described in the rollout and do the following:

    30% of traffic will be sent to the canary
    After 2 minutes the canary will get 60% of traffic
    After 2 more minutes 100% of the traffic will be sent to the canary

You can monitor the progress by using the command line

kubectl argo rollouts get rollout simple-rollout --watch

Notice that even though the next version of our application is already deployed, live traffic goes to both new/old versions. You can verify this by looking at the "live traffic" tab.

version1

Notice that at each step you can also look at the "stable" and "unstable" tabs and you can see that you can keep both old and new versions in play. This is useful if for some reason you have other applications in the cluster that always need to be pointed to the old or new version while a canary is in progress.

After all 4 minutes pass the canary is complete

Now all live traffic goes to the new version as can be seen from the "live traffic" tab.

version1

The deployment has finished successfully now.
Finish

Once you are ready to finish the track, press Check.






fernando@debian10x64:~/cursos/gitops-certification-examples$
fernando@debian10x64:~/cursos/gitops-certification-examples$ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   canary-app-timed/rollout.yaml

no changes added to commit (use "git add" and/or "git commit -a")
fernando@debian10x64:~/cursos/gitops-certification-examples$ git add canary-app-timed/rollout.yaml
fernando@debian10x64:~/cursos/gitops-certification-examples$ git commit -m "ajustado o Rollout de v1 para v2."
[main ed7439d] ajustado o Rollout de v1 para v2.
 1 file changed, 36 insertions(+), 36 deletions(-)
 rewrite canary-app-timed/rollout.yaml (96%)
fernando@debian10x64:~/cursos/gitops-certification-examples$ git push
Enumerating objects: 7, done.
Counting objects: 100% (7/7), done.
Delta compression using up to 8 threads
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 607 bytes | 607.00 KiB/s, done.
Total 4 (delta 3), reused 0 (delta 0)
remote: Resolving deltas: 100% (3/3), completed with 3 local objects.
To github.com:fernandomullerjr/gitops-certification-examples.git
   bb2de9a..ed7439d  main -> main
fernando@debian10x64:~/cursos/gitops-certification-examples$






root@kubernetes-vm:~/workdir# kubectl argo rollouts get rollout simple-rollout
Name:            simple-rollout
Namespace:       default
Status:          ॥ Paused
Message:         CanaryPauseStep
Strategy:        Canary
  Step:          1/5
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
⟳ simple-rollout                            Rollout     ॥ Paused   6m49s  
├──# revision:2                                                           
│  └──⧉ simple-rollout-7f9d9f9f8c           ReplicaSet  ✔ Healthy  37s    canary
│     ├──□ simple-rollout-7f9d9f9f8c-29mx2  Pod         ✔ Running  37s    ready:1/1
│     ├──□ simple-rollout-7f9d9f9f8c-k7d9x  Pod         ✔ Running  37s    ready:1/1
│     └──□ simple-rollout-7f9d9f9f8c-wvwh2  Pod         ✔ Running  37s    ready:1/1
└──# revision:1                                                           
   └──⧉ simple-rollout-67df66795d           ReplicaSet  ✔ Healthy  6m49s  stable
      ├──□ simple-rollout-67df66795d-h227c  Pod         ✔ Running  6m48s  ready:1/1
      ├──□ simple-rollout-67df66795d-tg85h  Pod         ✔ Running  6m48s  ready:1/1
      ├──□ simple-rollout-67df66795d-xxb9v  Pod         ✔ Running  6m48s  ready:1/1
      ├──□ simple-rollout-67df66795d-qhrvh  Pod         ✔ Running  6m47s  ready:1/1
      ├──□ simple-rollout-67df66795d-s8kzf  Pod         ✔ Running  6m47s  ready:1/1
      ├──□ simple-rollout-67df66795d-v7vmg  Pod         ✔ Running  6m47s  ready:1/1
      ├──□ simple-rollout-67df66795d-4tnpt  Pod         ✔ Running  6m46s  ready:1/1
      ├──□ simple-rollout-67df66795d-775mc  Pod         ✔ Running  6m46s  ready:1/1
      ├──□ simple-rollout-67df66795d-n9xpm  Pod         ✔ Running  6m46s  ready:1/1
      └──□ simple-rollout-67df66795d-vckgl  Pod         ✔ Running  6m46s  ready:1/1
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# date
Sat Oct 29 23:28:24 UTC 2022
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
  Step:          1/5
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
⟳ simple-rollout                            Rollout     ॥ Paused   7m11s  
├──# revision:2                                                           
│  └──⧉ simple-rollout-7f9d9f9f8c           ReplicaSet  ✔ Healthy  59s    canary
│     ├──□ simple-rollout-7f9d9f9f8c-29mx2  Pod         ✔ Running  59s    ready:1/1
│     ├──□ simple-rollout-7f9d9f9f8c-k7d9x  Pod         ✔ Running  59s    ready:1/1
│     └──□ simple-rollout-7f9d9f9f8c-wvwh2  Pod         ✔ Running  59s    ready:1/1
└──# revision:1                                                           
   └──⧉ simple-rollout-67df66795d           ReplicaSet  ✔ Healthy  7m11s  stable
      ├──□ simple-rollout-67df66795d-h227c  Pod         ✔ Running  7m10s  ready:1/1
      ├──□ simple-rollout-67df66795d-tg85h  Pod         ✔ Running  7m10s  ready:1/1
      ├──□ simple-rollout-67df66795d-xxb9v  Pod         ✔ Running  7m10s  ready:1/1
      ├──□ simple-rollout-67df66795d-qhrvh  Pod         ✔ Running  7m9s   ready:1/1
      ├──□ simple-rollout-67df66795d-s8kzf  Pod         ✔ Running  7m9s   ready:1/1
      ├──□ simple-rollout-67df66795d-v7vmg  Pod         ✔ Running  7m9s   ready:1/1
      ├──□ simple-rollout-67df66795d-4tnpt  Pod         ✔ Running  7m8s   ready:1/1
      ├──□ simple-rollout-67df66795d-775mc  Pod         ✔ Running  7m8s   ready:1/1
      ├──□ simple-rollout-67df66795d-n9xpm  Pod         ✔ Running  7m8s   ready:1/1
      └──□ simple-rollout-67df66795d-vckgl  Pod         ✔ Running  7m8s   ready:1/1
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# date
Sat Oct 29 23:29:01 UTC 2022
root@kubernetes-vm:~/workdir# date
Sat Oct 29 23:30:45 UTC 2022
root@kubernetes-vm:~/workdir# kubectl argo rollouts get rollout simple-rollout
Name:            simple-rollout
Namespace:       default
Status:          ॥ Paused
Message:         CanaryPauseStep
Strategy:        Canary
  Step:          3/5
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
⟳ simple-rollout                            Rollout     ॥ Paused   9m21s  
├──# revision:2                                                           
│  └──⧉ simple-rollout-7f9d9f9f8c           ReplicaSet  ✔ Healthy  3m9s   canary
│     ├──□ simple-rollout-7f9d9f9f8c-29mx2  Pod         ✔ Running  3m9s   ready:1/1
│     ├──□ simple-rollout-7f9d9f9f8c-k7d9x  Pod         ✔ Running  3m9s   ready:1/1
│     ├──□ simple-rollout-7f9d9f9f8c-wvwh2  Pod         ✔ Running  3m9s   ready:1/1
│     ├──□ simple-rollout-7f9d9f9f8c-cf7ll  Pod         ✔ Running  58s    ready:1/1
│     ├──□ simple-rollout-7f9d9f9f8c-lvplj  Pod         ✔ Running  58s    ready:1/1
│     └──□ simple-rollout-7f9d9f9f8c-vm4dw  Pod         ✔ Running  58s    ready:1/1
└──# revision:1                                                           
   └──⧉ simple-rollout-67df66795d           ReplicaSet  ✔ Healthy  9m21s  stable
      ├──□ simple-rollout-67df66795d-h227c  Pod         ✔ Running  9m20s  ready:1/1
      ├──□ simple-rollout-67df66795d-tg85h  Pod         ✔ Running  9m20s  ready:1/1
      ├──□ simple-rollout-67df66795d-xxb9v  Pod         ✔ Running  9m20s  ready:1/1
      ├──□ simple-rollout-67df66795d-qhrvh  Pod         ✔ Running  9m19s  ready:1/1
      ├──□ simple-rollout-67df66795d-s8kzf  Pod         ✔ Running  9m19s  ready:1/1
      ├──□ simple-rollout-67df66795d-v7vmg  Pod         ✔ Running  9m19s  ready:1/1
      ├──□ simple-rollout-67df66795d-4tnpt  Pod         ✔ Running  9m18s  ready:1/1
      ├──□ simple-rollout-67df66795d-775mc  Pod         ✔ Running  9m18s  ready:1/1
      ├──□ simple-rollout-67df66795d-n9xpm  Pod         ✔ Running  9m18s  ready:1/1
      └──□ simple-rollout-67df66795d-vckgl  Pod         ✔ Running  9m18s  ready:1/1
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# date
Sat Oct 29 23:30:52 UTC 2022
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# date
Sat Oct 29 23:33:35 UTC 2022
root@kubernetes-vm:~/workdir# kubectl argo rollouts get rollout simple-rollout
Name:            simple-rollout
Namespace:       default
Status:          ✔ Healthy
Strategy:        Canary
  Step:          5/5
  SetWeight:     100
  ActualWeight:  100
Images:          docker.io/kostiscodefresh/gitops-canary-app:v2.0 (stable)
Replicas:
  Desired:       10
  Current:       10
  Updated:       10
  Ready:         10
  Available:     10

NAME                                        KIND        STATUS        AGE    INFO
⟳ simple-rollout                            Rollout     ✔ Healthy     12m    
├──# revision:2                                                              
│  └──⧉ simple-rollout-7f9d9f9f8c           ReplicaSet  ✔ Healthy     5m59s  stable
│     ├──□ simple-rollout-7f9d9f9f8c-29mx2  Pod         ✔ Running     5m59s  ready:1/1
│     ├──□ simple-rollout-7f9d9f9f8c-k7d9x  Pod         ✔ Running     5m59s  ready:1/1
│     ├──□ simple-rollout-7f9d9f9f8c-wvwh2  Pod         ✔ Running     5m59s  ready:1/1
│     ├──□ simple-rollout-7f9d9f9f8c-cf7ll  Pod         ✔ Running     3m48s  ready:1/1
│     ├──□ simple-rollout-7f9d9f9f8c-lvplj  Pod         ✔ Running     3m48s  ready:1/1
│     ├──□ simple-rollout-7f9d9f9f8c-vm4dw  Pod         ✔ Running     3m48s  ready:1/1
│     ├──□ simple-rollout-7f9d9f9f8c-6xs24  Pod         ✔ Running     103s   ready:1/1
│     ├──□ simple-rollout-7f9d9f9f8c-8p72w  Pod         ✔ Running     103s   ready:1/1
│     ├──□ simple-rollout-7f9d9f9f8c-9v9c7  Pod         ✔ Running     103s   ready:1/1
│     └──□ simple-rollout-7f9d9f9f8c-gmlpc  Pod         ✔ Running     103s   ready:1/1
└──# revision:1                                                              
   └──⧉ simple-rollout-67df66795d           ReplicaSet  • ScaledDown  12m    
root@kubernetes-vm:~/workdir# 