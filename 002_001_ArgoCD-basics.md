
# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Git push
git status
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git add .
git commit -m "Aula 2 - ArgoCD Basics"
git status
 





[ Questões importantes, antes de instalar o Argo ]
There are several questions that you need to answer before making a decision about installation:
1.	Will your Argo CD installation manage only the cluster on which it is installed? Will it manage other clusters? Both?
2.	Do you want a High Availability (HA) installation or not?
3.	Do you want to use plain manifests or are you looking for a Helm chart?
4.	How will you manage Argo CD upgrades? With an external system? Or do you wish for Argo CD to manage itself?





[RECOMENDADO – PRODUÇÃO]
For a production setup we suggest you use Autopilot, a companion project that not only installs Argo CD, but commits all configuration to git so Argo CD can manage itself using GitOps.



[EXPONDO O ARGO]
Regarding configuration options, Argo CD can be customized a lot after the initial installation, but the absolute essentials are:

1.	Decide how to expose Argo CD API/UI to your users.
2.	Decide how users are going to authenticate to Argo CD.

Exposing your Argo CD to users will be a different process depending on your organization, but in most cases, you should make the Argo CD UI available to external traffic via a Kubernetes ingress.

The setup process is not specific to Argo CD, and you should already be familiar with ingresses for other Kubernetes applications. You can follow the official documentation for setting up Argo CD with popular Ingress options.

Once the external URL is ready, you need to decide how users will access the Argo CD UI. There are mainly two approaches:

1.	Use a small number of local users. Authentication is handled by Argo CD itself. Best for very small companies (e.g. 2-5 people).
2.	Use an SSO provider. Authentication is handled by the provider. Ideal for companies and large organizations

You can read all the details at https://argo-cd.readthedocs.io/en/stable/operator-manual/user-management/





01 Setup Argo CD
Learn how to install Argo CD on a cluster
Deploy Argo CD to Kubernetes
Expose the Argo CD UI
Log in the Argo CD UI
Argo CD CLI
Login with the CLI







# Deploy Argo CD to Kubernetes

Use the terminal to deploy Argo CD:

kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/codefresh-contrib/gitops-certification-examples/main/argocd-noauth/install.yaml

This will create a new namespace, argocd, where Argo CD services and application resources will live.

To view the deployment enter

kubectl get pods -n argocd

Note that this is simple installation method without any authentication. In a real setting you would probably setup SSO with your ArgoCD instance.
Finish

Click Check to finish this challenge when ready.



root@kubernetes-vm:~/workdir# kubectl create namespace argocd
namespace/argocd created
root@kubernetes-vm:~/workdir# kubectl apply -n argocd -f https://raw.githubusercontent.com/codefresh-contrib/gitops-certification-examples/main/argocd-noauth/install.yaml
customresourcedefinition.apiextensions.k8s.io/applications.argoproj.io created
customresourcedefinition.apiextensions.k8s.io/appprojects.argoproj.io created
serviceaccount/argocd-application-controller created
serviceaccount/argocd-dex-server created
serviceaccount/argocd-redis created
serviceaccount/argocd-server created
role.rbac.authorization.k8s.io/argocd-application-controller created
role.rbac.authorization.k8s.io/argocd-dex-server created
role.rbac.authorization.k8s.io/argocd-server created
clusterrole.rbac.authorization.k8s.io/argocd-application-controller created
clusterrole.rbac.authorization.k8s.io/argocd-server created
rolebinding.rbac.authorization.k8s.io/argocd-application-controller created
rolebinding.rbac.authorization.k8s.io/argocd-dex-server created
rolebinding.rbac.authorization.k8s.io/argocd-redis created
rolebinding.rbac.authorization.k8s.io/argocd-server created
clusterrolebinding.rbac.authorization.k8s.io/argocd-application-controller created
clusterrolebinding.rbac.authorization.k8s.io/argocd-server created
configmap/argocd-cm created
configmap/argocd-cmd-params-cm created
configmap/argocd-gpg-keys-cm created
configmap/argocd-rbac-cm created
configmap/argocd-ssh-known-hosts-cm created
configmap/argocd-tls-certs-cm created
secret/argocd-secret created
service/argocd-dex-server created
service/argocd-metrics created
service/argocd-redis created
service/argocd-repo-server created
service/argocd-server created
service/argocd-server-metrics created
deployment.apps/argocd-dex-server created
deployment.apps/argocd-redis created
deployment.apps/argocd-repo-server created
deployment.apps/argocd-server created
statefulset.apps/argocd-application-controller created
networkpolicy.networking.k8s.io/argocd-application-controller-network-policy created
networkpolicy.networking.k8s.io/argocd-dex-server-network-policy created
networkpolicy.networking.k8s.io/argocd-redis-network-policy created
networkpolicy.networking.k8s.io/argocd-repo-server-network-policy created
networkpolicy.networking.k8s.io/argocd-server-network-policy created
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# kubectl get pods -n argocd
NAME                                 READY   STATUS    RESTARTS   AGE
argocd-redis-5b6967fdfc-wv6rf        1/1     Running   0          83s
argocd-dex-server-5fc596bcdd-jsfbz   1/1     Running   0          83s
argocd-repo-server-98598b6c7-4sncp   1/1     Running   0          83s
argocd-application-controller-0      1/1     Running   0          81s
argocd-server-5b4b7b868b-6vnb6       1/1     Running   0          82s
root@kubernetes-vm:~/workdir# 






