
kubectl vsphere login --vsphere-username administrator@vsphere.local --server=https://10.214.162.66 --insecure-skip-tls-verify


kubectl get tanzukubernetesreleases

pcarlo@pcarlo3C2T5 tmcsm % kubectl get tanzukubernetesreleases
NAME                                      VERSION                                 READY   COMPATIBLE   CREATED
v1.16.12---vmware.1-tkg.1.da7afe7         v1.16.12+vmware.1-tkg.1.da7afe7         False   False        2d8h
....
v1.24.9---vmware.1-tkg.4                  v1.24.9+vmware.1-tkg.4                  True    True         2d8h




k apply -f tkg-service-configuration.yaml


THIS FILE USE apiVersion: cluster.x-k8s.io/v1beta1
k apply -f tmcsm-cluster-8-v1beta1.yaml

pcarlo@pcarlo3C2T5 tmcsm % k get cluster -n tmcsm
NAME              PHASE         AGE     VERSION
tmcsm-cluster     Provisioned   5m30s   v1.24.9+vmware.1
pcarlo@pcarlo3C2T5 tmcsm %



kubectl vsphere login --server 10.214.162.66 --vsphere-username administrator@vsphere.local --tanzu-kubernetes-cluster-name tmcsm-cluster --tanzu-kubernetes-cluster-namespace tmcsm --insecure-skip-tls-verify

Hm6BJR7!yWxNAh8@fxn


kubectl config use-context tmcsm-cluster

kubectl create clusterrolebinding default-tkg-admin-privileged-binding --clusterrole=psp:vmware-system-privileged --group=system:authenticated


pcarlo@pcarlo3C2T5 tmcsm % tanzu package available list -A

  NAMESPACE  NAME  DISPLAY-NAME
pcarlo@pcarlo3C2T5 tmcsm %



kubectl create ns tanzu-standart-packages

tanzu package repository add tanzu-standard --url projects.registry.vmware.com/tkg/packages/standard/repo:v2023.10.16 --namespace tanzu-standart-packages


pcarlo@pcarlo3C2T5 tmcsm % tanzu package available list -A
  NAMESPACE                NAME                                            DISPLAY-NAME
  tanzu-standart-packages  cert-manager.tanzu.vmware.com                   cert-manager
  tanzu-standart-packages  contour.tanzu.vmware.com                        contour
  tanzu-standart-packages  external-csi-snapshot-webhook.tanzu.vmware.com  external-csi-snapshot-webhook
  tanzu-standart-packages  external-dns.tanzu.vmware.com                   external-dns
  tanzu-standart-packages  fluent-bit.tanzu.vmware.com                     fluent-bit
  tanzu-standart-packages  fluxcd-helm-controller.tanzu.vmware.com         Flux Helm Controller
  tanzu-standart-packages  fluxcd-kustomize-controller.tanzu.vmware.com    Flux Kustomize Controller
  tanzu-standart-packages  fluxcd-source-controller.tanzu.vmware.com       Flux Source Controller
  tanzu-standart-packages  grafana.tanzu.vmware.com                        grafana
  tanzu-standart-packages  harbor.tanzu.vmware.com                         harbor
  tanzu-standart-packages  multus-cni.tanzu.vmware.com                     multus-cni
  tanzu-standart-packages  prometheus.tanzu.vmware.com                     prometheus
  tanzu-standart-packages  whereabouts.tanzu.vmware.com                    whereabouts
pcarlo@pcarlo3C2T5 tmcsm %

pcarlo@pcarlo3C2T5 tmcsm % tanzu package available list cert-manager.tanzu.vmware.com -A
  NAMESPACE                NAME                           VERSION                RELEASED-AT
  tanzu-standart-packages  cert-manager.tanzu.vmware.com  1.1.0+vmware.1-tkg.2   2020-11-24 19:00:00 +0100 CET
  tanzu-standart-packages  cert-manager.tanzu.vmware.com  1.1.0+vmware.2-tkg.1   2020-11-24 19:00:00 +0100 CET
  tanzu-standart-packages  cert-manager.tanzu.vmware.com  1.11.1+vmware.1-tkg.1  2023-01-11 13:00:00 +0100 CET
  tanzu-standart-packages  cert-manager.tanzu.vmware.com  1.5.3+vmware.2-tkg.1   2021-08-23 19:22:51 +0200 CEST
  tanzu-standart-packages  cert-manager.tanzu.vmware.com  1.5.3+vmware.4-tkg.1   2021-08-23 19:22:51 +0200 CEST
  tanzu-standart-packages  cert-manager.tanzu.vmware.com  1.5.3+vmware.7-tkg.1   2021-08-23 19:22:51 +0200 CEST
  tanzu-standart-packages  cert-manager.tanzu.vmware.com  1.5.3+vmware.7-tkg.3   2021-08-23 19:22:51 +0200 CEST
  tanzu-standart-packages  cert-manager.tanzu.vmware.com  1.7.2+vmware.1-tkg.1   2021-10-29 14:00:00 +0200 CEST
  tanzu-standart-packages  cert-manager.tanzu.vmware.com  1.7.2+vmware.3-tkg.1   2021-10-29 14:00:00 +0200 CEST
  tanzu-standart-packages  cert-manager.tanzu.vmware.com  1.7.2+vmware.3-tkg.3   2021-10-29 14:00:00 +0200 CEST
