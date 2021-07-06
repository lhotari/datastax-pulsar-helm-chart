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

# remove helm-charts-sources/pulsar/charts directory to prevent unwanted dependency to kube-prometheus-stack
rm -rf helm-chart-sources/pulsar/charts

# install local helm chart
helm install pulsar -f ./examples/dev-values-simple.yaml ./helm-chart-sources/pulsar

# wait for deployment, then expose route for admin console
oc expose svc/pulsar-adminconsole
# show routes
oc get route
# go to http://pulsar-adminconsole-pulsar.apps-crc.testing/ for admin console
```


## Commands for testing Pulsar Functions

Start port forwarding for Pulsar to pulsar-proxy service ports 8080 and 6650
```
kubectl port-forward service/pulsar-proxy 8080 6650
```

Deploy a python function
```
pulsar-admin functions create --name ptest --inputs ptest-in --output ptest-out --py $HOME/workspace-pulsar/apache-pulsar-2.8.0/examples/python-examples/exclamation_function.py --classname exclamation_function.ExclamationFunction
```

Consume messages (in one terminal window)
```
pulsar-client consume -s sub -n 0 ptest-out
```

Produce 10 message
```
pulsar-client produce -m "test message $(date)" -n 10 ptest-in
```

Uninstall function
```
pulsar-admin functions delete --name ptest
```


