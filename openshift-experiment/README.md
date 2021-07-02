# Openshift experiment


## Commands

```
# login as admin
oc login -u kubeadmin -p xxxx https://api.crc.testing:6443
# add uid1000 and uid10000 scc resources
kubectl apply -f uid1000.yaml
kubectl apply -f uid10000.yaml

# delete existing pulsar project
oc delete project pulsar
# wait until deleted
while oc get project pulsar; do sleep 2; done
# create new project
oc new-project pulsar   --description="pulsar" --display-name="pulsar"
# add developer as admin
oc adm policy add-role-to-user admin developer

cd ../helm-chart-sources/pulsar
# create RBAC resources
helm template pulsar -f ../../examples/dev-values-simple.yaml --set rbac.create=true . | yq e '. | select(.kind == "Role" or .kind == "RoleBinding" or .kind == "ServiceAccount")' - | kubectl apply -f -

# set scc bindings
oc adm policy add-scc-to-user uid10000 -z default
oc adm policy add-scc-to-user uid10000 -z pulsar-function
oc adm policy add-scc-to-user uid1000 -z pulsar-pulsarheartbeat

# switch to developer for deployment
oc login -u developer -p developer https://api.crc.testing:6443
helm install pulsar -f ../../examples/dev-values-simple.yaml .
```


commands for deleting scc bindings
```
oc adm policy remove-scc-from-user uid10000 -z default
oc adm policy remove-scc-from-user uid10000 -z pulsar-function
oc adm policy remove-scc-from-user uid1000 -z pulsar-pulsarheartbeat
```