pcarlo@pcarlo3C2T5 tmcsm %


tanzu package install cert-manager --package cert-manager.tanzu.vmware.com --namespace tanzu-standart-packages --version 1.11.1+vmware.1-tkg.1


pcarlo@pcarlo3C2T5 tmcsm % tanzu package installed list -A
  NAMESPACE                NAME                                      PACKAGE-NAME                                 PACKAGE-VERSION                 STATUS
  tanzu-standart-packages  cert-manager                              cert-manager.tanzu.vmware.com                1.11.1+vmware.1-tkg.1           Reconcile succeeded
OTHER...........
pcarlo@pcarlo3C2T5 tmcsm %

pcarlo@pcarlo3C2T5 tmcsm % k get po -n cert-manager
NAME                                       READY   STATUS    RESTARTS   AGE
cert-manager-6b9d659bbc-2tcwj              1/1     Running   0          2m29s
cert-manager-cainjector-6c9c5f69d5-bfrk2   1/1     Running   0          2m29s
cert-manager-webhook-597fc9b784-77sdz      1/1     Running   0          2m29s
pcarlo@pcarlo3C2T5 tmcsm %



pcarlo@pcarlo3C2T5 tmcsm % k get po -A | grep kapp
tkg-system                     kapp-controller-5df7745586-lvhsw                                             2/2     Running   0             14m
pcarlo@pcarlo3C2T5 tmcsm % 

kubectl create secret generic kapp-controller-config --namespace tkg-system --from-file caCerts=harbor.crt
secret/kapp-controller-config created

pcarlo@pcarlo3C2T5 tmcsm % k get secret -A | grep kapp
tkg-system                     kapp-controller-config                                 Opaque                                1      6s
pcarlo@pcarlo3C2T5 tmcsm %



k apply -f certmanager-issuer-secret.yaml

pcarlo@pcarlo3C2T5 tmcsm % k get secret -n cert-manager
NAME                          TYPE                             DATA   AGE
ca-key-pair                   Opaque                           2      20s
OTHER....
pcarlo@pcarlo3C2T5 tmcsm % 



k apply -f clusterissuer.yaml

pcarlo@pcarlo3C2T5 tmcsm % k get clusterissuer local-issuer -o wide
NAME           READY   STATUS                AGE
local-issuer   True    Signing CA verified   18s
pcarlo@pcarlo3C2T5 tmcsm %




CONFIGURE OKTA
https://www.outofmemory.info/tanzu/tmc/tmcsm/tmcsm-installation-part-3/#assign-app-integration-to-groups


k create ns tmc-local

tanzu package repository add tanzu-mission-control-packages --url "harbor.h2o-4-23130.h2o.vmware.com/tmc-sm/package-repository:1.1.0" --namespace tmc-local

pcarlo@pcarlo3C2T5 tmcsm % tanzu package repository list --namespace tmc-local
  NAME                            SOURCE                                                                      STATUS
  tanzu-mission-control-packages  (imgpkg) harbor.h2o-4-23130.h2o.vmware.com/tmc-sm/package-repository:1.1.0  Reconcile succeeded
pcarlo@pcarlo3C2T5 tmcsm %



tanzu package install tanzu-mission-control -p "tmc.tanzu.vmware.com" --version 1.1.0 --values-file tmc-sm-values.yaml --namespace tmc-local

AT THE BEGINNING che envoy external IP and configure DNS
CHECK DNS Settings for h2o-4-23142.h2o.vmware.com
pcarlo@pcarlo3C2T5 ~ % k get svc -A | grep envoy
tmc-local           contour-envoy                                      LoadBalancer   100.70.89.222    10.214.162.69   80:30752/TCP,443:32115/TCP   21m
tmc-local           contour-envoy-metrics                              ClusterIP      None             <none>          8002/TCP                     21m
pcarlo@pcarlo3C2T5 ~ %




TRAY TO LOGIN: IF
Carlo Pulcini
I retried login, inserting the code, but {"message":"Could not exchange authorization code"}

Ashish Amarnath
This is mostly because TMC SM is being reinstalled and there are stale secrets left over from a previous install.
Please follow these steps to recover from the error.
Please run this command kubectl -n tmc-local delete oidcclient/client.oauth.pinniped.dev-auth-manager-pinniped-oidc-client secret/client.oauth.pinniped.dev-auth-manager-pinniped-oidc-client-client-secret-generated; kubectl -n tmc-local delete po -lapp=authenticator; kubectl delete lease authenticator-leader-elect;
Wait for a couple of minutes for the resources to be reconciled
Use kubectl -n tmc-local get oidcclient/client.oauth.pinniped.dev-auth-manager-pinniped-oidc-client secret/client.oauth.pinniped.dev-auth-manager-pinniped-oidc-client-client-secret-generated to get a confirmation.
Once this is done, please try login again













