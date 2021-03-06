---
subcategory: "HDInsight"
layout: "azurerm"
page_title: "Azure Resource Manager: azurerm_hdinsight_hadoop_cluster"
description: |-
  Manages a HDInsight Hadoop Cluster.
---

# azurerm_hdinsight_hadoop_cluster

Manages a HDInsight Hadoop Cluster.

## Example Usage

```hcl
resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "West Europe"
}

resource "azurerm_storage_account" "example" {
  name                     = "hdinsightstor"
  resource_group_name      = "${azurerm_resource_group.example.name}"
  location                 = "${azurerm_resource_group.example.location}"
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "example" {
  name                  = "hdinsight"
  resource_group_name   = "${azurerm_resource_group.example.name}"
  storage_account_name  = "${azurerm_storage_account.example.name}"
  container_access_type = "private"
}

resource "azurerm_hdinsight_hadoop_cluster" "example" {
  name                = "example-hdicluster"
  resource_group_name = "${azurerm_resource_group.example.name}"
  location            = "${azurerm_resource_group.example.location}"
  cluster_version     = "3.6"
  tier                = "Standard"

  component_version {
    hadoop = "2.7"
  }

  gateway {
    enabled  = true
    username = "acctestusrgw"
    password = "TerrAform123!"
  }

  storage_account {
    storage_container_id = "${azurerm_storage_container.example.id}"
    storage_account_key  = "${azurerm_storage_account.example.primary_access_key}"
    is_default           = true
  }

  roles {
    head_node {
      vm_size  = "Standard_D3_V2"
      username = "acctestusrvm"
      password = "AccTestvdSC4daf986!"
    }

    worker_node {
      vm_size               = "Standard_D4_V2"
      username              = "acctestusrvm"
      password              = "AccTestvdSC4daf986!"
      target_instance_count = 3
    }

    zookeeper_node {
      vm_size  = "Standard_D3_V2"
      username = "acctestusrvm"
      password = "AccTestvdSC4daf986!"
    }
  }
}
```

## Argument Reference

The following arguments are supported:

* `name` - (Required) Specifies the name for this HDInsight Hadoop Cluster. Changing this forces a new resource to be created.

* `resource_group_name` - (Required) Specifies the name of the Resource Group in which this HDInsight Hadoop Cluster should exist. Changing this forces a new resource to be created.

* `location` - (Required) Specifies the Azure Region which this HDInsight Hadoop Cluster should exist. Changing this forces a new resource to be created.

* `cluster_version` - (Required) Specifies the Version of HDInsights which should be used for this Cluster. Changing this forces a new resource to be created.

* `component_version` - (Required) A `component_version` block as defined below.

* `gateway` - (Required) A `gateway` block as defined below.

* `roles` - (Required) A `roles` block as defined below.

* `storage_account` - (Required) One or more `storage_account` block as defined below.

* `storage_account_gen2` - (Required) A `storage_account_gen2` block as defined below.

* `tier` - (Required) Specifies the Tier which should be used for this HDInsight Hadoop Cluster. Possible values are `Standard` or `Premium`. Changing this forces a new resource to be created.

---

* `tags` - (Optional) A map of Tags which should be assigned to this HDInsight Hadoop Cluster.

---

A `component_version` block supports the following:

* `hadoop` - (Required) The version of Hadoop which should be used for this HDInsight Hadoop Cluster. Changing this forces a new resource to be created.

---

A `gateway` block supports the following:

* `enabled` - (Required) Is the Ambari portal enabled? Changing this forces a new resource to be created.

* `password` - (Required) The password used for the Ambari Portal. Changing this forces a new resource to be created.

-> **NOTE:** This password must be different from the one used for the `head_node`, `worker_node` and `zookeeper_node` roles.

* `username` - (Required) The username used for the Ambari Portal. Changing this forces a new resource to be created.

---

A `head_node` block supports the following:

* `username` - (Required) The Username of the local administrator for the Head Nodes. Changing this forces a new resource to be created.

* `vm_size` - (Required) The Size of the Virtual Machine which should be used as the Head Nodes. Changing this forces a new resource to be created.

* `password` - (Optional) The Password associated with the local administrator for the Head Nodes. Changing this forces a new resource to be created.

