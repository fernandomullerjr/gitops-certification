# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Git push
git status
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git add .
git commit -m "Aula 03 - LIVE EXERCISE - 05 Secrets with GitOps"
git push
git status


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# 05 Secrets with GitOps

Handling secrets with Argo CD

Welcome

Install the Sealed Secrets controller

Use Kubeseal to encrypt secrets

Deploy secrets







# Managing secrets with Argo CD

You need to have a GitHub account for this exercise.

The example application is at https://github.com/codefresh-contrib/gitops-certification-examples/tree/main/secret-app. Fork this repository in your own account.
Objectives

In this track, this is what you'll learn:

    Installation of the sealed secrets controller
    How to encrypt secrets and commit them to Git
    How the decryption controller works

Please wait while we boot the VM for you and start Kubernetes.









# Welcome

Our example application can be found at https://github.com/codefresh-contrib/gitops-certification-examples/tree/main/secret-app

Make sure to fork it to your own account and note down the URL. It should be something like:

https://github.com/<your user>/gitops-certification-examples/

Take a look at the Kubernetes manifests in secret-app/manifests/ to understand what we will deploy.

It is a very simple application with one deployment and one service that also needs two secrets files (for db connection and paypal certificate) that are passed to the application as files under /secrets/.

The two secrets are stored in the files db-creds-encrypted.yaml and paypal-cert-encrypted.yaml These files are empty in the Git repository because we want to encrypt them first.

You can also see the full source code at source-code-secrets if you want to understand how the application is loading secrets from files.

Once you are ready to proceed, press Next.


- Repo do Fernando:
<https://github.com/fernandomullerjr/gitops-certification-examples/tree/main/secret-app>







# Install the Sealed Secrets controller

We have placed the raw/unencrypted secrets in your work directory that are needed by the application. You can look at the files in the Editor tab.

These secrets are plain Kubernetes secrets and are not encrypted in any way. Let's install the Bitnami Sealed secrets controller to encrypt our secrets.

We installed Argo CD for you and you can login in the UI tab.

The UI starts empty because nothing is deployed on our cluster. Click the "New app" button on the top left and fill the following details:

    application name : controller
    project: default
    SYNC POLICY: automatic
    repository URL: https://github.com/codefresh-contrib/gitops-certification-examples
    path: ./bitnami-sealed-controller
    Cluster: https://kubernetes.default.svc (this is the same cluster where ArgoCD is installed)
    Namespace: kube-system

Leave all the other values empty or with default selections. Finally click the Create button. The controller will be installed on the cluster.

Notice that are using a specific namespace for the controller and not the default one. It is imperative to enter kube-system otherwise the secrets controller will not work.

You can see that GitOps is not only useful for your own applications but also for other supporting applications as well.

Once you are ready to proceed, press Check.





Installing Kubeseal executable...




# Use Kubeseal to encrypt secrets

The Sealed Secrets controller is running now on the cluster and it is ready to decrypt secrets.

We now need to encrypt our secrets and commit them to git. Encryption happens with the kubeseal executable. It needs to be installed in the same way as kubectl. It re-uses the cluster authentication already used by kubectl.

We have already installed kubeseal for you in this exercise. You can use it right away to encrypt your plain Kubernetes secrets and convert them to encrypted secrets

Run the following

kubeseal < unsealed_secrets/db-creds.yml > sealed_secrets/db-creds-encrypted.yaml -o yaml
kubeseal < unsealed_secrets/paypal-cert.yml > sealed_secrets/paypal-cert-encrypted.yaml -o yaml

Now you have encrypted secrets. Open the files in the "Editor" tab and copy the contents in your clipboard.

Then go the Github UI in another browser tab and commit/push their contents in your own fork of the application, filling the empty files at gitops-certification-examples/secret-app/manifests/db-creds-encrypted.yaml and gitops-certification-examples/secret-app/manifests/paypal-cert-encrypted.yaml

Once you are ready to proceed, press Check.