TO UPDATE
tanzu package installed update tanzu-mission-control --values-file tmc-sm-values.yaml --namespace tmc-local


TO DELETE
pcarlo@pcarlo3C2T5 tmcsm % tanzu package installed delete tanzu-mission-control --namespace tmc-local
Delete package install 'tanzu-mission-control' from namespace 'tmc-local'

Continue? [yN]: y




USEFULL COMMANDS

1335  k -n tmc-local logs -l app=api-gateway
 1336  k -n tmc-local logs -l app=api-gateway -f
 1337  alias kcl='kubectl -n tmc-local'
 1338  kcl get po
 1339  kcl logs -lapp=cluster-agent
 1340  kcl get deploy cluster-agent-service -oyaml | yq .spec.template.spec
 1341  kcl get deploy cluster-agent-server -oyaml | yq .spec.template.spec
 1342  kcl get deploy cluster-agent-service-server -oyaml | yq .spec.template.spec
 1343  tanzu package installed update tanzu-mission-control --values-file tmc-sm-values.yaml --namespace tmc-local\n
 1344  kcl get cm tls-ca-bunles -oyaml | yq .data
 1345  kcl get cm tls-ca-bundles -oyaml | yq .data
 1346  pbpaste | cksum
 1347  pbpaste | cksum
 1348  kcl delete po -lapp=cluster-agent
 1349  kcl get po -lapp=cluster-agent
 1350  kcl logs -lapp=cluster-agent -f


kubectl -n tmc-local logs -c envoy -l app.kubernetes.io/component=envoy -f


SUPERVISOR CLUSTER REGISTRATION
https://docs.vmware.com/en/VMware-Tanzu-Mission-Control/1.1/tanzumc-using/GUID-CC6E721E-43BF-4066-AA0A-F744280D6A03.html

kubectl vsphere login --vsphere-username administrator@vsphere.local --server=https://10.214.162.66 --insecure-skip-tls-verify
kubectl config use-context 10.214.162.66


pcarlo@pcarlo3C2T5 tmcsm % kubectl apply -f agentconfig.yaml
agentconfig.installers.tmc.cloud.vmware.com/tmc-agent-config created
pcarlo@pcarlo3C2T5 tmcsm %

pcarlo@pcarlo3C2T5 tmcsm % kubectl create -f tmc-registration.yaml
Error from server (AlreadyExists): error when creating "tmc-registration.yaml": agentinstalls.installers.tmc.cloud.vmware.com "tmc-agent-installer-config" already exists
DELETE AND RECREATE
CHECK LOGS
pcarlo@pcarlo3C2T5 tmcsm % kubectl -n svc-tmc-c8 describe agentinstall tmc-agent-installer-config
Name:         tmc-agent-installer-config
Namespace:    svc-tmc-c8
Labels:       <none>
Annotations:  <none>
API Version:  installers.tmc.cloud.vmware.com/v1alpha1
Kind:         AgentInstall
Metadata:
  Creation Timestamp:  2024-02-29T21:53:46Z
  Generation:          1
  Managed Fields:
    API Version:  installers.tmc.cloud.vmware.com/v1alpha1
    Fields Type:  FieldsV1
    fieldsV1:
      f:spec:
        .:
        f:operation:
        f:registrationLink:
    Manager:      kubectl-create
    Operation:    Update
    Time:         2024-02-29T21:53:46Z
    API Version:  installers.tmc.cloud.vmware.com/v1alpha1
    Fields Type:  FieldsV1
    fieldsV1:
      f:status:
        .:
        f:message:
        f:status:
    Manager:         tmc-agent-installer
    Operation:       Update
    Subresource:     status
    Time:            2024-02-29T21:54:08Z
  Resource Version:  2261398
  UID:               25245f2b-e1ef-42c9-977a-67c197aa97d4
Spec:
  Operation:          INSTALL
  Registration Link:  https://tmc.h2o-4-23142.h2o.vmware.com/installer?id=79d738df3cbf4ea6e14a876093573e369da3dec0e234a8df636eaaf1f845d0f0&source=registration&type=tkgs
Status:
  Message:  successfully applied registration link
  Status:   INSTALLED
Events:     <none>
pcarlo@pcarlo3C2T5 tmcsm %


BUT STILL DO NOT COME UP ON TMCSM (IT WORKS IN VSHERE GUI)

pcarlo@pcarlo3C2T5 tmcsm % k get po -n svc-tmc-c8
NAME                                 READY   STATUS                  RESTARTS         AGE
domain-local-ds-5zztr                0/1     Init:CrashLoopBackOff   11 (4m21s ago)   36m
domain-local-ds-fhvmm                0/1     Init:CrashLoopBackOff   11 (4m36s ago)   36m
domain-local-ds-sqt7m                0/1     Init:CrashLoopBackOff   11 (4m58s ago)   36m
tmc-agent-installer-28487430-xf5ww   0/1     Completed               0                19s
tmc-bootstrapper-tjcgl               0/1     ImagePullBackOff        0                36m
pcarlo@pcarlo3C2T5 tmcsm %





