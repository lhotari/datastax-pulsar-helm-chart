# Openshift experiment


## Commands

```
# login as admin
oc login -u kubeadmin -p xxxx https://api.crc.testing:6443

# delete existing pulsar project
oc delete project pulsar
# wait until deleted
while oc get project pulsar; do sleep 2; done

# create new project
oc new-project pulsar   --description="pulsar" --display-name="pulsar"
# add developer as admin
oc adm policy add-role-to-user admin developer

# switch to developer for deployment
oc login -u developer -p developer https://api.crc.testing:6443
# install local helm chart
helm install pulsar -f ./examples/dev-values-simple.yaml ./helm-chart-sources/pulsar
```
