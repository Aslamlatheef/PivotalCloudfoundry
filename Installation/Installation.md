# Installation of PCF 2.4 ( The latest at the moment)

## How does licensing model works
PCF pass works on the Workspace capacity model and you can avail the detials from https://run.pivotal.io/pricing/
PCF on-prem - Contact sales


## On Vmware
### Installation

Create Vmware Cluster --> download the Opsmanager OVF / Download Bosh OVF --> import in Vmware --> Create OPSmanager --> configure the authentication --> configure Vmware Cluster for bosh

[Requirements](https://docs.pivotal.io/pivotalcf/2-4/customizing/vsphere.html#vsphere-reqs)

[Installation](https://www.youtube.com/watch?v=wz-y3Zhd2FQ)

[Installation](https://github.com/pivotal-cf/docs-pcf-install)

[Vsphere Security Guide](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-security-guide.pdf)

[Deploying opsmanager](https://docs.pivotal.io/pivotalcf/2-4/om/vsphere/deploy.html)

[Configuring Bosh](https://docs.pivotal.io/pivotalcf/2-4/om/vsphere/config.html)

[install ipsec add-on](https://docs.pivotal.io/addon-ipsec/1-9/index.html)

[Configure NXT tile if required](https://github.com/Aslamlatheef/PivotalCloudfoundry/blob/master/Installation/nsxt_21_ncp_kubernetes.pdf)

[Deploying PAS on VSPHERE](https://docs.pivotal.io/pivotalcf/2-4/customizing/config-er-vmware.html)

[create user accounts](https://docs.pivotal.io/pivotalcf/2-4/opsguide/creating-account.html)

[Installing CF commandline](https://docs.pivotal.io/pivotalcf/2-4/cf-cli/install-go-cli.html)

Also read

[Configuring availability zones](https://docs.pivotal.io/pivotalcf/2-4/customizing/understand-az.html)

[Deploying PAS with NXT networking](https://docs.pivotal.io/pivotalcf/2-4/customizing/vsphere-nsx-t.html)

[Deploying BOSH with Vsphere HA](https://docs.pivotal.io/pivotalcf/2-4/customizing/resurrector.html)

[Configuring SSL termination](https://docs.pivotal.io/pivotalcf/2-4/opsguide/ssl-term.html)

## On Azure
### Installation 

Earlier Azure uses ARM template, but now it has been depricated. you can now deploy PCF via Terraform.
Please create Service princepal with contributor access [Clickhere](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-create-service-principals) 

[Azure Pre-requirements](https://docs.pivotal.io/pivotalcf/2-4/customizing/azure.html)

[Preparing to Deploy Ops Manager on Azure Manually](https://docs.pivotal.io/pivotalcf/2-4/om/azure/prepare-env-manual.html)

[Deploying Ops Manager on Azure Manually](https://docs.pivotal.io/pivotalcf/2-4/om/azure/deploy-manual.html)

[Deploying BOSH and Ops Manager on Azure Using Terraform](https://docs.pivotal.io/pivotalcf/2-4/om/azure/prepare-env-terraform.html)

[Configuring BOSH Director on Azure Manually](https://docs.pivotal.io/pivotalcf/2-4/om/azure/config-manual.html)

[Deploying PAS on Azure](https://docs.pivotal.io/pivotalcf/2-4/customizing/azure-er-config.html)

### Azure GOVT CLOUD
[Configuring BOSH Director on Azure Government Cloud](https://docs.pivotal.io/pivotalcf/2-4/om/azure/gov-cloud.html)

[Preparing to Deploy Ops Manager on Azure Manually](https://docs.pivotal.io/pivotalcf/2-4/om/azure/prepare-env-manual.html)

[Deploying Ops Manager on Azure Manually](https://docs.pivotal.io/pivotalcf/2-4/om/azure/deploy-manual.html)

[Configuring BOSH Director on Azure Manually](https://docs.pivotal.io/pivotalcf/2-4/om/azure/config-manual.html)

### Azure Germany
[Configuring BOSH Director on Azure Germany](https://docs.pivotal.io/pivotalcf/2-4/om/azure/german-cloud.html)

[Preparing to Deploy Ops Manager on Azure Manually](https://docs.pivotal.io/pivotalcf/2-4/om/azure/prepare-env-manual.html)

[Deploying Ops Manager on Azure Manually](https://docs.pivotal.io/pivotalcf/2-4/om/azure/deploy-manual.html)

[Configuring BOSH Director on Azure Manually](https://docs.pivotal.io/pivotalcf/2-4/om/azure/config-manual.html)

## AWS (Amazon WebServices)

[Pre-requirements](https://docs.pivotal.io/pivotalcf/2-4/customizing/aws.html)

**[Installing PCF on AWS Manually](https://docs.pivotal.io/pivotalcf/2-4/om/aws/prepare-env-manual.html)

  [Configuring BOSH Director on AWS](https://docs.pivotal.io/pivotalcf/2-4/om/aws/config-manual.html)
  
  [Deploying PAS on AWS](https://docs.pivotal.io/pivotalcf/2-4/customizing/pcf-aws-manual-er-config.html)
**[Installing PCF on AWS using Terraform](https://docs.pivotal.io/pivotalcf/2-4/customizing/aws-terraform.html)

  [Deploying Ops Manager on AWS Using Terraform](https://docs.pivotal.io/pivotalcf/2-4/om/aws/prepare-env-terraform.html)
  
  [Configuring BOSH Director on AWS Using Terraform](https://docs.pivotal.io/pivotalcf/2-4/om/aws/config-terraform.html)
  
  [(Optional) Installing the PCF IPsec Add-On](http://docs.pivotal.io/addon-ipsec/installing.html)
  
  [Deploying PAS on AWS (Terraform)](https://docs.pivotal.io/pivotalcf/2-4/customizing/aws-er-config-terraform.html)
  
**[Creating a Proxy ELB for Diego SSH](https://docs.pivotal.io/pivotalcf/2-4/customizing/elb-ssh-proxy.html)

[Upgrading Ops Manager Director on AWS](https://docs.pivotal.io/pivotalcf/2-4/customizing/aws-om-upgrade.html)

## Best Practices
https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html

http://d0.awsstatic.com/whitepapers/aws-security-best-practices.pdf

https://d0.awsstatic.com/whitepapers/architecture/AWS_Well-Architected_Framework.pdf


# PCF on GCP

  
 
