By default, Argo CD is only accessible from within the cluster. To expose the UI to users you can utilize any of the standard Kubernetes networking mechanisms such as

    Ingress (recommended for production)
    Load balancer (affects cloud cost)
    NodePort (simple but not very flexible)

For this exercise we will do the simplest way and use a Nodeport service. In a production environment you should use an Ingress.









# Expose the Argo CD UI

Feel free to look at the file service.yml in the Editor tab. It is a standard NodePort service resource.

Use the terminal to create it in the cluster:

kubectl apply -f service.yml

After the service is created, check the "Argo CD UI" tab to access the Argo CD Web interface.
Finish

Click Check to finish this challenge when ready.



root@kubernetes-vm:~/workdir# LS
LS: command not found
root@kubernetes-vm:~/workdir# ls
service.yml
root@kubernetes-vm:~/workdir# cat service.yml 
apiVersion: v1
kind: Service
metadata:
  labels:
    app: argocd-server
  managedFields:
  name: argocd-server-nodeport
  namespace: argocd
spec:
  ports:
  - nodePort: 30443
    port: 8080
    protocol: TCP
  selector:
    app.kubernetes.io/name: argocd-server
  type: NodePort
root@kubernetes-vm:~/workdir# 





root@kubernetes-vm:~/workdir# kubectl apply -f service.yml
service/argocd-server-nodeport created
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# kubectl get svc
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.43.0.1    <none>        443/TCP   5m10s
root@kubernetes-vm:~/workdir# kubectl get svc -A
NAMESPACE              NAME                        TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
default                kubernetes                  ClusterIP      10.43.0.1       <none>        443/TCP                      5m12s
kubernetes-dashboard   kubernetes-dashboard        ClusterIP      10.43.253.139   <none>        443/TCP                      5m9s
kube-system            kube-dns                    ClusterIP      10.43.0.10      <none>        53/UDP,53/TCP,9153/TCP       5m7s
kubernetes-dashboard   dashboard-metrics-scraper   ClusterIP      10.43.128.72    <none>        8000/TCP                     5m7s
kube-system            metrics-server              ClusterIP      10.43.157.236   <none>        443/TCP                      5m5s
kube-system            traefik                     LoadBalancer   10.43.0.21      10.5.0.123    80:30493/TCP,443:31267/TCP   4m12s
argocd                 argocd-dex-server           ClusterIP      10.43.243.171   <none>        5556/TCP,5557/TCP,5558/TCP   3m55s
argocd                 argocd-metrics              ClusterIP      10.43.212.1     <none>        8082/TCP                     3m55s
argocd                 argocd-redis                ClusterIP      10.43.239.33    <none>        6379/TCP                     3m55s
argocd                 argocd-repo-server          ClusterIP      10.43.187.8     <none>        8081/TCP,8084/TCP            3m55s
argocd                 argocd-server               ClusterIP      10.43.81.29     <none>        80/TCP,443/TCP               3m55s
argocd                 argocd-server-metrics       ClusterIP      10.43.31.142    <none>        8083/TCP                     3m55s
argocd                 argocd-server-nodeport      NodePort       10.43.37.2      <none>        8080:30443/TCP               14s
root@kubernetes-vm:~/workdir# 







