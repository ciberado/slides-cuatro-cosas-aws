# Autoscaling Groups

## Mixed fleets

It enhances the ability of the ASG of getting the required instances,
while providing greater spot resiliency. **AWS [recommends](https://docs.aws.amazon.com/autoscaling/ec2/userguide/mixed-instances-groups-set-up-overview.html#mixed-instances-group-instance-flexibility) 
using 10 instance types** as a best practice!

```terraform
resource "aws_autoscaling_group" "nginx" {
  ...
  mixed_instances_policy {
    launch_template {
      ...
      override {
        instance_type     = "t3.micro"
        weighted_capacity = "1"
      }
      override {
        instance_type     = "t3.small"
        weighted_capacity = "2"
      }
    }
  }
}
```

## Mixed with filters

```terraform
  ...
  mixed_instances_policy {
    ...
    launch_template {
      override {
        instance_requirements {
          memory_mib {
            min = 4000
          }

          vcpu_count {
            min = 2
          }
        }
      }
    }
  }
```

### Mixed with spot

It is possible in the ASG to determine which percentage of the instances
should be created as spot and which one should be non-spot. This feature
provides an easy way to balance the risk of interruption in smaller fleets.

```terraform
resource "aws_autoscaling_group" "nginx" {
  ...
  mixed_instances_policy {
    instances_distribution {
      on_demand_allocation_strategy = "prioritized"  // or "lowest-price"
      on_demand_base_capacity       = 2
      on_demand_percentage_above_base_capacity = 25
      spot_allocation_strategy      = "price-capacity-optimized"
    }
    ...
  }
}
```

### Proactive capacity rebalancing

If enabled, the ASG will monitor the risk of spot instance interruption and
replace the affected machines *before* the event occurs.

It will not work if [scale-in protection](https://docs.aws.amazon.com/autoscaling/ec2/userguide/ec2-auto-scaling-instance-protection.html) is active.

```terraform
resource "aws_autoscaling_group" "nginx" {
  ...
  capacity_rebalance  = true
  ...
}
```

### LT License management

[License management](https://mng.workshop.aws/licensemanager.html) can
be integrated, automatically generating an audit of the actual usage of
the software and even limiting the creation of new instances.

```terraform
  ...
  license_specification {
    license_configuration_arn = "arn:aws:license-manager:eu-wes...lic-012def"
  }
  ...
```

::: Notes

This is a very important feature in such a dynamic environment. For example,
it can be managed for [optimizing Fortigate deployments](https://docs.fortinet.com/document/fortigate-public-cloud/7.4.0/aws-administration-guide/397979/deploying-auto-scaling-on-aws).


#### How It Works

It simply **associates a License Manager configuration** ARN with instances launched by the ASG (via Launch Templates), which tracks license consumption based on instance attributes like vCPUs, cores, or count—regardless of software presence. This enables enforcement rules (e.g., launch prevention if limits are exceeded) and usage reporting.
Actual Verification

**For detecting installed software, AWS License Manager relies on separate automated discovery** integrated with Systems Manager Inventory. This scans running instances (and on-premises servers with SSM Agent) for specified applications using product name, publisher, and version rules—then dynamically associates/disassociates licenses upon install/uninstall. LicenseSpecification alone skips this step, assuming consumption from launch.

:::

### Warm pools

A warm pool is a group of pre-initialized (**stopped**, usually) EC2 instances 
aiding an Auto Scaling group to quickly scale out.

```terraform
resource "aws_autoscaling_group" "nginx" {
  ...
  warm_pool {
    pool_state                  = "Hibernated"
    min_size                    = 5
    max_group_prepared_capacity = 10
    instance_reuse_policy {
      reuse_on_scale_in = true
    }
  }
}
```

::: Notes

The ASG will keep between `min_size` and `max_group_prepared_capacity` warmed
instances. For example, with a `desired` of 2 and `min_size` of 4 the
ASG will create four instances and stop/hibernate two of them. By the way,
Windows hibernation is supported since 2019.

`pool_state` can be `stopped`, `running` or `hibernated` indicating the
desired state of the warmed instances.

`reuse_on_scale_in` should be `true` if instance are planned to be kept
in the warm pool after a scale-in event.

:::


### Maximum instance lifetime

For security reasons it is common to set a limit in the amount of time
an instance can be allowed to exist before being **replaced with a fresh one**.
The ASG will try to replace one instance at a time, with a pause between
each termination, but too short intervals may cause a more aggressive behavior.

```terraform
resource "aws_autoscaling_group" "nginx" {
  vpc_zone_identifier = data.aws_subnets.default.ids

  desired_capacity = 50

  max_instance_lifetime = 60*60*24*7
  ...
```

::: Notes

Yes, yes: this feature has been used to replace instances running
applications with **memory leaks** on regular intervals.

:::

[]()

### Auto Scaling Group refreshing

ASG have incorporated a **rolling upgrade** feature called [Instance Refresh](https://docs.aws.amazon.com/autoscaling/ec2/userguide/asg-instance-refresh.html). It provides a well-designed UX through the **web console**,
including the definition of checkpoints. But it can also be configured and triggered using
**Terraform** and the [CLI](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/autoscaling/start-instance-refresh.html).

```terraform
resource "aws_autoscaling_group" "nginx" {
  ...
  instance_refresh {
    strategy = "Rolling"
    preferences {
      checkpoint_percentages = [33, 66, 100]
      checkpoint_delay       = 60*10
      min_healthy_percentage = 75
      skip_matching          = true
    }
    triggers = ["launch_template"]
  }
}


::: Notes


```bash
#!/bin/sh
 
sudo apt update
sudo apt install openjdk-17-jre-headless -y
wget https://github.com/ciberado/pokemon-java/releases/download/v2.0.0/pokemon-2.0.0.jar
# wget https://github.com/ciberado/pokemon-java/releases/download/v2.0.1/pokemon-2.0.1.jar
java -jar pokemon*.jar
```

:::