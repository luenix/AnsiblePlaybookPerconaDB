Web Server Stack Ansible Playbook for Wordpress deployments
===========================================================

Author:      Jared Mock ( jmock@fixsociety.tech )
Last Update: 2016-07-25
Attribution: This playbook steals heavily from Zach Adam's Mercury Vagrant Deployment Playbook
             https://github.com/zach-adams/hgv-deploy-full


Stack
------------------------
* NGINX (:8080)
* HHVM (@fallback PHP-FPM)
* APC, memcached, Varnish (:80)
* PerconaDB
* WP CLI (command 'wp' in web root directory)


Pre-Reqs/Notes
--------------
* Recommended minimum of 4GiB RAM and 2 vCPUs, e.g. AWS EC2 t2.medium
* Instructions based on Ubuntu 14.04

0. Install the packages: software-properties-common ansible git
    $ sudo add-apt-repository ppa:ansible/ansible
    $ sudo apt-get update && sudo apt-get upgrade
    $ sudo apt-get install software-properties-common ansible git

1. Edit the configuration files
    Replace ./hosts contents with website hostname.
    Configure ./host_vars/domain.com contents with usernames and passwords.

2. Run the Ansible playbook
    $ ansible-playbook -i hosts -c local playbook.yml

3. Configure the system services
    Secure MySQL
    $ mysql_secure_installation

    How to toggle use of Varnish
        * Disable or enable Varnish services
        $ sysv-rc-conf
            Spacebar toggles the highlighted option.
            Toggle the 3rd-6th options for the 3 Varnish services.

        * Configure NGINX
        $ sudo nano +2 /etc/nginx/sites-available/( hostname )
            Varnish now enabled:  change listening port from '80' to '8080'.
            Varnish now disabled: change listening port from '8080' to '80'.
        
        * Varnish now enabled:
        $ sudo service varnish start && sudo service varnishncsa start && sudo service nginx restart

        * Varnish now disabled:
        $ sudo service varnish stop && sudo service varnishncsa stop && sudo service nginx restart

    How to switch from HHVM to PHP-FPM
        * Configure NGINX
        $ sudo nano +35 /etc/nginx/sites-available/( hostname )
            Change line 35: "fastcgi_pass hhvm;" to "fastcgi_pass php;"
        $ sudo service nginx restart
