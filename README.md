# Licensing an Air-Gapped VM-Series NGFW

Normally, when a VM-Series Palo Alto Networks firewall has direct internet access 
and an activated auth-code, activating the VM's licenses after initial 
configurations is as easy as clicking _Device > Licenses > **Activate feature using
authorization code**._

When a VM-Series is not connected to the internet (or is said to be air-gapped), 
additional steps must be completed. This Quickplay solution was created to simplify
these steps:

    * Grab the CPUID and UUID from the NGFW
    * Activate the NGFW with the Licensing API 
    * Push the PA-VM license key and wait for a reboot
    * Push all remaining received licensing keys to the NGFW

### Running the Playbook

#### Install/Upgrade the PAN-OS Enhanced Ansible Collection

This playbook requires the `v1.2.0+ mrichardson03.panos` Ansible collection. Install 
with this command:

`ansible-galaxy collection install --force mrichardson03.panos`

#### Edit your inventory file

In the inventory file, you can set the NGFW's IP address (_ansible_host_), 
username (_ansible_user_) and password (_ansible_password_).

`cp inventory.sample inventory`

`vi inventory`

#### Edit your vars file

In the `vars/main.yml` file, you can set:
   1. the pre-activated auth-code 
   2. your _Customer Support Portal > Assets > API Key Management > **Licensing API**,_
   3. the `first_time` registration (yes/no)

If you did not save the license keys or had a network connection 
trouble during initial license activation, you may run into the error message:

> "Cannot apply a provisioning license feature to an already provisioned device"

In this case, you will need to set the `first_time` as _no_. 

#### Run the Playbook, passing along extra vars if not previously set

If you did not edit the variables and inventory files, you can use the `-e` command 
line parameter to set extra variables. Run the following command to license your
airgapped firewall:

`ansible-playbook -i inventory license-airgap-firewall.yml -e 'first_time=yes'`

> **Please Note**: 
> 
> During the first time registration of a VM, the *Push PA-VM license key to Firewall*
> Ansible task **WILL FAIL** with the error `Remote end closed connection without response`
> since the VM must reboot. This failure is an expected behavior, and the playbook will 
> wait for the firewall to boot back up and will push remaining licenses to the VM. 
