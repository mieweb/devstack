# MIE Devstack Local Environment Install
[[Instructions using Vagrant + Virtualbox on Mac OS X can be found here]](https://docs.google.com/a/mieweb.com/document/d/1Z7atkdpGPlJaWb0e0L89Jhgzf1lkbLTfISToKpbL0FQ/edit?usp=sharing)

**NOTE:** You must update the **local.conf** file and configure it for your local environment before attempting to run devstack.

## Under - Common Configurations in **local.conf**:
* You will need to update the IP addresses accordingly for the following variables:

1. This IP should be your local machine address and will serve as a router for a multi-node setup:
..* PUBLIC_NETWORK_GATEWAY="192.168.1.6"

2. These IPs should be on the same subnet as the local machine IP above, but can be any IP address on that subnet you'd like:
..* HOST_IP=192.168.1.15
..* SERVICE_HOST=192.168.1.15
..* MYSQL_HOST=192.168.1.15
..* RABBIT_HOST=192.168.1.15
..* GLANCE_HOSTPORT=192.168.1.15:9292

3. This floating range is your local machine subnet and will be created as the Openstack PUBLIC network:
..* FLOATING_RANGE="192.168.1.0/24"

4. This fixed range is what Openstack will create as a PRIVATE subnet to host instances and should NOT be on the same block as your local / public network above:
..* FIXED_RANGE="10.0.0.0/24"

5. This IP range will be used to assign publicly accessible floating IPs that can be mapped to private addresses, and should therefore match your PUBLIC address block defined above.
..* Q_FLOATING_ALLOCATION_POOL=start=192.168.1.100,end=192.168.1.200


#############################################################
# Everything below is from the default devstack README.md file
#############################################################
DevStack is a set of scripts and utilities to quickly deploy an OpenStack cloud.

# Goals

* To quickly build dev OpenStack environments in a clean Ubuntu or Fedora
  environment
* To describe working configurations of OpenStack (which code branches
  work together?  what do config files look like for those branches?)
* To make it easier for developers to dive into OpenStack so that they can
  productively contribute without having to understand every part of the
  system at once
* To make it easy to prototype cross-project features
* To provide an environment for the OpenStack CI testing on every commit
  to the projects

Read more at http://docs.openstack.org/developer/devstack

IMPORTANT: Be sure to carefully read `stack.sh` and any other scripts you
execute before you run them, as they install software and will alter your
networking configuration.  We strongly recommend that you run `stack.sh`
in a clean and disposable vm when you are first getting started.

# Versions

The DevStack master branch generally points to trunk versions of OpenStack
components.  For older, stable versions, look for branches named
stable/[release] in the DevStack repo.  For example, you can do the
following to create a juno OpenStack cloud:

    git checkout stable/juno
    ./stack.sh

You can also pick specific OpenStack project releases by setting the appropriate
`*_BRANCH` variables in the ``localrc`` section of `local.conf` (look in
`stackrc` for the default set).  Usually just before a release there will be
milestone-proposed branches that need to be tested::

    GLANCE_REPO=git://git.openstack.org/openstack/glance.git
    GLANCE_BRANCH=milestone-proposed

# Start A Dev Cloud

Installing in a dedicated disposable VM is safer than installing on your
dev machine!  Plus you can pick one of the supported Linux distros for
your VM.  To start a dev cloud run the following NOT AS ROOT (see
**DevStack Execution Environment** below for more on user accounts):

    ./stack.sh

When the script finishes executing, you should be able to access OpenStack
endpoints, like so:

* Horizon: http://myhost/
* Keystone: http://myhost:5000/v2.0/

We also provide an environment file that you can use to interact with your
cloud via CLI:

    # source openrc file to load your environment with OpenStack CLI creds
    . openrc
    # list instances
    nova list

# DevStack Execution Environment

DevStack runs rampant over the system it runs on, installing things and
uninstalling other things.  Running this on a system you care about is a recipe
for disappointment, or worse.  Alas, we're all in the virtualization business
here, so run it in a VM.  And take advantage of the snapshot capabilities
of your hypervisor of choice to reduce testing cycle times.  You might even save
enough time to write one more feature before the next feature freeze...

``stack.sh`` needs to have root access for a lot of tasks, but uses
``sudo`` for all of those tasks.  However, it needs to be not-root for
most of its work and for all of the OpenStack services.  ``stack.sh``
specifically does not run if started as root.

DevStack will not automatically create the user, but provides a helper
script in ``tools/create-stack-user.sh``.  Run that (as root!) or just
check it out to see what DevStack's expectations are for the account
it runs under.  Many people simply use their usual login (the default
'ubuntu' login on a UEC image for example).

# Customizing

DevStack can be extensively configured via the configuration file
`local.conf`.  It is likely that you will need to provide and modify
this file if you want anything other than the most basic setup.  Start
by reading the [configuration guide](doc/source/configuration.rst) for
details of the configuration file and the many available options.
