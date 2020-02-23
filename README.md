Ansible LEMP + Redis Stack
==========================

Ansible playbook that sets up a LEMP (Linux NGINX MariaDB PHP) and Redis stack on an Ubuntu 18.04 system.  In addition, it partially hardens the install using decent default options.

Overview of what this playbook does:
* Security
    * Modifies SSH port to a non-standard port
    * Sets up automated security patching
    * Configures fail2ban
    * Enables the host firewall and restricts all ports except SSH, HTTP, and HTTPS
* Sets up CertBot
    * Creates a TLS certificate for the server's domain
    * Gets the certificate signed by LetsEncrypt
* Installs PHP and PHP-FPM
    * Sets reasonable php and php-fpm settings
    * Enables opcache
* Sets up NGINX
    * Generates the main NGINX.conf
    * Forces HTTP to HTTPS redirection
    * Configures PHP-FPM proxying
    * Restricts access to sensitive locations
    * Enables caching of static content
* Sets up up MariaDB
    * Removes test schema
    * Changes root password

Prerequisites
---

The target host must be running an Ubuntu 18.04 and you must have a user account with previliged access (sudo) on that system.  Other Ubuntu releases might be supported but have not been tested.

Please ensure that the following packages are installed on the host that will be executing the ansible playbooks.

Required to run the ansible playbook directly without using Vagrant:

    Ansible 2.8+
    Python 3
    PyMySQL

Required to provision a development VM locally using Vagrant:

   Vagrant 2.2+
   Virtualbox 5+


Getting Started
---

Ensure that the prerequisites section is satisfied before proceeding.

Clone this repository.

    $ git clone https://github.com/IyadKandalaft/wp-lemp wp-lemp
    $ cd wp-lemp

Create an inventory file with the list of target systems' hostnames.

    $ echo "webserver.mydomain.com" > inventory

_Note that you will need to set the ansible ssh user and key/password for the hosts in the inventory file or configure user+key-based access via .ssh/config.  See ansible documentation for more information._

Set the minimum required variables for each host by creating a @host_vars/webserver.mydomain.com@ file to set them

    $vim host_vars/webserver.mydomain.com
        domain_name: myhost.com  # Domain name that resolves to the server and will serve pages
        mysql_super_user_password: ChangeM3!  # MySQL password for the root user
        webmaster_user_password: ChangeM3! # Password for the webmaster user that is created
        ssh_port: 22 # Port that the SSH server will listen on
        lets_encrypt_email: "my@email.com" # E-mail address used by CertBot

_You can optionally define these in the inventory file but it's not recommended._
_Optionally, override other default configuration found in group_vars/all.yml on a per host basis within the @host_vars/webserver.mydomain.com@ file._

Install the required ansible roles using ansible-galaxy

    $ ansible-galaxy install -r requirements.yml

Run ansible on the site.yml playbook

    $ ansible-playbook -i inventory site.yml


Configurable Options
---

Much of the functionality in this playbook is obtained from advanced ansible roles that provide a significant amount of customization using role variables.  This playbook defines working defaults in the @group_vars/all@ file but can be modified and/or extended.

To view the available options, consult the following ansible roles' documentation:

* [geerlingguy.security](https://github.com/geerlingguy/ansible-role-security)
* [geerlingguy.firewall](https://github.com/geerlingguy/ansible-role-firewall)
* [geerlingguy.mysql](https://github.com/geerlingguy/ansible-role-mysql)
* [geerlingguy.php](https://github.com/geerlingguy/ansible-role-php)
* [geerlingguy.redis](https://github.com/geerlingguy/ansible-role-redis)
* [nginxinc.nginx](https://github.com/nginxinc/ansible-role-nginx)

Development
---
Ensure that the prerequisites section is satisfied including the portion related to the Vagrant requirements.

Clone this repository

    git clone https://github.com/IyadKandalaft/wp-lemp wp-lemp
    cd wp-lemp

Checkout the development branch

    git checkout dev

Optionally create a feature branch off the development branch

    git checkout -b myfeature

Make the required modifications to the playbooks

Use Vagrant to spin a testing VM

    vagrant up

To validate that the provisioning complete successfully, review the ansible output.  In addition, SSH into the VM

    vagrant ssh
    
... wait for Vagrant to ssh into the VM, then verify your changes and then exit back to the host

    systemctl status mariadb
    exit

If provisioning fails, you can potentially force vagrant to reprovision using after you fix the ansible playbooks

    vagrant up --provision

To destroy the VM and start over, run

    vagrant destroy -f


Contibution
---

Create a pull request against this repository and explain the changes that you are trying to merge.
Don't forget to add your name to the acknoledgements list below.


Acknowledgements
---

* geerlingguy for suppling and maintaining many great ansible roles.
* nginxinc for supplying and maintaining the nginx ansible role.
