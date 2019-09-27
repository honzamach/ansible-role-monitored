.. _section-role-monitored:

Role **monitored**
================================================================================

Ansible role for configuring Nagios NRPE monitoring on all target systems.

* `Ansible Galaxy page <https://galaxy.ansible.com/honzamach/monitored>`__
* `GitHub repository <https://github.com/honzamach/ansible-role-monitored>`__
* `Travis CI page <https://travis-ci.org/honzamach/ansible-role-monitored>`__


Description
--------------------------------------------------------------------------------


Main purpose of this role is installation and configuration of remote system monitoring
via `Nagios <https://www.nagios.org/>`__ platform.

.. note::

    This role supports the :ref:`template customization <section-overview-customize-templates>` feature.


Requirements
--------------------------------------------------------------------------------

This role does not have any special requirements.


Dependencies
--------------------------------------------------------------------------------

This role is not dependent on any other role.

Following roles have dependency on this role:

* :ref:`logserver <section-role-logserver>`
* :ref:`mentat <section-role-mentat>`
* :ref:`warden_client <section-role-warden-client>`


Managed files
--------------------------------------------------------------------------------

This role directly manages content of following files on target system:

* ``/etc/nagios/nrpe.cfg``
* ``/etc/nagios/nrpe.d/local.cfg``
* ``/opt/system-status/system-status``
* ``/opt/system-status/system-status.d/10-local``


Additional Nagios plugins
--------------------------------------------------------------------------------

This role installs following additional Nagios plugins:

* `check_file_count <https://exchange.nagios.org/directory/Plugins/System-Metrics/File-System/check_file_count/details>`__
* `check_ro_mounts <https://exchange.nagios.org/directory/Plugins/Operating-Systems/Linux/check_ro_mounts/details>`__
* `check_ssl_cert <https://exchange.nagios.org/directory/Plugins/Network-Protocols/HTTP/check_ssl_cert/details>`__


Role variables
--------------------------------------------------------------------------------

There are following internal role variables defined in ``defaults/main.yml`` file,
that can be overriden and adjusted as needed:

.. envvar:: hm_monitored__package_list:

    List of role-related packages, that will be installed on target system.

    * *Datatype:* ``string``
    * *Default:* (please see YAML file ``defaults/main.yml``)

.. envvar:: hm_monitored__plugins_dir

    Location of Nagios plugin directory.

    * *Datatype:* ``string``
    * *Default value:* ``/usr/lib/nagios/plugins``

.. envvar:: hm_monitored__allowed_hosts

    List of allowed hosts for Nagios monitoring connections.

    * *Datatype:* ``list of strings``
    * *Default value:* ``empty list``

.. envvar:: hm_monitored__local_commands

    List of additional local Nagios commands.

    * *Datatype:* ``list of dictionaries``
    * *Default value:* ``[]`` (empty list)
    * *Example:*

    .. code-block:: yaml

        # Check that the Dionaea honeypot process is running:
        hm_monitored__local_commands:
          - name: "check_dionaea"
            command: "check_procs -c 1:2 -C dionaea"
            clean: "sed 's/:/ -/' | cut -f 1 -d \\|;"

.. envvar:: hm_monitored__settings_check_users

    Monitoring configuration setting for **check_users** command.

    * *Datatype:* ``dictionary``
    * *Default value:* ``{ "w": 10, "c": 15 }``

.. envvar:: hm_monitored__settings_check_load

    Monitoring configuration setting for **check_load** command.

    * *Datatype:* ``dictionary``
    * *Default value:* ``{ "w": "45,40,20", "c": 50,50,40 }``

.. envvar:: hm_monitored__settings_check_disk

    Monitoring configuration setting for **check_disk** command.

    * *Datatype:* ``dictionary``
    * *Default value:* ``{ "w": "20%", "c": "10%" }``

.. envvar:: hm_monitored__settings_check_zombies

    Monitoring configuration setting for **check_zombies** command.

    * *Datatype:* ``dictionary``
    * *Default value:* ``{ "w": 5, "c": 10 }``

.. envvar:: hm_monitored__settings_check_procs

    Monitoring configuration setting for **check_procs** command.

    * *Datatype:* ``dictionary``
    * *Default value:* ``{ "w": 500, "c": 1000 }``

.. envvar:: hm_monitored__settings_check_ntp

    Monitoring configuration setting for **check_ntp** command.

    * *Datatype:* ``dictionary``
    * *Default value:* ``{ "w": 0.5, "c": 1 }``

.. envvar:: hm_monitored__settings_check_ssh

    Settings for check_ssh check

    * *Datatype:* ``dictionary``
    * *Default value:* ``{ "p": 22 }``

Additionally this role makes use of following built-in Ansible variables:

.. envvar:: ansible_lsb['codename']

    Debian distribution codename is used for :ref:`template customization <section-overview-customize-templates>`__
    feature.


Usage and customization
--------------------------------------------------------------------------------

This role is (attempted to be) written according to the `Ansible best practices <https://docs.ansible.com/ansible/latest/user_guide/playbooks_best_practices.html>`__. The default implementation should fit most users,
however you may customize it by tweaking default variables and providing custom
templates.


Variable customizations
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Most of the usefull variables are defined in ``defaults/main.yml`` file, so they
can be easily overridden almost from `anywhere <https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable>`__.


Template customizations
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This roles uses *with_first_found* mechanism for all of its templates. If you do
not like anything about built-in template files you may provide your own custom
templates. For now please see the role tasks for list of all checked paths for
each of the template files.


Installation
--------------------------------------------------------------------------------

To install the role `honzamach.monitored <https://galaxy.ansible.com/honzamach/monitored>`__
from `Ansible Galaxy <https://galaxy.ansible.com/>`__ please use variation of
following command::

    ansible-galaxy install honzamach.monitored

To install the role directly from `GitHub <https://github.com>`__ by cloning the
`ansible-role-monitored <https://github.com/honzamach/ansible-role-monitored>`__
repository please use variation of following command::

    git clone https://github.com/honzamach/ansible-role-monitored.git honzamach.monitored

Currently the advantage of using direct Git cloning is the ability to easily update
the role when new version comes out.


Example Playbook
--------------------------------------------------------------------------------

Example content of inventory file ``inventory``::

    [server-central-logserver]
    remote

    [servers_monitored]
    localhost

Example content of role playbook file ``playbook.yml``::

    - hosts: servers_monitored
      remote_user: root
      roles:
        - role: honzamach.monitored
      tags:
        - role-monitored

Example usage::

    ansible-playbook -i inventory playbook.yml


License
--------------------------------------------------------------------------------

MIT


Author Information
--------------------------------------------------------------------------------

Jan Mach <honza.mach.ml@gmail.com>
