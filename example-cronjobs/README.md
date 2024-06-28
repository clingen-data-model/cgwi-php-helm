# Example CronJobs

Right now, this directory just includes some CronJob resources that can stage relevant state
from producion instances to demo/dev instances for testing purposes.

`kubectl apply` with these does not do anything directly, since these jobs have `suspend` set
to true. Ideally, you would just be able to run these with `kubectl create job JOBNAME --from cj/CRONJOBNAME`
but some versions of openshift/kubernetes have an error related to configuraiton of
`ownerReferences` when you do this, so instead you have to do something like:

```
CJ=refresh-demo-data-ccdb
kubectl create job $CJ-$(date '+%Y%m%d-%H%M') --from=cj/$CJ --dry-run=client -o yaml \
  | yq '.metadata |= (del(.ownerReferences))' | kubectl -f -
```


