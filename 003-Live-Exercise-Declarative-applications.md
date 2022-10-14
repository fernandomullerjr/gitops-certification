
# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Git push

git status
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git add .
git commit -m "Aula 03 - 06 Declarative deployments"
git push
git status


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# 06 Declarative deployments

Describe applications as Kubernetes resources

Welcome

ArgoCD declarative format

Use App of Apps










# Declarative management of Argo CD applications

The example application is at https://github.com/codefresh-contrib/gitops-certification-examples/tree/main/declarative.
Objectives

In this track, this is what you'll learn:

    How an Argo CD application can be modeled as a Kubernetes resource
    How to manage multiple ArgoCD applications together
    How to use GitOps for the application definitions

Please wait while we boot the VM for you and start Kubernetes.












# Welcome

So far in all the previous challenges, you have been creating applications via the ArgoCD UI or CLI. An ArgoCD application is essentially a combination of a git repository, an Argo project, several sync options and other values.

This information doesn't need to be confined in Argo CD itself. It can be modeled in a Kubernetes resource so that it can be stored in Git and managed with GitOps as well.

Take a look at https://github.com/codefresh-contrib/gitops-certification-examples/blob/main/declarative/single-app/my-application.yml for an example application

Once you are ready to proceed, press Next.


<https://github.com/fernandomullerjr/gitops-certification-examples/blob/main/declarative/single-app/my-application.yml>







Please wait while we setup Argo CD for you...









# ArgoCD declarative format

Since our ArgoCD application is now a Kubernetes resource we can handle it like any other kubernetes resource.

We have checked out for you locally the file my-application.yml Apply it with

kubectl apply -f my-application.yml

We have also installed Argo CD for you and you see it in the UI tab.

You should now see the application already in the ArgoCD UI.

Once you are ready to proceed, press Check.


root@kubernetes-vm:~/workdir# ls
admin-pass.txt  my-application.yml
root@kubernetes-vm:~/workdir# kubectl apply -f my-application.yml
application.argoproj.io/demo created
root@kubernetes-vm:~/workdir# kubectl get all
NAME                                    READY   STATUS              RESTARTS   AGE
pod/simple-deployment-9d88c574d-mbmd4   0/1     ContainerCreating   0          2s

NAME                     TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
service/kubernetes       ClusterIP   10.43.0.1      <none>        443/TCP   7m12s
service/simple-service   ClusterIP   10.43.54.170   <none>        80/TCP    2s

NAME                                READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/simple-deployment   0/1     1            0           2s

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/simple-deployment-9d88c574d   1         1         0       2s
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# kubectl get application -A
NAMESPACE   NAME   SYNC STATUS   HEALTH STATUS
argocd      demo   Synced        Healthy
root@kubernetes-vm:~/workdir# 

root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# cat 
admin-pass.txt      my-application.yml  
root@kubernetes-vm:~/workdir# cat my-application.yml 

~~~~yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: demo
  # You'll usually want to add your resources to the argocd namespace.
  namespace: argocd
  # Add a this finalizer ONLY if you want these to cascade delete.
  finalizers:
    - resources-finalizer.argocd.argoproj.io
spec:
  # The project the application belongs to.
  project: default

  # Source of the application manifests
  source:
    repoURL: https://github.com/codefresh-contrib/gitops-certification-examples.git
    targetRevision: HEAD
    path: ./declarative/manifests
   
    # directory
    directory:
      recurse: false
  # Destination cluster and namespace to deploy the application
  destination:
    server: https://kubernetes.default.svc
    namespace: default

  # Sync policy
  syncPolicy:
    automated: # automated sync by default retries failed attempts 5 times with following delays between attempts ( 5s, 10s, 20s, 40s, 80s ); retry controlled using `retry` field.
      prune: true # Specifies if resources should be pruned during auto-syncing ( false by default ).
      selfHeal: true # Specifies if partial app sync should be executed when resources are changed only in target Kubernetes cluster and no git change detected ( false by default ).
      allowEmpty: false # Allows deleting all application resources during automatic syncing ( false by default ).
~~~~










- DICA
The App-of-Apps pattern allows you to group/deploy multiple applications together















# Use App of Apps

We can delete the application like any other Kubernetes resource

kubectl delete -f my-application.yml

After a while the application should disappear from the ArgoCD UI.

Remember however that the whole point of GitOps is to avoid manual kubectl commands. We want to store this application manifest in Git. And if we store it in Git, we can handle it like another GitOps application!

ArgoCD can manage any kind of Kubernetes resources and this includes its own applications (inception).

So we can commit multiple applications in Git and pass them to ArgoCD like any other kind of manifest. This means that we can handle multiple applications as a single one.

We already have such example at https://github.com/codefresh-contrib/gitops-certification-examples/tree/main/declarative/multiple-apps.

In the ArgoCD UI, click the "New app" button on the top left and fill the following details:

    application name : 3-apps
    project: default
    SYNC POLICY: automatic
    repository URL: https://github.com/codefresh-contrib/gitops-certification-examples
    path: ./declarative/multiple-apps
    Cluster: https://kubernetes.default.svc (this is the same cluster where ArgoCD is installed)
    Namespace: argocd

Notice that the namespace value is the namespace where the parent Application is deployed and not the namespace where the individual applications are deployed. ArgoCD applications must be deployed in the same namespace as ArgoCD itself.

Leave all the other values empty or with default selections. Finally click the Create button.

ArgoCD will deploy the parent application and its 3 children. Click on the parent application. You should see the following. three-apps

Spend some time on the UI to understand where each application is deployed. You can also use the command line

kubectl get all -n demo1
kubectl get all -n demo2
kubectl get all -n demo3

Once you are ready to finish the track, press Check.