Argo CD has a flexible user model that also supports SSO with popular identify providers. For this simple example we will use the default admin account.









# Log in the Argo CD UI

Argo CD initially has an admin user with an autogenerated password. You can get the admin password from the default Configmap

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d > admin-pass.txt

Just to make things simple we have disabled authentication in the ArgoCD UI.

Simply use the "ArgoCD UI" tab to look at the interface Everything is empty right now.
Finish

Click Check to finish this challenge when ready.


root@kubernetes-vm:~/workdir# kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d > admin-pass.txt
root@kubernetes-vm:~/workdir# cat admin-pass.txt 
6buHIU3JtSKsGelE
root@kubernetes-vm:~/workdir# 







Apart from the Graphical User interface, Argo CD also comes with a Command Line Interface (CLI) that can be used for common operations.

The CLI is great for administration work, while the GUI is best for inspecting applications and settings.











# Argo CD CLI
Step 1

To install the CLI

curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/download/v2.1.5/argocd-linux-amd64
chmod +x /usr/local/bin/argocd

Step 2

Test that the CLI works by typing

argocd help

Finish

Click Check to finish this challenge when ready.


root@kubernetes-vm:~/workdir# curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/download/v2.1.5/argocd-linux-amd64
root@kubernetes-vm:~/workdir# chmod +x /usr/local/bin/argocd
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# argocd version
argocd: v2.1.5+a8a6fc8
  BuildDate: 2021-10-20T15:16:40Z
  GitCommit: a8a6fc8dda0e26bb1e0b893e270c1128038f5b0f
  GitTreeState: clean
  GoVersion: go1.16.5
  Compiler: gc
  Platform: linux/amd64
FATA[0000] Argo CD server address unspecified           
root@kubernetes-vm:~/workdir# 





root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# argocd help
argocd controls a Argo CD server

Usage:
  argocd [flags]
  argocd [command]

Available Commands:
  account     Manage account settings
  admin       Contains a set of commands useful for Argo CD administrators and requires direct Kubernetes access
  app         Manage applications
  cert        Manage repository certificates and SSH known hosts entries
  cluster     Manage cluster credentials
  completion  output shell completion code for the specified shell (bash or zsh)
  context     Switch between contexts
  gpg         Manage GPG keys used for signature verification
  help        Help about any command
  login       Log in to Argo CD
  logout      Log out from Argo CD
  proj        Manage projects
  relogin     Refresh an expired authenticate token
  repo        Manage repository connection parameters
  repocreds   Manage repository connection parameters
  version     Print version information

Flags:
      --auth-token string               Authentication token
      --client-crt string               Client certificate file
      --client-crt-key string           Client certificate key file
      --config string                   Path to Argo CD config (default "/root/.argocd/config")
      --core                            If set to true then CLI talks directly to Kubernetes instead of talking to Argo CD API server
      --grpc-web                        Enables gRPC-web protocol. Useful if Argo CD server is behind proxy which does not support HTTP2.
      --grpc-web-root-path string       Enables gRPC-web protocol. Useful if Argo CD server is behind proxy which does not support HTTP2. Set web root.
  -H, --header strings                  Sets additional header to all requests made by Argo CD CLI. (Can be repeated multiple times to add multiple headers, also supports comma separated headers)
  -h, --help                            help for argocd
      --http-retry-max int              Maximum number of retries to establish http connection to Argo CD server
      --insecure                        Skip server certificate and domain verification
      --logformat string                Set the logging format. One of: text|json (default "text")
      --loglevel string                 Set the logging level. One of: debug|info|warn|error (default "info")
      --plaintext                       Disable TLS
      --port-forward                    Connect to a random argocd-server port using port forwarding
      --port-forward-namespace string   Namespace name which should be used for port forwarding
      --server string                   Argo CD server address
      --server-crt string               Server certificate file

