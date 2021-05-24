---
title: "Backup Reference"
layout: docs
---

## Exclude Specific Items from Backup

It is possible to exclude individual items from being backed up, even if they match the resource/namespace/label selectors defined in the backup spec. To do this, label the item as follows:

```bash
kubectl label -n <ITEM_NAMESPACE> <RESOURCE>/<NAME> velero.io/exclude-from-backup=true
```

## Specify Backup Orders of Resources of Specific Kind

To backup resources of specific Kind in a specific order, use option --ordered-resources to specify a mapping Kinds to an ordered list of specific resources of that Kind.  Resource names are separated by commas and their names are in format 'namespace/resourcename'. For cluster scope resource, simply use resource name. Key-value pairs in the mapping are separated by semi-colon.  Kind name is in plural form.

```bash
velero backup create backupName --include-cluster-resources=true --ordered-resources 'pods=ns1/pod1,ns1/pod2;persistentvolumes=pv4,pv8' --include-namespaces=ns1
velero backup create backupName --ordered-resources 'statefulsets=ns1/sts1,ns1/sts0' --include-namespaces=ns1
```
## Enable Kubernetes API Pagination

By default, Velero uses a single LIST API call for each resource type from the Kubernetes API when collecting items into a backup. This provides a fully consistent list. However, on clusters with a very large number of objects, the performance of this API call can be degraded and eventually be timed out by the Kubernetes apiserver.

The `--client-page-size` flag for the Velero server configures Velero to attempt to split the list up over multiple requests. Each request will contain no more objects than the given page size. If the object list is modified between requests, Velero will fall back to attempting a full list in a single API call.

If pagination is needed on a large cluster, a good starting page size is 500. This is the default value of `kubectl`'s similar `--chunk-size` flag. You can then experiment with higher values, noting their impact on the relevant `apiserver_request_duration_seconds_*` metrics from the Kubernetes apiserver.