~~~~bash

root@kubernetes-vm:~/workdir# ls
admin-pass.txt  sealed_secrets  unsealed_secrets
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# kubeseal < unsealed_secrets/db-creds.yml > sealed_secrets/db-creds-encrypted.yaml -o yamlroot@kubernetes-vm:~/workdir# kubeseal < unsealed_secrets/paypal-cert.yml > sealed_secrets/paypal-cert-encrypted.yaml -o yaml
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# cat sealed_secrets/
db-creds-encrypted.yaml     paypal-cert-encrypted.yaml  
root@kubernetes-vm:~/workdir# cat sealed_secrets/db-creds-encrypted.yaml 
apiVersion: bitnami.com/v1alpha1
kind: SealedSecret
metadata:
  creationTimestamp: null
  name: mysql-credentials
  namespace: default
spec:
  encryptedData:
    connection: AgCpO6GYVv7ClUupgjhACmWjybSJgSPgAMa1Lx7xHE/AzHk/SB9KcJK7h12gyKsymTozXiEZ0+uzkV006juY1sDfp/MyEiNvNFgwe6/BNq9rtnuTouKFC7mCYnu2sjKu/a6JCuq3COh1beykePfQ0u6JYimnGwehqBtXrYACaPzDLHHG58eV0UlpU2cWEaBzpTgrhQ0mv1e6RX7P/VbR/IpvjqfXyTH0Ea13TGTI2aAJDOgWyyyCwFRrONr257RqGJ/6+0PthBtu0VKMFlDgy/zlz3ZjDqlNIeMiD5s/m6LZuQs6JAgRXJDYHrZFk12RtZhDwwXjiUukCySMj+ZSgPSi2KA7t9Fkbu8Qhs/luqKaYZj4ME5u4/2k5MDNqcU0UQidtYK5Y31tcMboTfnXtbNFvf9f6eNJuZn2RGNCydIdGTenkGhnJzUjPpcPCngUMbQa51UFHl/TIVWtVlE2JCrl9grdsumibyJL7yNU1Wn2EBmILTbsjqnVZxEbI3PbyXuWnTqRscaJRTHOQjQYDo/5ZHNnxN+K8Z6s3/x7rDV3OHcWsRmGHCVT4rjyH0vbONvKtm/wzL86qhYD8Lf0Z3Mti9eeoEZFEMCogWF82O8O4TtbASslJlwP/oxqAf/Arcp12EalNWJ4tspn3+PezgTcTM8x5WP+D1tARRoKg6Ovb0LgrilmU+p0zYxzxyKD98FfMWmWwFiOxfTDIdvs+LVK+2TRZO50c30Dv+ZfSZB3ddE=
    password: AgDib27Iuk7kjL5xzndanrCB4BkKCvDftoHC7ecKJLdI6SXiVvuKmntQccfBFr7WXFbsb5+et7FraSPYptXs+fb8VTcX5dcEtldy3m8NvHda0/BhByMBS89GiS9J7VWX4v6durm19sGRRctnYs6OuQ1vSl43pI60iGohaqmpp1wb46Thpi96MiWW+JpE9zwZXsqV/d0anQYJ8tXNvuVxbbKZZ4IRTzsFHy+1UW6fichR4pgeARoB1atrMR6LSW7hEDoQmDE+v+boJAd8brVg6IejrUGwW+J0VjajuYYLqFWbFu8qlJrk4G6iOufSf6uau5vNofn5tndqknuWVp2EGQ6Fyd3rcrcmZlTax4SpbN7O+fUY6CgEXYiDQT5NYydgzkv11IZp4d5yNy0TmR2Ie9KF78FBMDF2hCyaQ4112IoBqCMzIZE71Trc/sS0W6f5DBxIvL+F/aIWoeRgr9YB1TFFqbltcbQ++QXZf2JfDnTLbipQ/kHbBr7Tvqrogwn+V2SUaAyJ525BgNdcJrZFx5BU1q4yBLdRT6iZxoaXlDRFb+2nkNrPzclbhj0EReXMiRlHw19hB5YoQook0kEICxZcFjm1PiXpWiKnh6edUwoaBkOAKqwebxglbbxbmYzFToaJ2TMDJxUYQDuoQCPGqfrb5S3WWghxqvFj7wzBKPlVvnSxfWrALzWQuD5HPKSl5gJm1wCcE2Q6Z1g0WbK1Ous=
    username: AgDMsuUPv8ML8XUYP8rNWeD/D+fKs0Bas4CcriPZGHVLXD2Qm1r/ym7o7qv5mgXLtspNhQnhcIz8hER8kdK3XsumJBsTIjkJStvFOB44Ud5BBYprONFR9g3w7XXhBmFZRMmZGkBNs8H38Ybtq27Q4AdhaWJLHTCQfk+21xbwtDsb0tTb0T3nsbBICVp95oPY4NNlTyzw3VwoxyF6FQchPwkI/fqArIfqdQeVCYszolk2xPtXmsfGGGXFX6nKqnmexPMEzO8tarOvdQ40Nl6WBhVt0Wm1GQW1ApRO7A0mQcJOyzY2RIYTj5UD95WEr0Uk8iIZgGMjj/omHljdtsHTnCGY3itK5CovCjl7GhZGsmVDmEqALIze5STzTzdrhyrffkd9L6437CXeTF7TlQT4ptfYqtEKZhAuRYtwlFvMYajJlIpEW79aJigNNoDs5DsjyxqMSKbmn0As8Pwzmq31/BF6JNHiELCiFsxSHE9C+1QZ/UkNdj5bBTRX0C3Jygsz3HZjvH92g0FkvyuSH2dBugdGXGR1LOHO+mBAzktP1KiRC6bcJklc0NOZscQB246Che8zT1qgYgTcw3cLSHAMH00T/KCdz80asAJf7bhCJCzEHPcZiiJt8SvIS/VOjUVivaBKzn1zYcC/NiPouzVuXTkoKPdhdqEx4C6Ym9MRoChMEKlhSyela3PyRRc4+C+A00tEz2AmxCmdIcLeRg==
  template:
    data: null
    metadata:
      creationTimestamp: null
      name: mysql-credentials
      namespace: default
    type: Opaque

