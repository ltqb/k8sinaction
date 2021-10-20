```shell
[jialesun:...tion/examples/rs_deployment]$ minikube kubectl -- get deploy -o wide 
NAME                                        READY   UP-TO-DATE   AVAILABLE   AGE     CONTAINERS   IMAGES   SELECTOR
deployments-busybox-deployment-deployment   2/2     2            2           6m55s   busybox      nginx    app=deployments-simple-deployment-app
```

```
[jialesun:...tion/examples/rs_deployment]$ minikube kubectl -- rollout history deploy  deployments-busybox-deployment-deployment 
deployment.apps/deployments-busybox-deployment-deployment 
REVISION  CHANGE-CAUSE
1         <none>
```

```
[jialesun:...tion/examples/rs_deployment]$ minikube kubectl -- apply -f simple_deploy.yaml --record 
deployment.apps/deployments-busybox-deployment-deployment configured
[jialesun:...tion/examples/rs_deployment]$ minikube kubectl -- get deploy -o wide  
NAME                                        READY   UP-TO-DATE   AVAILABLE   AGE     CONTAINERS   IMAGES    SELECTOR
deployments-busybox-deployment-deployment   2/2     1            2           8m35s   busybox      busybox   app=deployments-simple-deployment-app
```

```
[jialesun:...tion/examples/rs_deployment]$ minikube kubectl -- rollout history deploy  deployments-busybox-deployment-deployment
deployment.apps/deployments-busybox-deployment-deployment 
REVISION  CHANGE-CAUSE
2         <none>
3         kubectl apply --cluster=minikube --filename=simple_deploy.yaml --record=true
```

```
[jialesun:...tion/examples/rs_deployment]$ minikube kubectl -- rollout undo deploy/deployments-busybox-deployment-deployment --to-revision=2 
deployment.apps/deployments-busybox-deployment-deployment rolled back
[jialesun:...tion/examples/rs_deployment]$ minikube kubectl -- get deploy -o wide 
NAME                                        READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES   SELECTOR
deployments-busybox-deployment-deployment   2/2     2            2           11m   busybox      nginx    app=deployments-simple-deployment-app

[jialesun:...tion/examples/rs_deployment]$ minikube kubectl -- get rs -A
NAMESPACE       NAME                                                   DESIRED   CURRENT   READY   AGE
default         deployments-busybox-deployment-deployment-6d6f55b4b    0         0         0       29m
default         deployments-busybox-deployment-deployment-869df7f76d   2         2         2       25m
```