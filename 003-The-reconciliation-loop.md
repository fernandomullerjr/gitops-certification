
# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Git push
git status
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git add .
git commit -m "Aula 03 -  The reconciliation loop"
git status


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# The reconciliation loop
You now know how to sync an application on demand using the GUI or the CLI based on the previous sections of this course. But as you saw, Argo CD will also auto-sync applications on its own (if you enable it).

By default, the sync period is 3 minutes. This means that every 3 minutes Argo CD will:

    Gather all applications that are marked to auto-sync.
    Fetch the latest Git state from the respective repositories.
    Compare the Git state with the cluster state.
    Take an action:

        If both states are the same, do nothing and mark the application as synced.
        If states are different mark the application as "out-of-sync".


In the case of out-of-sync applications, you can further decide if you want Argo CD to discard the cluster state or do nothing at all. More details about this will be discussed later on in the section regarding Git strategies.

If you are not happy with the default sync period, you can do the following:

You can change the default period by editing the argocd-cm configmap found on the same namespace as Argo CD.

~~~~yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-cm
  namespace: argocd
  labels:
    app.kubernetes.io/name: argocd-cm
    app.kubernetes.io/part-of: argocd
data:
  timeout.reconciliation: 240s
~~~~

This example changes the sync period to 4 minutes. After you change the manifest, you need to redeploy the argocd-repo-server service.

You can disable the sync functionality completely by setting the timeout.reconciliation period to 0.

And finally you can change the way Argo CD learns about Git changes, and instead of polling your Git provider, you can use Git webhooks or you can use webhooks in tandem with reconciliation. This way should the webhooks fail or you are operating within an environment behind a firewall - the webhook that cannot reach a deployment will continue as expected.

The process is described in the official documentation page at https://argo-cd.readthedocs.io/en/stable/operator-manual/webhook/ and it is different according to your Git provider.

Using Webhooks is very efficient, as now your Argo CD installation will never delay when you commit something to Git. If you only use the default way of polling, then you might have to wait up to 3 minutes (or whatever time you have set as sync period) for Argo CD to detect the changes. With Webhooks, as soon as there is any change in Git, Argo CD will run the sync process.