Use "argocd [command] --help" for more information about a command.
root@kubernetes-vm:~/workdir# 












Before we can use your CLI we need to authentication against our own Argo CD instance.

In a production environment you might have different authentication options and also change the default admin password (or remove the admin account completely).









# Login with the CLI
Step 1

To authenticate

argocd login localhost:30443 --insecure

We use the insecure option because Argo CD has a self-signed certificate. In a production installation you would have a real certificate and this option should not be used.

Log in as admin and use the password stored in admin-pass.txt. You can see the value from the editor tab.

Notice that for security reasons the password you paste is not shown in the terminal (not even with asterisks). Just paste the password value and press Enter.
Step 2

Run some example commands

argocd version
argocd app list

Finish

Click Check to finish this challenge when ready.

root@kubernetes-vm:~/workdir# argocd login localhost:30443 --insecure
Username: admin
Password: 
'admin:login' logged in successfully
Context 'localhost:30443' updated
root@kubernetes-vm:~/workdir# argocd version
argocd: v2.1.5+a8a6fc8
  BuildDate: 2021-10-20T15:16:40Z
  GitCommit: a8a6fc8dda0e26bb1e0b893e270c1128038f5b0f
  GitTreeState: clean
  GoVersion: go1.16.5
  Compiler: gc
  Platform: linux/amd64
argocd-server: v2.1.5+a8a6fc8
  BuildDate: 2021-10-20T15:16:40Z
  GitCommit: a8a6fc8dda0e26bb1e0b893e270c1128038f5b0f
  GitTreeState: clean
  GoVersion: go1.16.5
  Compiler: gc
  Platform: linux/amd64
  Ksonnet Version: v0.13.1
  Kustomize Version: v4.2.0 2021-06-30T22:49:26Z
  Helm Version: v3.6.0+g7f2df64
  Kubectl Version: v0.21.0
  Jsonnet Version: v0.17.0
root@kubernetes-vm:~/workdir# argocd app list
NAME  CLUSTER  NAMESPACE  PROJECT  STATUS  HEALTH  SYNCPOLICY  CONDITIONS  REPO  PATH  TARGET
root@kubernetes-vm:~/workdir# 







# Creating an application

An application can be created in Argo CD from the UI, CLI, or by writing a Kubernetes manifest that can then be passed to kubectl to create resources.
Creating an ArgoCD application in the UI

First, navigate to the +NEW APP on the left-hand side of the UI. 

Next, add the following to create the application:

General Section:

    Application Name: TBD
    Project: default
    Sync Policy: Automatic


Source Section:

    Repository URL/Git: this is the GitHub repository URL
    Branches: main
    Path: TBD


Destination Section:

    Cluster URL: select the cluster URL you are using
    Namespace: default


Then, click CREATE and you have now created your Argo CD application.

Creating an Argo CD application with the argocd CLI

argocd app create {APP NAME} \
--project {PROJECT} \
--repo {GIT REPO} \--path {APP FOLDER} \
--dest-namespace {NAMESPACE} \
--dest-server {SERVER URL}

    {APP NAME} is the name you want to give the application
    {PROJECT} is the name of the project created or "default"
    {GIT REPO} is the url of the git repository where the gitops config is located
    {APP FOLDER} is the path to the configuration for the application in the gitops repo
    {DEST NAMESPACE} is the target namespace in the cluster where the application will be deployed
    {SERVER URL} is the url of the cluster where the application will be deployed. Use https://kubernetes.default.svc to reference the same cluster where Argo CD has been deployed


Once this completes, you can see the status and configuration of the application.

argocd app list

For a more detailed view of the application configuration, run:

argocd app get {APP NAME}
