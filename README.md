NOTE: This is currently a development version; not all functions will be in a working order

Ansible Satellite6-installer Role
=================================

This role is to install and apply a basic "connected" Red Hat Satellite 6.2+ configuration. 

Requirements
------------
1) An Ansible server. (Version 2.1+ recommended).

2) A Red Hat registered target RHEL 7+ server/instance and an available Satellite 6.2+ subscription/entitlement.

3) The target server/instance must have a raw disk/volume with sufficent storage, as outlined in the RH Satellite documentation.

4) A valid Satellite manifest
- Navigate and login: [Red Hat Network](https://rhn.redhat.com)
- Select "Subscriptions"
- Select "Satellite" under "Subscription Management Applications"
- Select "Register a Satellite"
- Enter a name, the version of Satellite, and click Register
- Select "Attach Subscription" and choose which subscription to attach
- Select "Download Manifest" 
- Move manifest file into [role_name]/files/satellite_manifest.zip 

Variables
---------
This role's tasks may be controled using the following variables in default/main.yml:
Note: All of these values are currently set to False.

* rhn_registration: True or False
  - Add the Satellite 6.2+ Pool ID to the server.
* make_satellite: True or False
  - Configure the raw disk/volume, set up firewalld, install Satellite packages, 
    and set up the Satellite server using an answer file.
* upload_manifest: True or Flase
  - Upload the provided Satellite manifest.
* add_repos: True or Flase
  - Add RHEL 7, Satellite client tools, and other requested repositories.
* add_epel: True or Flase
  - Add the EPEL 7 yum repository.
* make_lifecycle: True or Flase
  - Create Satellite lifecycles: Development, Staging, and Production.
* make_sync_plans: True or Flase
  - Create repository synchronization schedules.
* make_content_views: True or Flase
  - Create Content Views and Activation Keys, and assign Activation Keys to Content Views.

Modify the values under satellite_volumes in default/main.yml to fit your planned storage requiremments.
1) Change the satellite_data_physvol, and satellite_data_physlvm to match your target's satellite data physical volume.
2) Change the "use_firewalld_services" variable to use firewalld services (set to True) or firewalld ports (set to False).
   - Uncomment commented out entires to open additional service ports.  Most dpeloyments won't require anything beyond SSH and RH-Satellite-6 services.
3) Modify defaults/answer-file.yml and change the values to match your system's fqdn, url, and admin password.
   Note: Make sure your system's fqdn resples correctly via the fqdn and via the short name.  Check /etc/hosts for entries that may cause this to fail.
   The foreman installer will test this and quit if the DNS lookup fails.

4) To add additional Red Hat provided repositories, you will need the following:
   - Repository Name
   - Product Name
   - Release version: i.e. 7Server -- if one is assigned to the repository
   - Architechtrue: x86_64, i386, noarch
   Edit the defaults/hammer-vars.yml to add the repositories.

5) To set up defaults/content_views.yml, you will need the following:
   - The repository's product id number: i.e. Red Hat Entreprise Linux's number might be "13."
   - The repository names associated with the product id number.
   - The list of Content Views that you wish to create.

Obtaining information for Steps 4 and 5:
* List an organziation's available products: hammer product list --organization <organization_name>
* List a product's available repositories: hammer repository-set list --organization <organization_name> --product <product name>
* List an organization's enabled products: hammer product list --organization <organziation_name> --enabled true
* List a organizaiton's enabled repositories by product id number: hammer repository list --organziation <organization_name> --product-id <product id number>

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

Known Issues
------------
A test build against an AWS instance had problems with the qpid and pulp packages.  Disabling SELinux and reinstalling all installed qpid and pulp packages resolved the issue.
Indications:
- The qpid daemon would not start, stating it couldn't create /var/lib/qpid/.qpid
- Pulp workers weren't spawning.
- Several Satellite data directories and files had incorrect SELinux tags.

License
-------

BSD

Author Information
------------------

Josh Springer: <springer@redhat.com>, [GitHub](https://github.com/josh-springer)

Contributors
------------------
Glenn Snead <glennsnead@gmail.com>, [GitHub](https://github.com/killroy1971)
