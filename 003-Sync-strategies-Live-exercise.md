
# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Git push
git status
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git add .
git commit -m "Aula 03 - 04 Sync strategies"
git push
git status


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# 04 Sync strategies

Learn about Argo CD sync strategies

    Welcome

    Auto-sync

    Deploy a new version with AutoSync

    Enabling self-heal

    Enable auto-prune




# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Argo CD Sync strategies

You need to have a GitHub account for this exercise.

The example application is at https://github.com/codefresh-contrib/gitops-certification-examples/tree/main/sync-strategies. Fork this repository in your own account.
Objectives

In this track, this is what you'll learn:

    How to use auto-sync
    How to use self-heal
    How to use auto-prune

Please wait while we boot the VM for you and start Kubernetes.


- Repo do Fernando
<https://github.com/fernandomullerjr/gitops-certification-examples/tree/main/sync-strategies>









# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Welcome

Our example application can be found at https://github.com/codefresh-contrib/gitops-certification-examples/tree/main/sync-strategies

Make sure to fork it to your own account and note down the URL. It should be something like:

https://github.com/<your user>/gitops-certification-examples/

Take a look at the Kubernetes manifests to understand what we will deploy. It is a very simple application with one deployment and one service

Once you are ready to proceed, press Next.







# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Auto-sync

We installed Argo CD for you and you can login in the UI tab.

The UI starts empty because nothing is deployed on our cluster. Click the "New app" button on the top left and fill the following details:

    application name : demo
    project: default
    SYNC POLICY: automatic
    repository URL: https://github.com/<your user>/gitops-certification-examples
    path: ./sync-strategies
    Cluster: https://kubernetes.default.svc (this is the same cluster where ArgoCD is installed)
    Namespace: default

Leave all the other values empty or with default selections. Finally click the Create button. The application entry will appear in the main dashboard. Click on it.

Since we have selected the auto-sync strategy, Argo CD will autodeploy the application right away (because the cluster state is not the same as the commit state)

You should see the following: synced app

You can also verify the application from the command line with:

kubectl get deployments

Once you are ready to proceed, press Check.







# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Deploy a new version with AutoSync

If you look at the "Deployed app" tab you will see that our application is at version 1.0

We want to deploy another version of our application. We will change the Git and see how Argo CD detects the change and automatically deploys (since we have set sync strategy to auto).

Perform a commit in your sync-strategies/deployment.yml file in your own account (You can use GitHub on another tab for this) and change the container tag at line 18 from v1.0 to v2.0

Normally Argo CD checks the state between Git and the cluster every 3 minutes on its own. Just to speed things up you should click manually on the application in the Argo CD dashboard and press the "Refresh" button

You should see the following. git-change

The application is already deployed. If you click the "refresh" button in the top right corner of the "Deployed App" tab you will see the version 2 is now live.

Once you are ready to proceed, press Check.



- Efetuado ajuste no Deployment, de v1 para v2:

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
        image: docker.io/kostiscodefresh/gitops-simple-app:v2.0
        ports:
        - containerPort: 8080
~~~~

- Abrindo a página na UI, consta o texto do v2 corretamente:
    I am an application running in Kubernetes. Now at Version 2.0






- Dica
Avoid manual changes in your cluster









# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Enabling self-heal

Step 1

Autosync will automatically deploy your application as soon as the Git repository changes. But if somebody makes a manual change in the cluster state, Argo CD will not do anything by default (it will still mark the application as out-of-sync).

You can enable the self-heal option to tell Argo CD to discard any changes done manually to the cluster. This is a great advantage as it always makes your environments bulletproof against ad-hoc changes via kubectl.

Execute the following

kubectl scale --replicas=3 deployment simple-deployment

You have manually changed the cluster. Now go the Argo CD dashboard and click on the application. In the Application screen click the top left "App Details" button.

Scroll down and find the "Sync policy section". Click on the "Self-heal" button to enable it. Answer ok to the confirmation dialog.

The manual change will be discarded and replicas are back to one .

You can now try changing anything on the cluster and all changes will always be discarded (as they are not part of Git).

Try in the terminal again.

kubectl scale --replicas=3 deployment simple-deployment
kubectl get deployment simple-deployment

Your application will always have 1 pod deployed. This is what the Git state says and therefore Argo CD automatically makes the cluster state the same.
Finish

Once you are ready to proceed, press Check.






kubectl scale --replicas=3 deployment simple-deployment

