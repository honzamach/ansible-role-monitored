.. _section-role-monitored:

Role **monitored**
================================================================================

* `Ansible Galaxy page <https://galaxy.ansible.com/honzamach/monitored>`__
* `GitHub repository <https://github.com/honzamach/ansible-role-monitored>`__
* `Travis CI page <https://travis-ci.org/honzamach/ansible-role-monitored>`__

Main purpose of this role is installation and configuration of remote system monitoring
via `Nagios <https://www.nagios.org/>`__ platform.

This role installs following additional Nagios plugins:

* `check_file_count <https://exchange.nagios.org/directory/Plugins/System-Metrics/File-System/check_file_count/details>`__
* `check_ro_mounts <https://exchange.nagios.org/directory/Plugins/Operating-Systems/Linux/check_ro_mounts/details>`__
* `check_ssl_cert <https://exchange.nagios.org/directory/Plugins/Network-Protocols/HTTP/check_ssl_cert/details>`__

**Table of Contents:**

* :ref:`section-role-monitored-installation`
* :ref:`section-role-monitored-dependencies`
* :ref:`section-role-monitored-usage`
* :ref:`section-role-monitored-variables`
* :ref:`section-role-monitored-files`
* :ref:`section-role-monitored-author`

This role is part of the `MSMS <https://github.com/honzamach/msms>`__ package.
Some common features are documented in its :ref:`manual <section-manual>`.


.. _section-role-monitored-installation:

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


.. _section-role-monitored-dependencies:

Dependencies
--------------------------------------------------------------------------------

This role is not dependent on any other role.

Following roles have dependency on this role:

* :ref:`firewalled <section-role-firewalled>` *[SOFT]*
* :ref:`logserver <section-role-logserver>`
* :ref:`mentat <section-role-mentat>` *[SOFT]*
* :ref:`mentat_dev <section-role-mentat-dev>` *[SOFT]*
* :ref:`postgresql <section-role-postgresql>` *[SOFT]*
* :ref:`util_inspector <section-role-util-inspector>` *[SOFT]*
* :ref:`warden_client <section-role-warden-client>`


.. _section-role-monitored-usage:

Usage
--------------------------------------------------------------------------------

Example content of inventory file ``inventory``::

    [servers_monitored]
    your-server

Example content of role playbook file ``role_playbook.yml``::

    - hosts: servers_monitored
      remote_user: root
      roles:
        - role: honzamach.monitored
      tags:
        - role-monitored

Example usage::

    # Run everything:
    ansible-playbook --ask-vault-pass --inventory inventory role_playbook.yml

It is recommended to follow these configuration principles:

* Create/edit file ``inventory/group_vars/all/vars.yml`` and within define some sensible
  defaults for all your managed servers::

        # Mandatory for soft dependency mechanism.
        hm_monitored__plugins_dir: /usr/lib/nagios/plugins

        # You will probably use same NTP reference server.
        hm_monitored__ntp_server: 195.113.144.201

        # Your NRPE service will always run on the same port.
        hm_monitored__service_port: 5666

        # You will probably have same pool of monitoring servers for your whole infrastructure.
        hm_monitored__allowed_hosts:
          - 192.168.1.1
          - ::1

* Use files ``inventory/host_vars/[your-server]/vars.yml`` to customize settings
  for particular servers. Please see section :ref:`section-role-monitored-variables`
  for all available options.


.. _section-role-monitored-variables:

Configuration variables
--------------------------------------------------------------------------------


Internal role variables
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. envvar:: hm_monitored__install_packages

    List of packages defined separately for each linux distribution and package manager,
    that MUST be present on target system. Any package on this list will be installed on
    target host. This role currently recognizes only ``apt`` for ``debian``.

    * *Datatype:* ``dict``
    * *Default:* (please see YAML file ``defaults/main.yml``)
    * *Example:*

    .. code-block:: yaml

        hm_logged__install_packages:
          debian:
            apt:
              - syslog-ng
              - ...

