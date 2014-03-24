Role Name
========

ad_hoc_logs_and_diagnostics

Requirements
------------

This requires no installations, just bash and probably any shell would work.<br><br>
In the case of clusters only accessible via jump hosts you will need to add ~/.ssh/config similar to that in [files/ssh_config](https://github.com/darKoram/ad_hoc_logging_and_diagnostics/tree/master/files/ssh_config).  For example, the private ips of vms in an openstack tenant/project with the cluster on a private network is not accessible directly.  We set up an ansible_controller box on the private network (any cloudpipe or pivot vm would do as well).  The ansible_controller gets a public ip that is directly accessible to admins and all vms in the private network can be accessed via jump host entries in .ssh/config.
Jump hosts must have netcat (package name nc) installed <br>
```
    yum install nc
    apt-get install nc
```
Installation
------------

ansible-galaxy install kesten.ad_hoc_logs_and_diagnostics

Use Cases
-----------

There are lots of great log aggregators and visualizers.  However, we often find the need 
to work with 3rd party contractors where systems are under lock-down.  The primary motivation
was for receiving ad hoc requests from a party without access to our hosts to gather logs, execute
commands and run scrips on one several machines.  Often, we would share a tarball of the logs and
command stdouts.  Some trouble-shooting sessions lasted several hours a day for a week.  More details 
in [files/openstack_diagnose_ips.yml](https://github.com/darKoram/ad_hoc_logging_and_diagnostics/tree/master/files/openstack_diagnose_ips.yml).

Role Variables
--------------

    logging_home: "/tmp/ansible/log_gathering"
    mail_server: exchange.mycompany.com

#### load files from load_folder in format


    diagnostic_request: 
         header: 
            issue: "issue_number",
            email_from: me@example.com,
            email: minion1@example.com,boss1@example.com
          
         requests:
            - hostname: host_or_group_1,
              paths:  
                - /etc/nova
                - /var/log/quantum
              commands: 
                - /etc/init.d/rsyslog
                - service quantum-dhcp-agent status
            # in addition, scripts in the role's folder can be
            # executed on the remote hosts and results gathered 
              scripts:
                - script1
                - script2

            - hostname: host_or_group_2,
              paths: { "..." },
              commands: { "..."},
              scripts: { "..."}

Scripts go in [templates](https://github.com/darKoram/ad_hoc_logging_and_diagnostics/tree/master/templates) 
When executed it creates a tarball in logging_home called {issue}_{log_time}.tar.gz <br>
Optionally, attach the tarball to email and send to recipients. <br>


Dependencies
------------

None

Example Playbook
-------------------------

A proper example playbook is found in playbooks/gather_diagnostics.yml

    ansible-playbook -i hosts gather_logs.yml 
                                 -e "log_time=march18" 
                                 -e "requests_file='formatted request in files folder'" 


Where gather_logs.yml contains

    - hosts: servers
      roles:
         - { role: kesten.ad_hoc_logs_and_diagnostics, 
                   log_time: march18, 
                   send_email: yes    }

License
-------

MIT

Author Information
------------------

Kesten Broughton kesten.broughton@gmail.com

Bugs
------------

I expect that the commands will break with any kind of fancy quoting or shell expansion.
Just put it into a script to workaround that.

TODO
------------

Often the log files will be in the gigabytes.  
```
    add checking that the local disk has space for gathering everything
    add the ability to attach large files via google drive for emailing
```

Files are transferred by rsync so they should be compressed, however, they are expanded and then
compressed again to create a tarball on the ansible_controller
```
     add the ability to stream into a tar archive on the ansible_controller
     add the option for different compression schemes and encryption
```