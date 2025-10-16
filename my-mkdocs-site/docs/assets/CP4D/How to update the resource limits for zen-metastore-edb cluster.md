**How to update the resource limits for zen-metastore-edb cluster**  

**Introduction** 

The purpose of this article is to demonstrate how to update the resource limits for zen-metastore-edb cluster. 

In Cloud Pak for Data 5.x, the zen-metastore cluster is managed by the postgresql operator. In this article we have listed the correct procedure for updating the resource limits for zen-metastore-edb cluster, so that the changes are persisted. 


Note: 

The following steps are for updating the zen-metastore-edb resource limits, this operation will require downtime as it may cause your zen-metastore-edb cluster to failover momentarily. 


**1.Backup the current zenService cr relevant settings** 

```export PROJECT_CPD_INST_OPERANDS=cpd``` 

```oc project ${PROJECT_CPD_INST_OPERANDS} ```

```oc get zenservice lite-cr -o yaml > lite-cr.yaml ```

```oc get clusters.postgresql.k8s.enterprisedb.io zen-metastore-edb -o yaml > zen-metastore-edb-cluster.yaml ```

**2.Verify whether the zenservice CR is in Completed status**

```oc get zenservice lite-cr -ojsonpath="{.status.zenStatus}{'\n'}" ```

**#Output example:**

```Completed ```

**3.Verify the zen-metastore-edb cluster is in healthy state** 

```oc get clusters.postgresql.k8s.enterprisedb.io zen-metastore-edb ```

**#Output example:**

```
NAME                AGE   INSTANCES   READY   STATUS                     PRIMARY 

zen-metastore-edb   95m   2           2       Cluster in healthy state   zen-metastore-edb-2 

```

**4.Review the current resource limits applied on the zen-metastore-edb cluster** 

```oc get clusters.postgresql.k8s.enterprisedb.io zen-metastore-edb -o jsonpath="{.spec.resources}{'\n'}" ```

**5.Update the new resource limits by patching the zenservice CR** 

```
oc patch zenservice lite-cr \ 

--namespace ${PROJECT_CPD_INST_OPERANDS} \ 

--type=merge \ 

--patch '{"spec": {"ZenCoreMetaStoreEdb": {"name": "zen-metastore-edb","kind": "Cluster","instances": 2,"resources": {  "limits": {    "cpu": "2",    "memory": "1200Mi",    "ephemeral-storage": "512Mi"  }}}}}' 
```

**6. Wait for your zenservice CR to be in completed state and zen-metastore-edb cluster to be in healthy state**

```
oc get zenservice lite-cr -ojsonpath="{.status.zenStatus}{'\n'}" 
```

**#Output example:** 

```Completed ```

```oc get clusters.postgresql.k8s.enterprisedb.io zen-metastore-edb ```

**#Output example:**

```
NAME                AGE   INSTANCES   READY   STATUS                     PRIMARY 

zen-metastore-edb   95m   2           2       Cluster in healthy state   zen-metastore-edb-2 
```

**7.Verify whether the new resource limits have been updated on your zen-metastore-edb cluster** 

```
oc get clusters.postgresql.k8s.enterprisedb.io zen-metastore-edb -o jsonpath="{.spec.resources}{'\n'}" 
```

**Reference** 

- [IBM Cloud Pak for Data: How to update resource limits for zen-metastore-edb cluster](https://www.ibm.com/support/pages/ibm-cloud-pak-data-how-update-resource-limits-zen-metastore-edb-cluster-ibm-cloud-data-v47x-and-later-releases)