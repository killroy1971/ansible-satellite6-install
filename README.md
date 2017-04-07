NOTE: This is currently a development version; not all functions will be in a working order

Ansible Satellite6-installer Role
=================================

This role is to install and provide basic configuration of Red Hat Satellite 6.2+ on a RHEL 7+ machine. 

Requirements
------------
1) Ansible must be installed. (Version 2.1+ recommended).

2) RHEL 7+ and RH Satellite 6.2+ with valid subscriptions.
You must first provide a Satellite Manifest file
for the playbook to work properly. Follow the below steps.

3) A valid Satellite manifest
- Navigate and login to the [Red Hat Network](https://rhn.redhat.com)
- Select "Subscriptions"
- Select "Satellite" under "Subscription Management Applications"
- Select "Register a Satellite"
- Enter a name, the version of Satellite, and click Register
- Select "Attach Subscription" and choose which subscription to attach
- Select "Download Manifest" 
- Move manifest file into [role_name]/files/satellite_manifest.zip 

4) A target Satellite server or instance 
You should have built a target server/instance running Red Hat Enterprise Linux 7, and added a volume with sufficent storage, as outlined in the RH Satellite documentation.
This server/instance must have a valid RHEL 7 and Satellite 6 entitlement assigned to it.


Variables
---------
This role's tasks may be controled using the following variables in default/main.yml:
* rhn_registration: True or False
* make_satellite: True or False
* upload_manifest: True or Flase
* add_repos: True or Flase
* add_epel: True or Flase
* make_lifecycle: True or Flase
* make_sync_plans: True or Flase
* make_content_views: True or Flase
Note: All of these values are currently set to False.

Modify the values under satellite_volumes in default/main.yml to fit your planned storage requiremments.
Change the satellite_data_physvol, and satellite_data_physlvm to match your target's satellite data physical volume.
Use the "use_firewalld_services" variable to use firewalld services (set to True) or firewalld ports (set to False).
- Uncomment commented out entires to open additional service ports.  Most dpeloyments won't require anything beyond SSH and RH-Satellite-6 services.

Modify defaults/answer-file.yml and change the values to match your system's fqdn, url, and admin password.
Make sure your system's fqdn resples correctly via the fqdn and via the short name.  Check /etc/hosts for entries that may cause this to fail.
The foreman installer will test this.

To add additional Red Hat provided repositories, you will need the following:
- Repository Name
- Product Name
- Release version: i.e. 7Server -- if one is assigned to the repository
- Architechtrue: x86_64, i386, noarch
Edit the defaults/hammer-vars.yml to add the repositories.

To set up defaults/content_views.yml, you will need the following:
- The repository's product id number: i.e. Red Hat Entreprise Linux's number might be "13."
- The repository names associated with the product id number.
- The list of Content Views that you wish to create.

* This command wil list an organziation's available products: hammer product list --organization <organization_name>
* This command will list a product's available repositories: hammer repository-set list --organization <organization_name> --product <product name>
* This command will list an organization's enabled products: hammer product list --organization <organziation_name> --enabled true
* This command will list a organizaiton's enabled repositories by product id number: hammer repository list --organziation <organization_name> --product-id <product id number>

Dependencies
------------
None

Running the playbook
--------------------
If running main.yml directly from the role directory, make update hosts.target with the address corresponding to your target server.

If intended for use with other roles, include the role in a playbook located at your top level Ansible directory.

Example: [ansible_directory]/playbook.yml
- name: This is an example playbook
  hosts: desired_hostname
  roles:
    - include: satellite6-install

Optional
--------
If you wish to use Ansible to create and populate Content Views and access keys, use the hammer command to list product ids (see above) and update defaults/content_views.yml
Set the defaults/main.yml file so that only the content_views task runs by setting the other values to Flase (see Variabled above).
Re-run the role playbook.

License
-------

BSD

Author Information
------------------

Josh Springer: <springer@redhat.com>, [GitHub](https://github.com/josh-springer)

Contributors
------------------
Glenn Snead <glennsnead@gmail.com>, [GitHub](https://github.com/killroy1971)
