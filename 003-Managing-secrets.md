Managing Secrets
One of the foundations of GitOps is the usage of Git as the source of truth for the whole system. While most people are familiar with the practice of storing the application source code in version control, GitOps dictates that you should also store all the other parts of your application, such as configuration, Kubernetes manifests, db scripts, cluster definitions, etc.

But what about secrets? How can you use secrets with GitOps? This has been one of the most popular questions from teams that adopt GitOps.

The truth is that there is no single accepted practice for how secrets are managed with GitOps. If you already have a solid solution in place such as HashiCorp vault, then it would make sense to use that even though technically it is against GitOps practices.

If you are starting from scratch on a new project, there are ways to store secrets in Git so that you can manage them using GitOps principles as well. It goes without saying that you should never ever commit raw secrets in Git.

Argo CD can be used with any secret solution that you already have deployed.

All solutions that handle secrets using Git are storing them in an encrypted form. This means that you can get the best of both worlds. Secrets can be managed with GitOps, and they can also be placed in a secure manner in any Git repository (even public repositories).

As an example, we will use the Bitnamic Sealed secrets controller to encrypt secrets before storing them in Git and decrypt them before passing them to the application.

How Kubernetes secrets work
Over the next paragraphs, we will talk about two kinds of secrets, the built-in Kubernetes secrets (present in every Kubernetes cluster) and the sealed secrets as introduced by the Bitnami Sealed secrets controller.

Before we talk about sealed secrets, let’s talk about normal/plain secrets first. Kubernetes includes a native secret resource that you can use in your application. By default, these secrets are not encrypted in any way and the base64 encoding used should be never seen as a security feature. While there are ways to encrypt the Kubernetes secrets within the cluster itself, we are more interested in encrypting them outside the cluster, so that we can store them externally in Git as required by one the founding principles of GitOps (everything stored in Git).

Using Kubernetes application secrets is straightforward. You can use the same mechanisms as configmaps, namely mounting them as files on your application or passing them as environment variables.

Sealed secrets are just an extension built on top of Kubernetes native secrets. This means that after the encryption/decryption takes place, all secrets function as plain Kubernetes secrets, and this is how your application should access them. If you don’t like how plain Kubernetes secrets work, then you need to find an alternative security mechanism.

The Bitnami Sealed secrets controller is a Kubernetes controller you install on the cluster that performs a single task. It converts sealed secrets (that can be committed to Git) to plain secrets (that can be used in your application).

Installing the controller is straightforward:

helm repo add sealed-secrets https://bitnami-labs.github.io/sealed-secrets
helm repo update
helm install sealed-secrets-controller sealed-secrets/sealed-secrets

Once installed, the controller creates two keys on its own:

    The private key is used for secret decryption. This key should stay within the cluster, and you should never give it to anyone.
    The public key is used for secret encryption. This can (and will) be used outside the cluster, so it is ok to give it to somebody else.


Once the controller is installed, you install and use your application in the standard way. You don’t need to change your application code or tamper with your Kubernetes manifests. If your application can use vanilla Kubernetes secrets, then it can work with sealed secrets as well.
It should be clear that the controller does not come in direct contact with your application. It converts sealed secrets to Kubernetes secrets, and afterwards it is up to your application how to use them. The application does not even know that its secrets were originally encrypted in Git.
Encrypting your secrets
We have seen how the controller decrypts secrets. But how do we encrypt secrets in the first place? The controller comes with the associated kubeseal executable that is created for this purpose.

It is a single binary, so you can install it by copying it to your favorite directory (probably in your PATH variable).

Download the latest sealed secrets for your distribution from: https://github.com/bitnami-labs/sealed-secrets/releases 

And install by following these directions:
https://github.com/bitnami-labs/sealed-secrets/blob/main/README.md#installation

Kubeseal does the opposite operation from the controller. It takes an existing Kubernetes secret and encrypts it. Kubeseal requests the public key that was created during the installation process from the cluster and encrypts all secrets with that key.

This means that:

    Kubeseal needs access to the cluster in order to encrypt secrets. (It expects a kubeconfig like kubectl.)
    Encrypted secrets can only be used in the cluster that was used for the encryption process.


The last point is very important as it means that all secrets are cluster specific. 
The namespace of the application is also used by default, so secrets are cluster and namespace specific.

If you want to use the same secret for different clusters, you need to encrypt it for each cluster individually.

To use kubeseal, just take any existing secret in yaml or json format and encrypt it:

kubeseal -n my-namespace <.db-creds.yml> db-creds.json

This creates a SealedSecret which is a custom Kubernetes resource specific to the controller. This file is safe to commit in Git or store in another external system.
You can then apply the secret on the cluster:

kubectl apply -f db-creds.json -n my-namespace

The secret is now part of the cluster and will be decrypted by the controller when an application needs it.

Here is the full diagram of encryption/decryption:
The full process is the following:

    You create a plain Kubernetes secret locally. You should never commit this anywhere.
    You use kubeseal to encrypt the secret in a SealedSecret.
    You delete the original secret from your workstation and apply the sealed secret to the cluster.
    You can optionally commit the Sealed secret to Git.
    You deploy your application that expects normal Kubernetes secrets to function. (The application needs no modifications of any kind.)
    The controller decrypts the Sealed secrets and passes them to your application as plain secrets.
    The application works as usual.

By using the Sealed Secrets controller, we can finally store all our secrets in Git (in an encrypted form) right along the application configuration.

In the example repository, you can look at the folder https://github.com/codefresh-contrib/gitops-secrets-sample-app/tree/main/safe-to-commit and it contains all manifests of the application, including secrets.