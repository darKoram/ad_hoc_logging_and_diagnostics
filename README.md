Role Name
========

ad_hoc_logs_and_diagnostics

Requirements
------------

This requires no installations, just bash and probably any shell would work.

Installation
------------

ansible-galaxy install kesten.ad_hoc_logs_and_diagnostics

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
                - /etc/init.d/rsyslog,
                - service quantum-dhcp-agent status
            # in addition, scripts in the templates can be executed 
              scripts:
                - script1
                - script2

            - hostname: host_or_group_2,
              paths: { "..." },
              commands: { "..."},
              scripts: { "..."}

Creates a tarball in logging_home called {issue}_{log_time}.tar.gz <br>
Optionally, attach the tarball to email and send to recipients. <br>


Dependencies
------------

None

Example Playbook
-------------------------

A proper example playbook is found in playbooks/gather_diagnostics.yml

    ansible-playbook -i hosts gather_logs.yml -e "log_time='current time approximate' requests_file='formatted request in files folder'" 


Where gather_logs.yml contains

    - hosts: servers
      roles:
         - { role: kesten.ad_hoc_logs_and_diagnostics, 
                   log_time: march18, 
                   send_email: yes,  
            }

License
-------

MIT

Author Information
------------------

Kesten Broughton kesten.broughton@gmail.com
