---
subcategory: "AnalyticDB for PostgreSQL (GPDB)"
layout: "alicloud"
page_title: "Alicloud: alicloud_gpdb_account"
sidebar_current: "docs-alicloud-resource-gpdb-account"
description: |-
  Provides a Alicloud GPDB Account resource.
---

# alicloud_gpdb_account

Provides a GPDB Account resource.

For information about GPDB Account and how to use it, see [What is Account](https://www.alibabacloud.com/help/doc-detail/86924.htm).

-> **NOTE:** Available since v1.142.0.

## Example Usage

Basic Usage

```terraform
variable "name" {
  default = "tf-example"
}
data "alicloud_resource_manager_resource_groups" "default" {}
data "alicloud_gpdb_zones" "default" {}

resource "alicloud_vpc" "default" {
  vpc_name   = var.name
  cidr_block = "10.4.0.0/16"
}
resource "alicloud_vswitch" "default" {
  vswitch_name = var.name
  cidr_block   = "10.4.0.0/24"
  vpc_id       = alicloud_vpc.default.id
  zone_id      = data.alicloud_gpdb_zones.default.ids.0
}

resource "alicloud_gpdb_instance" "default" {
  db_instance_category  = "HighAvailability"
  db_instance_class     = "gpdb.group.segsdx1"
  db_instance_mode      = "StorageElastic"
  description           = var.name
  engine                = "gpdb"
  engine_version        = "6.0"
  zone_id               = data.alicloud_gpdb_zones.default.ids.0
  instance_network_type = "VPC"
  instance_spec         = "2C16G"
  master_node_num       = 1
  payment_type          = "PayAsYouGo"
  private_ip_address    = "1.1.1.1"
  seg_storage_type      = "cloud_essd"
  seg_node_num          = 4
  storage_size          = 50
  vpc_id                = alicloud_vpc.default.id
  vswitch_id            = alicloud_vswitch.default.id
  ip_whitelist {
    security_ip_list = "127.0.0.1"
  }
}

resource "alicloud_gpdb_account" "default" {
  account_name        = "tf_example"
  db_instance_id      = alicloud_gpdb_instance.default.id
  account_password    = "Example1234"
  account_description = "tf_example"
}
```

## Argument Reference

The following arguments are supported:

* `account_description` - (Optional, ForceNew) The description of the account.
  * Starts with a letter.
  * Does not start with `http://` or `https://`.
  * Contains letters, underscores (_), hyphens (-), or digits.
  * Be 2 to 256 characters in length.
* `account_name` - (Required, ForceNew) The name of the account. The account name must be unique and meet the following requirements:
  * Starts with a letter.
  * Contains only lowercase letters, digits, or underscores (_).
  * Be up to 16 characters in length.
  * Contains no reserved keywords.
* `account_password` - (Required) The password of the account. The password must be 8 to 32 characters in length and contain at least three of the following character types: uppercase letters, lowercase letters, digits, and special characters. Special characters include `! @ # $ % ^ & * ( ) _ + - =`.
* `db_instance_id` - (Required, ForceNew) The ID of the instance.

## Attributes Reference

The following attributes are exported:

* `id` - The resource ID of Account. The value formats as `<db_instance_id>:<account_name>`.
* `status` - The status of the account. Valid values: `Active`, `Creating` and `Deleting`.

## Timeouts

The `timeouts` block allows you to specify [timeouts](https://www.terraform.io/docs/configuration-0-11/resources.html#timeouts) for certain actions:

* `create` - (Defaults to 5 mins) Used when create the Account.

## Import

GPDB Account can be imported using the id, e.g.

```shell
$ terraform import alicloud_gpdb_account.example <db_instance_id>:<account_name>
```
