#egress test

create a project
```
oc new-project egress-test
```
create the test app
```
oc new-app https://github.com/raffaelespazzoli/openshift-enablement-exam --context-dir=misc/egress --strategy=docker --name=egress-test -l app=egress-test -e HOST=egress-svc -n egress-test
```
patch the new service to become be of loadbalancer type
```
oc patch svc egress-test -p '
"spec": {
  "type": "LoadBalancer"
  }'
```
create the egress pod
```
BASTION_IP=`gcloud compute addresses list | grep ose-bastion | awk '{print $3}'`
oc process egress.yaml -v EGRESS_SOURCE=10.128.0.12,EGRESS_GATEWAY=`ssh $BASTION_IP ip route show 0.0.0.0/0 | awk '{print $3}'`,EGRESS_DESTINATION=`oc get service | grep egress-test | awk '{print $3}'`
```