root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# 
root@kubernetes-vm:~/workdir# cat sealed_secrets/paypal-cert-encrypted.yaml 
apiVersion: bitnami.com/v1alpha1
kind: SealedSecret
metadata:
  creationTimestamp: null
  name: paypal-cert
  namespace: default
spec:
  encryptedData:
    paypal.crt: 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
  template:
    data: null
    metadata:
      creationTimestamp: null
      name: paypal-cert
      namespace: default
    type: Opaque

root@kubernetes-vm:~/workdir# 


~~~~









The Sealed secrets controller will automatically decrypt secrets and pass them to your application






# Deploy secrets
Step 1

Now that all our secrets are in Git in an encrypted form we can deploy our application as normal.

Login in the Argo CD UI.

Click the "New app" button on the top left and fill the following details:

    application name : demo
    project: default
    SYNC POLICY: automatic
    repository URL: https://github.com/your_github_account/gitops-certification-examples
    path: ./secret-app/manifests
    Cluster: https://kubernetes.default.svc (this is the same cluster where ArgoCD is installed)
    Namespace: default

Leave all the other values empty or with default selections. Finally click the Create button. The application entry will appear in the main dashboard. Click on it.

The application should now be deployed. Once it is healthy you can see it running in the "Deployed app" tab. Just for illustration purposes, the application is showing the secrets it can access. This way you can verify that the sealed secrets controller is working as expected, decrypting secrets on the fly.
Finish

Once you are ready to finish the track, press Check.



https://github.com/fernandomullerjr/gitops-certification-examples/tree/main/secret-app
https://github.com/fernandomullerjr/gitops-certification-examples