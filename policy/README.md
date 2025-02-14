# Some Notes and Explanation

## Unable to use fleetMemberships fields (at least for now)
```json
  "type": "Microsoft.ContainerService/fleetMemberships",
  "name": "default",
  "existenceScope": "subscription",
  "allOf": [
      {
        "field": "id",
        "exists": "true"
      }
  ]
```

Only standard fields in fleetMemberships can be used in Azure Policy at the moment because none of the fields of fleetMemberships have an alias defined in ARM.  I'm in the process of requesting them.

## fleets/members resource name pattern
```json
  "resources": [
    {
      "type": "Microsoft.ContainerService/fleets/members",
      "apiVersion": "2024-05-02-preview",
      "name": "[concat(parameters('fleet-name'), '/', parameters('member-name'))]",
      "properties": {
        "clusterResourceId": "[parameters('cluster-resource-id')]"
      }
    }
  ]
```

Since members are a child resource of fleet, the name field must be in the pattern of *parent-fleet*/*membername*, otherwise, you'd get the following error message:

> A nested resource type must have identical number of segments as its resource name. A root resource type must have segment length one greater than its resource name.

More details [here](https://learn.microsoft.com/en-us/azure/azure-resource-manager/troubleshooting/error-invalid-name-segments?tabs=bicep)

## Hub cluster exclusion
```json
  {
    "field": "Microsoft.ContainerService/managedClusters/nodeResourceGroup",
    "notLike": "MC_FL_*"
  },
  {
    "field": "name",
    "notEquals": "hub"
  }
```
is used for excluding all fleet hub clusters as they are managedClusters by themselves. Note that this is not ideal as it relies on the names..
