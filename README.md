# Non Routable Secondary CIDR Patterns

This repo contains sample files to demonstrate how secondary non-routable CIDRs can be used on VPCs for non-workload subnets including TGW subnets and GWLB endpoint subnets. This is a sample implementation from [AWS prescriptive guidance](https://docs.aws.amazon.com/prescriptive-guidance/latest/patterns/preserve-routable-ip-space-in-multi-account-vpc-designs-for-non-workload-subnets.html).



## Lab set up

Use the following steps to set up a pair of VPCs that leverage a TGW.

1. Run the `tgw.yaml` to create the TGW and TGW route tables. (Default parameters are ok)
2. Run the `public-vpc.yaml` to create a VPC that will attach to the TGW. 
   - leave default parameters
   - Set the `TransitGatewayId` parameter. The `tgw.yaml` output has the TransitGatewayId needed.
3. Associate and propagate the TGW attachment to the `sandbox-route-domain` TGW routing table.
4. (Optional) Run the `public-vpc.yaml` again to create an additional VPC. Again, associate and propagate the routes.

### Lab setup options
#### Option 1 - TGW subnets (default)
The Lab Setup described above will deploy a TGW and a VPC attached to the TGW. It will use the non-routable CIDR for the TGW subnets.

#### Option 2 - TGW and GWLBe subnets
This option will deploy a TGW and a VPC attached to the TGW. The VPC will also have GWLBe subnets used for ingress routing from the IGW to an appliance of your choosing. This option does not deploy endpoints within the GWLBe subnets, rather demonstrates how the pattern can be applied for your appliances.

**Important Note** - An existing GWLB endpoint service is required.

Follow the deployment steps from Option 1. On the public-vpc.yaml template, specify the following parameters:
- `UseIngressRouting` -  **true**
- `IngressFirewallGWLBEServiceName` - Specify the name of your existing GWLB endpoint service (VPC PrivateLink Service Name).

Afterwards you can deploy your GWLB VPC endpoints into the GWLBe subnets.

#### Option 3 - TGW and GWLBe subnets plus nonroutable workload subnets
This option builds on Option 2 by deploying an additional set of non-routable workload subnets to the VPC. These can be used for EKS deployments for example where the only required direct connectivity is from the routable CIDR range within the VPC.

Follow the deployment steps for option 2. Specify **true** for the `CreateNonRoutableWorkloadSubnets` parameter on the public-vpc.yaml template to deploy these additional non-routable workload subnets.

#### Option 4 - TGW and nonroutable workload subnets
This option builds on Option 1 by deploying an additional set of non-routable workload subnets to the VPC. These can be used for EKS deployments for example where the only required direct connectivity is from the routable CIDR range within the VPC.

Follow the deployment steps for option 1. Specify **true** for the `CreateNonRoutableWorkloadSubnets` parameter on the public-vpc.yaml template to deploy these additional non-routable workload subnets.

## Reference architecture 
There are two reference architectures for this pattern; one with both subnets for TGW attachments and GWLBe, and another with just TGW attachments.

The following diagram represents a reference architecture for a 2 AZ VPC implementation that uses a non-routable CIDR range for the TGW attachment subnet and the GWLBe subnet. In this example the /23 routable CIDR is divided up and fully allocated to routable subnets.

![Pattern 1](/docs/vpc-designs-public-2AZ-IngressRouting.drawio.png)

The following diagram represents a reference architecture for a 2 AZ VPC implementation that uses a non-routable CIDR range only for the TGW attachments subnet. In this example the /23 routable CIDR is divided up and fully allocated to routable subnets.

![Pattern 2](/docs/vpc-designs-public-2AZ.drawio.png)

These two patterns can be extended to support additional non-routable workload subnets as demonstrated in Option 3 and Option 4 above.

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

