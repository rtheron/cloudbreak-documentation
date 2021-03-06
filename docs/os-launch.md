## Launching Cloudbreak on OpenStack

Before launching Cloudbreak on OpenStack, review and meet the prerequisites. Next, import Cloudbreak image, launch a VM, SSH to the VM, and start Cloudbreak. Once Cloudbreak is running, log in to the Cloudbreak UI and create a Cloudbreak credential.

### Meet minimum system requirements

Before launching Cloudbreak on your OpenStack, make sure that your OpenStack deployment fulfills the following requirements.

#### Supported Linux distributions

The following versions of the [Red Hat distribution of OpenStack](https://www.rdoproject.org/) (RDO) are supported:

* Juno
* Kilo
* Liberty
* Mitaka


#### Standard modules

Cloudbreak requires that the following standard modules are installed and configured on OpenStack:

* Keystone V2 or Keystone V3
* Neutron (Self-service and provider networking)
* Nova (KVM or Xen hypervisor)
* Glance
* Cinder (Optional)
* Heat (Optional but highly recommended, since provisioning through native API calls will be deprecated in the future)

**Related links**  
[Red Hat distribution of OpenStack](https://www.rdoproject.org/) (External)


### Meet the prerequisites

Before launching Cloudbreak on OpenStack, you must meet the following prerequisites.

#### SSH key pair

[Generate a new SSH key pair](faq.md#generate-ssh-key-pair) or use an existing SSH key pair to your OpenStack account. You will be required to select it when launching the VM.

#### Security group

In order to launch Cloudbreak, you must have an existing security group with the following ports open: 22 (for access via SSH), 80 (for access via HTTP), and 443 (for access via HTTPS).

For information about OpenStack security groups, refer to the [OpenStack Administrator Guide](https://docs.openstack.org/ops-guide/index.html).

**Related links**  
[OpenStack Administrator Guide](https://docs.openstack.org/ops-guide/index.html) (External)


<!---Commenting out the regular steps--->
<!---
#### Import images to OpenStack

An OpenStack administrator must perform these steps to add the Cloudbreak deployer image to your OpenStack deployment. 

> Importing prewarmed and base HDP and HDF images is no longer required.

##### Import Cloudbreak deployer image

Import Cloudbreak deployer image using the following steps.

**Steps**

1. Download the latest Cloudbreak deployer image to your local machine:

    <pre><small>curl -O http://public-repo-1.hortonworks.com/HDP/cloudbreak/cloudbreak-deployer-250-2018-04-03.img</small></pre>

2. Set the following environment variables for the OpenStack image import:

    <pre><small>export CBD_LATEST_IMAGE=cloudbreak-deployer-250-2018-04-03.img
export OS_IMAGE_NAME=cloudbreak-deployer-250-2018-04-03.img
export OS_USERNAME=your_os_user_name
export OS_AUTH_URL=your_authentication_url
export OS_TENANT_NAME=your_os_tenant_name</small></pre>


3. Import the new image into your OpenStack:

    <pre><small>glance image-create --name "$OS_IMAGE_NAME" --file "$CBD_LATEST_IMAGE" --disk-format qcow2 --container-format bare --progress</small></pre>

After performing the import, you should be able to see the Cloudbreak deployer image among your other OpenStack images.


##### Import HDP prewarmed image

These steps are no longer required, because if these images are not imported manually, Cloudbreak will import them once you attempt to create an HDP cluster. If you would like to import HDP prewarmed images manually (instead of having them imported), you can do this by using the following steps.

**Steps**

1. Download the latest HDP image to your local machine:

    <pre><small>curl -O https://public-repo-1.hortonworks.com/HDP/cloudbreak/cb-hdp-26-1803301748.img</small></pre>

2. Set the following environment variables for the OpenStack image import:

    <pre><small>export CB_LATEST_IMAGE=cb-hdp-26-1803301748.img
export CB_LATEST_IMAGE_NAME=cb-hdp-26-1803301748.img
export OS_USERNAME=your_os_user_name
export OS_AUTH_URL=your_authentication_url
export OS_TENANT_NAME=your_os_tenant_name</small></pre>

3. Import the new image into your OpenStack:

    <pre><small>glance image-create --name "$CB_LATEST_IMAGE_NAME" --file "$CB_LATEST_IMAGE" --disk-format qcow2 --container-format bare --progress</small></pre>

After performing the import, you should be able to see the Cloudbreak image among your OpenStack images.


##### Import HDF prewarmed image

No prewarmed image is currently available for HDF. 


##### Import HDP/HDF base image

These steps are no longer required, because if these images are not imported manually, Cloudbreak will import them once you attempt to create an HDP or HDF cluster. If you would like to import HDP/HDF base images manually (instead of having them imported), you can do this by using the following steps.

**Steps**

1. Download the latest HDP image to your local machine:

    <pre><small>curl -O https://public-repo-1.hortonworks.com/HDP/cloudbreak/cb-hdp--1803211203.img</small></pre>

2. Set the following environment variables for the OpenStack image import:

    <pre><small>export CB_LATEST_IMAGE=cb-hdp--1803211203.img
export CB_LATEST_IMAGE_NAME=cb-hdp--1803211203.img
export OS_USERNAME=your_os_user_name
export OS_AUTH_URL=your_authentication_url
export OS_TENANT_NAME=your_os_tenant_name</small></pre>

3. Import the new image into your OpenStack:

    <pre><small>glance image-create --name "$CB_LATEST_IMAGE_NAME" --file "$CB_LATEST_IMAGE" --disk-format qcow2 --container-format bare --progress</small></pre>

After performing the import, you should be able to see the Cloudbreak image among your OpenStack images.
--->

### Launch the VM

{!docs/common/about-tp.md!}

<!---Commenting out the regular steps--->
<!---

In your OpenStack, launch and instance providing the following parameters:

* Select a VM flavor which meets the following minimum requirements: 4GB RAM, 10GB disk, 2 cores.
* Select the Cloudbreak deployer image that you imported earlier and launch an instance using that image.
* Select your SSH key pair.
* Select the security group which has the following ports open: 22 (SSH) and 443 (HTTPS).
* Select your preconfigured network.


### SSH to the VM

Now that your VM is ready, access it via SSH:

* Use a private key matching the public key that you added to your OpenStack project.
* The SSH user is called "cloudbreak".
* You can obtain the VM's IP address from the details of your instance.

On Mac OS X, you can SSH to the VM by running the following from the Terminal app: `ssh -i "your-private-key.pem" cloudbreak@instance_IP` where "your-private-key.pem" points to the location of your private key and "instance_IP" is the public IP address of the VM.

On Windows, you can use [PuTTy](http://www.putty.org/).


### Initialize the Profile

After accessing the VM via SSH, you must initialize your Profile.

**Steps**

1. Navigate to the cloudbreak-deployment directory:

    <pre>cd /var/lib/cloudbreak-deployment/</pre>

    This directory contains configuration files and the supporting binaries for Cloudbreak deployer.

2.  Initialize your profile by creating a new file called `Profile` and adding the following content:

    <pre>export UAA_DEFAULT_SECRET=MY-SECRET
export UAA_DEFAULT_USER_PW=MY-PASSWORD
export UAA_DEFAULT_USER_EMAIL=MY-EMAIL
export PUBLIC_IP=VM-PUBLIC-IP</pre>

    For example:

    <pre>export UAA_DEFAULT_SECRET=MySecret123
export UAA_DEFAULT_USER_PW=MySecurePassword123
export UAA_DEFAULT_USER_EMAIL=dbialek@hortonworks.com
export PUBLIC_IP=34.212.141.253</pre>

    > You will need to provide the email and password when logging in to the Cloudbreak web UI and when using the Cloudbreak CLI. The secret will be used by Cloudbreak for authentication.


### Perform optional configurations

> These configurations are optional.


#### Configuring a self-signed certificate

If your OpenStack is secured with a self-signed certificate, you need to import that certificate into Cloudbreak, or else Cloudbreak won't be able to communicate with your OpenStack.

To import the certificate, place the certificate file in the `/certs/trusted/` directory, follow these steps.

**Steps**

1. Navigate to the `certs` directory (automatically generated).
2. Create the `trusted` directory.
3. Copy the certificate to the `trusted` directory.

Cloudbreak will automatically pick up the certificate and import it into its trust store upon start.



### Start Cloudbreak deployer

Launch Cloudbreak deployer using the following steps.

**Steps**

1. Start the Cloudbreak application by using the following command:

    <pre>cbd start</pre>

    This will start the Docker containers and initialize the application. The first time you start the Cloudbreak app, the process will take longer than usual due to the download of all the necessary docker images.

    This will start the Docker containers and initialize the application. The first time you start the Cloudbreak app, this also downloads of all the necessary docker images.

[Comment]: <> (Extra info which may not be needed here: The `cbd start` command includes the `cbd generate` command which applies the following steps: Creates the `docker-compose.yml` file, which describes the configuration of all the Docker containers needed for the Cloudbreak deployment. Creates the `uaa.yml` file, which holds the configuration of the identity server used to authenticate users with Cloudbreak.)

    Once the `cbd start` has finished, it returns the "Uluwatu (Cloudbreak UI) url" which you can later paste in your browser and log in to Cloudbreak web UI.

    To check Cloudbreak deployer version and health, use:

    <pre>cbd doctor</pre>

    If you would like to check Cloudbreak Application logs, use:

    <pre>cbd logs cloudbreak</pre>

    You should see a message like this in the log: `Started CloudbreakApplication in 36.823 seconds.` Cloudbreak takes less than a minute to start. If you try to access the Cloudbreak UI before Cloudbreak started, you will get a "Bad Gateway" error or "Cannot connect to Cloudbreak" error.

-->

### Access Cloudbreak web UI

Log in to the Cloudbreak UI using the following steps.

**Steps**

1. You can log into the Cloudbreak application at `https://IP_Address` where "IP_Address" if the public IP of your OpenStack VM. For example `https://34.212.141.253`.

2. Confirm the security exception to proceed to the Cloudbreak web UI.

    The first time you access Cloudbreak UI, Cloudbreak will automatically generate a self-signed certificate, due to which your browser will warn you about an untrusted connection and will ask you to confirm a security exception. You can safely proceed to the website.

    | Browser | Steps |
|---|---|
| Firefox | Click **Advanced** > Click **Add Exception...** > Click **Confirm Security Exception** |
| Safari | Click **Continue** |
| Chrome | Click **Advanced** > Click **Proceed...** |  

3. The login page is displayed:

    <a href="../images/cb_cb-ui.png" target="_blank" title="click to enlarge"><img src="../images/cb_cb-ui.png" width="650" title="Cloudbreak web UI"></a>

4. Log in to the Cloudbreak web UI using the credential that you configured in your `Profile` file when [starting Cloudbreak deployer](#start-cloudbreak-deployer):

    * The username is the `UAA_DEFAULT_USER_EMAIL`
    * The password is the `UAA_DEFAULT_USER_PW`

5. Upon a successful login, you are redirected to the dashboard:

    <a href="../images/cb_cb-ui1.png" target="_blank" title="click to enlarge"><img src="../images/cb_cb-ui1.png" width="650" title="Cloudbreak web UI"></a>


### Create Cloudbreak credential

Cloudbreak works by connecting your OpenStack account through this credential, and then uses it to create resources on your behalf. Before you can start provisioning cluster using Cloudbreak, you must create a Cloudbreak credential.

**Steps**

1. In the Cloudbreak web UI, select **Credentials** from the navigation pane.

2. Click **Create Credential**.

3. Under **Cloud provider**, select "Google Cloud Platform".

    <a href="../images/cb_cb-os-cred.png" target="_blank" title="click to enlarge"><img src="../images/cb_cb-os-cred.png" width="650" title="Cloudbreak web UI"></a>

3. Select the keystone version.

4. Provide the  following information:

    For Keystone v2:

    | Parameter | Description |
|---|---|
| Name | Enter a name for your credential. |
| Description | (Optional) Enter a description. |
| User | Enter your OpenStack user name. |
| Password | Enter your OpenStack password. |
| Tenant Name | Enter the OpenStack tenant name. |
| Endpoint | Enter the OpenStack endpoint. |
| API Facing | (Optional) Select *public*, *private*, or *internal*. |

    For Keystone v3:

    | Parameter | Description |
|---|---|
| Keystone scope | Select the scope: default, domain, or project. |
| Name | Enter a name for your credential. |
| Description | (Optional) Enter a description. |
| User | Enter your OpenStack user name. |
| Password | Enter your OpenStack password. |
| User Domain | Enter your OpenStack user domain. |
| Endpoint | Enter the OpenStack endpoint. |
| API Facing | (Optional) Select *public*, *private*, or *internal*. |

[comment]: <> (Not sure what these params do: Keystone scope, User Domain)

4. Click **Create**.

5. Your credential should now be displayed in the **Credentials** pane.

    Congratulations! You have successfully launched Cloudbreak and created a Cloudbreak credential. Now you can use Cloudbreak to [create clusters](os-create.md).

<div class="next">
<a href="../os-create/index.html">Next: Create a Cluster</a>
</div>

