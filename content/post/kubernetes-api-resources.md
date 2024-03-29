---
title: kubernetes-api-resources
description: List of Kubernetes API Resources
tags: ["kubernetes", "api-resources"]
date: 2023-07-21

---

# List of Kubernetes API Resources

Kubernetes Version: 1.25
Cloud Provider: AWS EKS

```
| NAME                              SHORTNAMES   APIVERSION                             NAMESPACED   KIND                             VERBS                                                        |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| apiservices                                    apiregistration.k8s.io/v1              false        APIService                       [create delete deletecollection get list patch update watch] |
| bindings                                       v1                                     true         Binding                          [create]                                                     |
| certificatesigningrequests        csr          certificates.k8s.io/v1                 false        CertificateSigningRequest        [create delete deletecollection get list patch update watch] |
| clusterrolebindings                            rbac.authorization.k8s.io/v1           false        ClusterRoleBinding               [create delete deletecollection get list patch update watch] |
| clusterroles                                   rbac.authorization.k8s.io/v1           false        ClusterRole                      [create delete deletecollection get list patch update watch] |
| componentstatuses                 cs           v1                                     false        ComponentStatus                  [get list]                                                   |
| configmaps                        cm           v1                                     true         ConfigMap                        [create delete deletecollection get list patch update watch] |
| controllerrevisions                            apps/v1                                true         ControllerRevision               [create delete deletecollection get list patch update watch] |
| cronjobs                          cj           batch/v1                               true         CronJob                          [create delete deletecollection get list patch update watch] |
| csidrivers                                     storage.k8s.io/v1                      false        CSIDriver                        [create delete deletecollection get list patch update watch] |
| csinodes                                       storage.k8s.io/v1                      false        CSINode                          [create delete deletecollection get list patch update watch] |
| csistoragecapacities                           storage.k8s.io/v1                      true         CSIStorageCapacity               [create delete deletecollection get list patch update watch] |
| customresourcedefinitions         crd,crds     apiextensions.k8s.io/v1                false        CustomResourceDefinition         [create delete deletecollection get list patch update watch] |
| daemonsets                        ds           apps/v1                                true         DaemonSet                        [create delete deletecollection get list patch update watch] |
| deployments                       deploy       apps/v1                                true         Deployment                       [create delete deletecollection get list patch update watch] |
| endpoints                         ep           v1                                     true         Endpoints                        [create delete deletecollection get list patch update watch] |
| endpointslices                                 discovery.k8s.io/v1                    true         EndpointSlice                    [create delete deletecollection get list patch update watch] |
| eniconfigs                                     crd.k8s.amazonaws.com/v1alpha1         false        ENIConfig                        [delete deletecollection get list patch create update watch] |
| events                            ev           v1                                     true         Event                            [create delete deletecollection get list patch update watch] |
| events                            ev           events.k8s.io/v1                       true         Event                            [create delete deletecollection get list patch update watch] |
| flowschemas                                    flowcontrol.apiserver.k8s.io/v1beta3   false        FlowSchema                       [create delete deletecollection get list patch update watch] |
| horizontalpodautoscalers          hpa          autoscaling/v2                         true         HorizontalPodAutoscaler          [create delete deletecollection get list patch update watch] |
| ingressclasses                                 networking.k8s.io/v1                   false        IngressClass                     [create delete deletecollection get list patch update watch] |
| ingresses                         ing          networking.k8s.io/v1                   true         Ingress                          [create delete deletecollection get list patch update watch] |
| jobs                                           batch/v1                               true         Job                              [create delete deletecollection get list patch update watch] |
| leases                                         coordination.k8s.io/v1                 true         Lease                            [create delete deletecollection get list patch update watch] |
| limitranges                       limits       v1                                     true         LimitRange                       [create delete deletecollection get list patch update watch] |
| localsubjectaccessreviews                      authorization.k8s.io/v1                true         LocalSubjectAccessReview         [create]                                                     |
| mutatingwebhookconfigurations                  admissionregistration.k8s.io/v1        false        MutatingWebhookConfiguration     [create delete deletecollection get list patch update watch] |
| namespaces                        ns           v1                                     false        Namespace                        [create delete get list patch update watch]                  |
| networkpolicies                   netpol       networking.k8s.io/v1                   true         NetworkPolicy                    [create delete deletecollection get list patch update watch] |
| nodes                             no           v1                                     false        Node                             [create delete deletecollection get list patch update watch] |
| persistentvolumeclaims            pvc          v1                                     true         PersistentVolumeClaim            [create delete deletecollection get list patch update watch] |
| persistentvolumes                 pv           v1                                     false        PersistentVolume                 [create delete deletecollection get list patch update watch] |
| poddisruptionbudgets              pdb          policy/v1                              true         PodDisruptionBudget              [create delete deletecollection get list patch update watch] |
| pods                              po           v1                                     true         Pod                              [create delete deletecollection get list patch update watch] |
| podtemplates                                   v1                                     true         PodTemplate                      [create delete deletecollection get list patch update watch] |
| priorityclasses                   pc           scheduling.k8s.io/v1                   false        PriorityClass                    [create delete deletecollection get list patch update watch] |
| prioritylevelconfigurations                    flowcontrol.apiserver.k8s.io/v1beta3   false        PriorityLevelConfiguration       [create delete deletecollection get list patch update watch] |
| replicasets                       rs           apps/v1                                true         ReplicaSet                       [create delete deletecollection get list patch update watch] |
| replicationcontrollers            rc           v1                                     true         ReplicationController            [create delete deletecollection get list patch update watch] |
| resourcequotas                    quota        v1                                     true         ResourceQuota                    [create delete deletecollection get list patch update watch] |
| rolebindings                                   rbac.authorization.k8s.io/v1           true         RoleBinding                      [create delete deletecollection get list patch update watch] |
| roles                                          rbac.authorization.k8s.io/v1           true         Role                             [create delete deletecollection get list patch update watch] |
| runtimeclasses                                 node.k8s.io/v1                         false        RuntimeClass                     [create delete deletecollection get list patch update watch] |
| secrets                                        v1                                     true         Secret                           [create delete deletecollection get list patch update watch] |
| securitygrouppolicies             sgp          vpcresources.k8s.aws/v1beta1           true         SecurityGroupPolicy              [delete deletecollection get list patch create update watch] |
| selfsubjectaccessreviews                       authorization.k8s.io/v1                false        SelfSubjectAccessReview          [create]                                                     |
| selfsubjectrulesreviews                        authorization.k8s.io/v1                false        SelfSubjectRulesReview           [create]                                                     |
| serviceaccounts                   sa           v1                                     true         ServiceAccount                   [create delete deletecollection get list patch update watch] |
| services                          svc          v1                                     true         Service                          [create delete deletecollection get list patch update watch] |
| statefulsets                      sts          apps/v1                                true         StatefulSet                      [create delete deletecollection get list patch update watch] |
| storageclasses                    sc           storage.k8s.io/v1                      false        StorageClass                     [create delete deletecollection get list patch update watch] |
| subjectaccessreviews                           authorization.k8s.io/v1                false        SubjectAccessReview              [create]                                                     |
| tokenreviews                                   authentication.k8s.io/v1               false        TokenReview                      [create]                                                     |
| validatingwebhookconfigurations                admissionregistration.k8s.io/v1        false        ValidatingWebhookConfiguration   [create delete deletecollection get list patch update watch] |
| volumeattachments                              storage.k8s.io/v1                      false        VolumeAttachment                 [create delete deletecollection get list patch update watch] |
```
