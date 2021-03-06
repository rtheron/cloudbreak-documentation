## Security overview

Cloudbreak utilizes cloud provider security resources such as virtual networks, security groups, and identity and access management:

1. **Network isolation** is achieved via user-configured virtual networks and subnets.  
    Read more about [virtual networks](#virtual-networks).  
2. **Network security** is achieved via out-of-the-box security group settings.  
    Read more about [network security](#network-security).   
3. **Controlled use of cloud resources** using IAM roles (AWS, GCP) or Active Directory (in case of Azure). 
    Read more about [identity management](#identity-management).    
 

### Virtual networks

Cloud providers use virtual networks which resemble traditional networks. Depending on the options that you selected during deployment, your Cloudbreak instance and clusters are launched into new or existing cloud provider networking infrastructure (virtual networks and subnets). For more information about virtual networks, refer to the cloud-provider documentation:
  
| Cloud provider | External documentation link |
|---|---|
| AWS | [Amazon Virtual Private Cloud (Amazon VPC)](https://aws.amazon.com/documentation/vpc/) |
| Azure | [Microsoft Azure Virtual Network](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview) |
| Google Cloud Platform | [Virtual Private Cloud (VPC) network](https://cloud.google.com/compute/docs/vpc/) |
| OpenStack | [Network](https://docs.openstack.org/mitaka/networking-guide/intro-os-networking.html) |


### Network security 

Security groups are set up to control network traffic to the instances in the system.

Cloudbreak uses public IP addresses when communicating with cluster nodes. On AWS, you can configure it to use private IPs instead. For instructions, refer to [Configure communication via private IPs on AWS](trouble-cluster.md#configure-communication-via-private-ips-on-aws).  

#### Cloudbreak instance security group

The following table lists the minimum security group port configuration required for the Cloudbreak instance:

| Inbound port | Description |
|---|---|
| 22 | SSH access to the Cloudbreak VM. |
| 80 | HTTP access to the Cloudbreak UI. This is automatically redirected to the HTTPS (443) port. |
| 443 | HTTPS access to the Cloudbreak UI. |

#### Default cluster security groups 

For clusters, Cloudbreak provides the following security group settings. If you do not modify these settings, these default security rules will be created. You can modify these rules either when creating or, if you don't want to use security group, remove them. 

As an alternative to creating new security groups, you can select from your existing set of security groups, which can be modified using the cloud provider tools. 
 
**Cluster host group with Ambari server**

| Inbound port | Description |
|---|---|
| 22 | SSH access to the VM instance. This port is also used by Cloudbreak to communicate with the cluster. |
| 443 | HTTPS access to the Ambari UI. |
| 9443 | Management port, used by Cloudbreak to communicate with the cluster node VM. |

<div class="danger">
<p class="first admonition-title">Important</p>
<p class="last">
By default, ports 22, 443, and 9443 are set to 0.0.0.0/0 CIDR for inbound access on the Ambari node security group. We strongly recommend that you limit this CIDR, considering the following restrictions:
<ul><li>Ports 22 and 9443 must be open to Cloudbreak's CIDR. You can set CB_DEFAULT_GATEWAY_CIDR in your Cloudbreak's Profile file in order to automatically open ports 22 and 9443 to your Cloudbreak IP. Refer to <a href="../security-cb-inbound/index.html">Restricting inbound access from Cloudbreak to cluster</a>.</li>
<li>Port 22 must be open to your CIDR if you would like to access the master node via SSH.</li>
<li>Port 443 must be open to your CIDR if you would like to access Ambari web UI in a browser.</li>
</ul>  
</p>
</div>


**Cluster host groups without the Ambari server**

| Inbound port | Description |
|---|---|
| 22 | SSH access to the VM instance. |

<div class="danger">
<p class="first admonition-title">Important</p>
<p class="last">
By default, port 22 is set to 0.0.0.0/0 CIDR for inbound access on non-Ambari node security groups. We strongly recommend that you remove it.</p>
</div>

When creating a new security group, Cloudbreak uses the following naming convention: `<clustername>-ClusterNodeSecurityGroup<hostgroupname>` 


<div class="danger">
<p class="first admonition-title">Important</p>
<p class="last">
Depending on what services you are including, you need to open additional ports as required by these services. For example, when using the Flow Management blueprint, you must open port 9091 for NiFi (on NiFI host group) and port 61443 for NiFI Registry (on the Services host group).  
</p>
</div>


### Identity management

To securely control access to cloud resources, cloud providers use identity management services such as IAM roles (AWS and GCP) and Active Directory (Azure). 

| Cloud provider | External documentation link | 
|---|---|
| AWS | [AWS Identity and Access Management (IAM)](http://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) |
| Azure | [Azure Active Directory ((Azure AD))](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-whatis) | 
| Google | [Google Cloud Identity and Access Management (IAM)](https://cloud.google.com/iam/docs/overview) | 
| OpenStack | [Keystone](https://docs.openstack.org/keystone/pike/) |

Cloudbreak utilizes cloud provider’s identity management services via Cloudbreak credential. After launching Cloudbreak on your chosen cloud provider, you must create a Cloudbreak credential, which allows Cloudbreak to authenticate with your cloud provider identity management service. Only after you have completed this step, Cloudbreak can create resources on your behalf. 


#### Authentication with AWS

When launching Cloudbreak on AWS, you must select a way for Cloudbreak to authenticate with your AWS account and create resources on your behalf. While key-based authentication uses your AWS access key and secret key, role-based authentication uses IAM roles.

{!docs/common/aws-launch-authentication-role-based-intro.md!}

**Related links**  
[Meet the prerequisites: Authentication](aws-launch.md#authentication)  


#### Authentication with Azure

After launching Cloudbread on Azure, you are required to create a Cloudbreak credential, which allows Cloudbreak to authenticate with your Azure Active Directory. 

You have two options:

* Interactive: The app and service principal creation and role assignment are fully automated, so the only input that you need to provide to Cloudbreak is your Subscription ID and Directory ID. 

* App-based: The app and service principal creation and role assignment are not automated You must create an Azure Active Directory application registration and then provide its parameters to Cloudbreak, in addition to providing your Subscription ID and Directory ID. 

**Related links**  
[Create Cloudbreak credential](azure-launch.md#create-cloudbreak-credential)  


#### Authentication with GCP

After launching Cloudbreak on GCP, you are required to register a service account in Cloudbreak via creating a Cloudbreak credential. Cloudbreak uses this account to authenticate with the GCP identity management service.

**Related links**  
[Meet the prerequisites: Service account](gcp-launch.md#service-account)  


#### Authentication with OpenStack 

After launching Cloudbreak on OpenStack, you are required to create a Cloudbreak credential, which allows Cloudbreak to authenticate with keystone. 


**Related links**  
[Create Cloudbreak credential](os-launch.md#create-cloudbreak-credential)  
