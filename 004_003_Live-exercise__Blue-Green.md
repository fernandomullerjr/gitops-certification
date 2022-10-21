

# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Git push

git status
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git add .
git commit -m "Aula 004 - 003 - Live exercise Blue/Green"
git push
git status


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# 09 Blue/Green deployments

Blue/Green deployments with Argo Rollouts

Welcome

Install the Argo Rollouts controller

The first deployment

Blue/Green deployments









# Progressive delivery with Argo Rollouts

The example application is at https://github.com/codefresh-contrib/gitops-certification-examples/tree/main/blue-green-app.
Objectives

In this track, this is what you'll learn:

    Installation of the Argo Rollouts controller
    How to convert a Deployment to a Rollout
    How to perform blue/green deployments
    How to monitor the state of the deployment

Please wait while we boot the VM for you and start Kubernetes.










# Welcome

Our example application can be found at https://github.com/codefresh-contrib/gitops-certification-examples/tree/main/blue-green-app.

It is a very simple application with one deployment and one service. Notice that the deployment has been converted to a Rollout and has an extra section about the blue/green deployment options.

You can also see the full source code at source-code-canary if you want to understand how the application is rendering a web page.

Once you are ready to proceed, press Next.










Please wait while we setup Argo CD for you...

















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












# The first deployment

The Argo Rollouts controller is running now on the cluster and it is ready to handle deployments.

Lets deploy our application. Since this is the first deployment, there is no previous version and thus a normal deployment will take place.

We installed Argo CD for you and you can login in the UI tab.

Click the "New app" button on the top left and fill the following details

    application name : demo
    project: default
    SYNC POLICY: Manual
    repository URL: https://github.com/codefresh-contrib/gitops-certification-examples
    path: ./blue-green-app
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

The last command shows the status of the rollout. Since this is the first version there is only one replicaset with two pods.

You can also see this with

kubectl get rs

Once you are ready to proceed, press Check.






root@kubernetes-vm:~/workdir# kubectl argo rollouts list rollouts
NAME            STRATEGY   STATUS        STEP  SET-WEIGHT  READY  DESIRED  UP-TO-DATE  AVAILABLE
simple-rollout  BlueGreen  Healthy       -     -           2/2    2        2           2        
root@kubernetes-vm:~/workdir# kubectl argo rollouts status simple-rollout

Healthy
root@kubernetes-vm:~/workdir# kubectl argo rollouts get rollout simple-rollout
Name:            simple-rollout
Namespace:       default
Status:          ✔ Healthy
Strategy:        BlueGreen
Images:          docker.io/kostiscodefresh/gitops-canary-app:v1.0 (stable, active)
Replicas:
  Desired:       2
  Current:       2
  Updated:       2
  Ready:         2
  Available:     2

NAME                                       KIND        STATUS     AGE  INFO
⟳ simple-rollout                           Rollout     ✔ Healthy  38s  
└──# revision:1                                                        
   └──⧉ simple-rollout-b68b5bffb           ReplicaSet  ✔ Healthy  38s  stable,active
      ├──□ simple-rollout-b68b5bffb-wrcj6  Pod         ✔ Running  38s  ready:1/1
      └──□ simple-rollout-b68b5bffb-z9pm7  Pod         ✔ Running  38s  ready:1/1
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 



root@kubernetes-vm:~/workdir# kubectl get rs
NAME                       DESIRED   CURRENT   READY   AGE
simple-rollout-b68b5bffb   2         2         2       60s
root@kubernetes-vm:~/workdir# 





- The Argo Rollouts controller will only activate if a change happens in a Rollout resource.