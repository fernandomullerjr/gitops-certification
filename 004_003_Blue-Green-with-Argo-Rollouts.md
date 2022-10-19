




# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Git push

git status
eval $(ssh-agent -s)
ssh-add /home/fernando/.ssh/chave-debian10-github
git add .
git commit -m "Aula 004 - 003 - Blue/Green with Argo Rollouts"
git push
git status


# ################################################################################################################################################################
# ################################################################################################################################################################
# ################################################################################################################################################################
# Blue/Green with Argo Rollouts
Even though Argo Rollouts supports the basic blue/green pattern described in the previous section, it also offers a wealth of customization options. One of the most important additions is the ability to “test” the upcoming color by introducing a “preview” Kubernetes service, in addition to the service used for live traffic. This preview service can be used by the team that performs the deployment to verify the new version before actually switching the traffic.

Here is the initial state of a deployment. The example uses 2 pods (shown as xnsdx and jftql in the diagram).
There are two Kubernetes services. The rollout-blue-green-active is capturing all live traffic from actual users of the application (internet traffic coming from 52.141.221.40). There is also a secondary service called rollout-bluegreen-preview. Under normal circumstances, it also points to the same live version.

Once a deployment starts, a new “color” is created. In the example we have 2 new pods that represent the next version of the application to be deployed (shown as 9t67t and 7vs2m).
The important point here is the fact that the normal “active” service still points to the old version while the “preview” service points to the new pods. This means that all active users are still on the old/stable deployment while internal teams can use the “preview” service to test the new deployment.

If everything goes well, the next version is promoted to be the active version.
Here both services point to the new version. This is also the critical moment for all real users of the application, as they are now switched to use the new version of the application. The old version is still around, but no traffic is sent to it.

Having the old version around is a great failsafe, as one can abort the deployment process and switch back all active users to the old deployment in the fastest way possible.
After some time (the exact amount is configurable in Argo Rollouts), the old version is scaled down completely (to preserve resources). We are now back to the same configuration as the initial state, and the next deployment will follow the same sequence of events.