1. In Hub Cluster - setup GitOps, AWS Route

cd operator-setup
ansible-playbook playbooks/ocp4_workload_connectivity_link.yml  -e ACTION=create -i inventories/inventory.template -e SETUP=HUB

2. Login into ArgoCD from OpenShift
Username: admin
Password: 
HUB_ARGO_PWD="$(oc get secret openshift-gitops-cluster -n openshift-gitops --template="{{index .data \"admin.password\" | base64decode}}")"


3. Add the following Label the clusters in your argocd instance and click SAVE
```
argocd.argoproj.io/secret-type=cluster
deployment.kuadrant.io/hub=true
vendor=OpenShift
```
4. Login to ArgoCD from CLI
```
SPEC_DOMAIN="$(oc get ingresses.config/cluster -o jsonpath={.spec.domain})"
argocd login openshift-gitops-server-openshift-gitops.$SPEC_DOMAIN:443 --insecure --username admin --password $HUB_ARGO_PWD --skip-test-tls --grpc-web
```

5. Run setup on hub
```
make deploy ARGOCD_NAMESPACE=openshift-gitops
```

6. Setup echo API
```
cd operator-setup
ansible-playbook playbooks/ocp4_workload_connectivity_link.yml   -i  ../operator-setup/inventories/inventory.template -e ACTION=create -e SETUP=DEMO
```

-----------

In Spoke Cluster -> Setup GitOps

cd operator-setup
ansible-playbook playbooks/ocp4_workload_connectivity_link.yml  -e ACTION=create -e ENV=SPOKE -i inventories/inventory.template -e SETUP=SPOKE