.. envvar:: hm_monitored__plugins_dir

    Location of Nagios plugin directory.

    * *Datatype:* ``string``
    * *Default:* ``/usr/lib/nagios/plugins``

.. envvar:: hm_monitored__service_port

    Port number for NRPE server listener.

    * *Datatype:* ``integer``
    * *Default:* ``5666``

.. envvar:: hm_monitored__allowed_hosts

    List of allowed hosts for Nagios monitoring connections.

    * *Datatype:* ``list of strings``
    * *Default:* ``empty list``

.. envvar:: hm_monitored__local_commands

    List of additional local Nagios commands.

    * *Datatype:* ``list of dictionaries``
    * *Default:* ``[]`` (empty list)
    * *Example:*

    .. code-block:: yaml

        # Check that the Dionaea honeypot process is running:
        hm_monitored__local_commands:
          - name: "check_dionaea"
            command: "check_procs -c 1:2 -C dionaea"
            clean: "sed 's/:/ -/' | cut -f 1 -d \\|;"

Following are settings for particular checks:

.. envvar:: hm_monitored__ntp_server  195.113.144.201

    Hostname or IP address of reference NRP time server for NTP checks.

    * *Datatype:* ``string``
    * *Default:* ``195.113.144.201``

.. envvar:: hm_monitored__settings_check_users

    Monitoring configuration setting for **check_users** command.

    * *Datatype:* ``dictionary``
    * *Default:* ``{ "w": 10, "c": 15 }``

.. envvar:: hm_monitored__settings_check_load

    Monitoring configuration setting for **check_load** command.

    * *Datatype:* ``dictionary``
    * *Default:* ``{ "w": "45,40,20", "c": 50,50,40 }``

.. envvar:: hm_monitored__settings_check_disk

    Monitoring configuration setting for **check_disk** command.

    * *Datatype:* ``dictionary``
    * *Default:* ``{ "w": "20%", "c": "10%" }``

.. envvar:: hm_monitored__settings_check_zombies

    Monitoring configuration setting for **check_zombies** command.

    * *Datatype:* ``dictionary``
    * *Default:* ``{ "w": 5, "c": 10 }``

.. envvar:: hm_monitored__settings_check_procs

    Monitoring configuration setting for **check_procs** command.

    * *Datatype:* ``dictionary``
    * *Default:* ``{ "w": 500, "c": 1000 }``

.. envvar:: hm_monitored__settings_check_ntp

    Monitoring configuration setting for **check_ntp** command.

    * *Datatype:* ``dictionary``
    * *Default:* ``{ "w": 0.5, "c": 1 }``

.. envvar:: hm_monitored__settings_check_ssh

    Settings for check_ssh check

    * *Datatype:* ``dictionary``
    * *Default:* ``{ "p": 22 }``


Built-in Ansible variables
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:envvar:`group_names`

    List of group names current host is member of. This variable is used to resolve
    :ref:`soft role dependencies <section-overview-role-soft-dependencies>`.

:envvar:`ansible_lsb['codename']`

    Linux distribution codename. It is used for :ref:`template customizations <section-overview-role-customize-templates>`.


.. _section-role-monitored-files:

Managed files
--------------------------------------------------------------------------------

.. note::

    This role supports the :ref:`template customization <section-overview-role-customize-templates>` feature.

This role manages content of following files on target system:

* ``/etc/nagios/nrpe.cfg`` *[TEMPLATE]*
* ``/etc/nagios/nrpe.d/local.cfg`` *[TEMPLATE]*
* ``/opt/system-status/system-status`` *[TEMPLATE]*
* ``/opt/system-status/system-status.d/10-local`` *[TEMPLATE]*


.. _section-role-monitored-author:

Author and license
--------------------------------------------------------------------------------

| *Copyright:* (C) since 2019 Honza Mach <honza.mach.ml@gmail.com>
| *Author:* Honza Mach <honza.mach.ml@gmail.com>
| Use of this role is governed by the MIT license, see LICENSE file.
|