~~~~bash
root@kubernetes-vm:~/workdir# kubectl get pods -A
NAMESPACE              NAME                                         READY   STATUS      RESTARTS   AGE
kube-system            coredns-7448499f4d-xm24r                     1/1     Running     0          10m
kube-system            metrics-server-86cbb8457f-9f9wx              1/1     Running     0          10m
kubernetes-dashboard   dashboard-metrics-scraper-856586f554-s7vps   1/1     Running     0          10m
kube-system            local-path-provisioner-5ff76fc89d-sx872      1/1     Running     0          10m
kubernetes-dashboard   kubernetes-dashboard-5949b5c856-5ln9k        1/1     Running     0          10m
kube-system            helm-install-traefik-crd-bxpgs               0/1     Completed   0          10m
kube-system            helm-install-traefik-x7z5c                   0/1     Completed   1          10m
kube-system            svclb-traefik-7jx7q                          2/2     Running     0          9m20s
argocd                 argocd-repo-server-98598b6c7-kqqp7           1/1     Running     0          9m15s
argocd                 argocd-redis-5b6967fdfc-n7kk6                1/1     Running     0          9m15s
argocd                 argocd-dex-server-5fc596bcdd-ktqjt           1/1     Running     0          9m16s
kube-system            traefik-97b44b794-llbq8                      1/1     Running     0          9m21s
argocd                 argocd-application-controller-0              1/1     Running     0          9m12s
argocd                 argocd-server-5b4b7b868b-8x8qz               1/1     Running     0          9m14s
default                simple-deployment-5f64bf8ddf-h5fwb           1/1     Running     0          3m3s
root@kubernetes-vm:~/workdir# kubectl get rs
NAME                           DESIRED   CURRENT   READY   AGE
simple-deployment-5f64bf8ddf   1         1         1       3m11s
simple-deployment-9d88c574d    0         0         0       6m15s
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# kubectl scale --replicas=3 deployment simple-deployment
deployment.apps/simple-deployment scaled
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# kubectl get rs
NAME                           DESIRED   CURRENT   READY   AGE
simple-deployment-9d88c574d    0         0         0       6m24s
simple-deployment-5f64bf8ddf   3         3         3       3m20s
root@kubernetes-vm:~/workdir# 
~~~~




You have manually changed the cluster. Now go the Argo CD dashboard and click on the application. In the Application screen click the top left "App Details" button.

Scroll down and find the "Sync policy section". Click on the "Self-heal" button to enable it. Answer ok to the confirmation dialog.

The manual change will be discarded and replicas are back to one .

~~~~bash
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# kubectl get rs
NAME                           DESIRED   CURRENT   READY   AGE
simple-deployment-9d88c574d    0         0         0       7m42s
simple-deployment-5f64bf8ddf   1         1         1       4m38s
root@kubernetes-vm:~/workdir# 
~~~~




You can now try changing anything on the cluster and all changes will always be discarded (as they are not part of Git).

Try in the terminal again.

kubectl get deployment simple-deployment
kubectl scale --replicas=3 deployment simple-deployment
kubectl get deployment simple-deployment

Your application will always have 1 pod deployed. This is what the Git state says and therefore Argo CD automatically makes the cluster state the same.
Finish

Once you are ready to proceed, press Check.

~~~~bash
root@kubernetes-vm:~/workdir# kubectl get deployment simple-deployment
NAME                READY   UP-TO-DATE   AVAILABLE   AGE
simple-deployment   1/1     1            1           8m43s
root@kubernetes-vm:~/workdir# kubectl scale --replicas=3 deployment simple-deployment
deployment.apps/simple-deployment scaled
root@kubernetes-vm:~/workdir# kubectl get deployment simple-deployment
NAME                READY   UP-TO-DATE   AVAILABLE   AGE
simple-deployment   1/1     1            1           8m45s
root@kubernetes-vm:~/workdir#
~~~~





- DICA
Delete resources with just a Git commit








# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Enable auto-prune

Even after having auto-sync on and self-heal on, Argo CD will never delete resources from the cluster if you remove them in Git.

To enable this behavior you need to enable the auto-prune option.

First make a commit at your Git repo by deleting the sync-strategies/deployment.yml file.

Then click the "Refresh" button in the ArgoCD dashboard. ArgoCD will detect the change and mark the application as "Out of sync". But the deployment will still be there.

You should see the following. No prune

You can still see the deployment with

kubectl get deployment simple-deployment

~~~~bash
root@kubernetes-vm:~/workdir# kubectl get deployment simple-deployment
NAME                READY   UP-TO-DATE   AVAILABLE   AGE
simple-deployment   1/1     1            1           11m
root@kubernetes-vm:~/workdir# 
~~~~

Go the Argo CD dashboard and click on the application. In the Application screen click the top left "App Details" button.

Scroll down and find the "Sync policy section". Click on the "Prune resources" button to enable it. Answer ok to the confirmation dialog.

Close the settings window and click again the "Refresh" button in the application.

Now your deployment will be removed (since it does not exist in Git).

Check again:

kubectl get deployment

~~~~bash
root@kubernetes-vm:~/workdir# kubectl get deployment
No resources found in default namespace.
root@kubernetes-vm:~/workdir# 
~~~~


Finish

Once you are ready to finish the track, press Check.





# RESUMO
- O recurso de "self-heal" do ArgoCD garante que os comandos ad-hoc não tenham efeito, que seja seguido a risca o que está no Git. Impedindo alterações manuais nos recursos do Kubernetes.