-> **NOTE:** If specified, this password must be at least 10 characters in length and must contain at least one digit, one uppercase and one lower case letter, one non-alphanumeric character (except characters ' " ` \).

* `ssh_keys` - (Optional) A list of SSH Keys which should be used for the local administrator on the Head Nodes. Changing this forces a new resource to be created.

-> **NOTE:** Either a `password` or one or more `ssh_keys` must be specified - but not both.

* `subnet_id` - (Optional) The ID of the Subnet within the Virtual Network where the Head Nodes should be provisioned within. Changing this forces a new resource to be created.

* `virtual_network_id` - (Optional) The ID of the Virtual Network where the Head Nodes should be provisioned within. Changing this forces a new resource to be created.

---

A `roles` block supports the following:

* `head_node` - (Required) A `head_node` block as defined above.

* `worker_node` - (Required) A `worker_node` block as defined below.

* `zookeeper_node` - (Required) A `zookeeper_node` block as defined below.

* `edge_node` - (Optional) A `edge_node` block as defined below.

---

A `storage_account` block supports the following:

* `is_default` - (Required) Is this the Default Storage Account for the HDInsight Hadoop Cluster? Changing this forces a new resource to be created.

-> **NOTE:** One of the `storage_account` or `storage_account_gen2` blocks must be marked as the default.

* `storage_account_key` - (Required) The Access Key which should be used to connect to the Storage Account. Changing this forces a new resource to be created.

* `storage_container_id` - (Required) The ID of the Storage Container. Changing this forces a new resource to be created.

-> **NOTE:** This can be obtained from the `id` of the `azurerm_storage_container` resource.

---

A `storage_account_gen2` block supports the following:

* `is_default` - (Required) Is this the Default Storage Account for the HDInsight Hadoop Cluster? Changing this forces a new resource to be created.

-> **NOTE:** One of the `storage_account` or `storage_account_gen2` blocks must be marked as the default.

* `storage_resource_id` - (Required) The ID of the Storage Account. Changing this forces a new resource to be created.

* `filesystem_id` - (Required) The ID of the Gen2 Filesystem. Changing this forces a new resource to be created.

* `managed_identity_resource_id` - (Required) The ID of Managed Identity to use for accessing the Gen2 filesystem. Changing this forces a new resource to be created.

-> **NOTE:** This can be obtained from the `id` of the `azurerm_storage_container` resource.

---

A `worker_node` block supports the following:

* `username` - (Required) The Username of the local administrator for the Worker Nodes. Changing this forces a new resource to be created.

* `vm_size` - (Required) The Size of the Virtual Machine which should be used as the Worker Nodes. Changing this forces a new resource to be created.

* `min_instance_count` - (Optional) The minimum number of instances which should be run for the Worker Nodes. Changing this forces a new resource to be created.

* `password` - (Optional) The Password associated with the local administrator for the Worker Nodes. Changing this forces a new resource to be created.

-> **NOTE:** If specified, this password must be at least 10 characters in length and must contain at least one digit, one uppercase and one lower case letter, one non-alphanumeric character (except characters ' " ` \).

* `ssh_keys` - (Optional) A list of SSH Keys which should be used for the local administrator on the Worker Nodes. Changing this forces a new resource to be created.

-> **NOTE:** Either a `password` or one or more `ssh_keys` must be specified - but not both.

* `subnet_id` - (Optional) The ID of the Subnet within the Virtual Network where the Worker Nodes should be provisioned within. Changing this forces a new resource to be created.

* `target_instance_count` - (Optional) The number of instances which should be run for the Worker Nodes.

* `virtual_network_id` - (Optional) The ID of the Virtual Network where the Worker Nodes should be provisioned within. Changing this forces a new resource to be created.

---

A `zookeeper_node` block supports the following:

* `username` - (Required) The Username of the local administrator for the Zookeeper Nodes. Changing this forces a new resource to be created.

* `vm_size` - (Required) The Size of the Virtual Machine which should be used as the Zookeeper Nodes. Changing this forces a new resource to be created.

* `password` - (Optional) The Password associated with the local administrator for the Zookeeper Nodes. Changing this forces a new resource to be created.

-> **NOTE:** If specified, this password must be at least 10 characters in length and must contain at least one digit, one uppercase and one lower case letter, one non-alphanumeric character (except characters ' " ` \).

* `ssh_keys` - (Optional) A list of SSH Keys which should be used for the local administrator on the Zookeeper Nodes. Changing this forces a new resource to be created.

-> **NOTE:** Either a `password` or one or more `ssh_keys` must be specified - but not both.

* `subnet_id` - (Optional) The ID of the Subnet within the Virtual Network where the Zookeeper Nodes should be provisioned within. Changing this forces a new resource to be created.

* `virtual_network_id` - (Optional) The ID of the Virtual Network where the Zookeeper Nodes should be provisioned within. Changing this forces a new resource to be created.

---

A `edge_node` block supports the following:

* `vm_size` - (Required) The Size of the Virtual Machine which should be used as the Edge Nodes. Changing this forces a new resource to be created.

* `install_script_action` - A `install_script_action` block as defined below.

---

A `install_script_action` block supports the following:

* `name` - (Required) The name of the install script action. Changing this forces a new resource to be created.

* `uri` - (Required) The URI pointing to the script to run during the installation of the edge node. Changing this forces a new resource to be created.

## Attributes Reference

The following attributes are exported:

* `id` - The ID of the HDInsight Hadoop Cluster.

* `https_endpoint` - The HTTPS Connectivity Endpoint for this HDInsight Hadoop Cluster.

* `ssh_endpoint` - The SSH Connectivity Endpoint for this HDInsight Hadoop Cluster.

### Timeouts

~> **Note:** Custom Timeouts is available [as an opt-in Beta in version 1.43 of the Azure Provider](/docs/providers/azurerm/guides/2.0-beta.html) and will be enabled by default in version 2.0 of the Azure Provider.

The `timeouts` block allows you to specify [timeouts](https://www.terraform.io/docs/configuration/resources.html#timeouts) for certain actions:

* `create` - (Defaults to 60 minutes) Used when creating the Hadoop HDInsight Cluster.
* `update` - (Defaults to 60 minutes) Used when updating the Hadoop HDInsight Cluster.
* `read` - (Defaults to 5 minutes) Used when retrieving the Hadoop HDInsight Cluster.
* `delete` - (Defaults to 60 minutes) Used when deleting the Hadoop HDInsight Cluster.

## Import

HDInsight Hadoop Clusters can be imported using the `resource id`, e.g.

```shell
terraform import azurerm_hdinsight_hadoop_cluster.example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mygroup1/providers/Microsoft.HDInsight/clusters/cluster1}
```
