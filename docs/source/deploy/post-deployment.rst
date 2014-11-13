Post Deployment
===============

Tips and tricks for post-deployment actions and configuration.

Glance
------

Import Cloud images
~~~~~~~~~~~~~~~~~~~

Currently Cloud images are not available online yet. We will provide both `QCOW2` and `RAW` format images. But in the meantime this is how we add a new image::

    glance image-create --name <name> --disk-format raw --container-format bare --file <path_to_img> --is-public True --is-protected True --progress


Mirosoft Windows images
~~~~~~~~~~~~~~~~~~~~~~~

Microsoft Cloud images need special properties to run properly. In order to improve the user experience we use the QLX drivers::

    glance image-create --name Microsoft_Windows_Server_2012_R2 --disk-format raw --container-format bare --file <path_to_img> --is-public True --is-protected True --property hw_video_model=qxl --property hw_video_ram=64 --progress

Properties can also be edited like this::

    glance image-update <image-id> --property hw_video_model=qxl --property hw_video_ram=6

The Nova flavor must be edited accordingly to reflect the properties::

    nova flavor-key m1.microsoft.windows set hw_video:ram_max_mb=128


Old Cloud images
~~~~~~~~~~~~~~~~

For CentOS 5 images, the virtio support is pretty poor and tend to make the VM randomly not bootable (2/5 times).
You will likely encounter the following message from the Kernel during the boot sequence.::

    Kernel panic - not syncing: IO-APIC + timer doesn't work!

In order to bypass this, we use `IDE` disks and `rtl8139` network card instead of virtio models.::

    glance image-create --name GNU_Linux_CentOS_5.1 --disk-format raw --container-format bare --file <path_to_img> --is-public True --is-protected True --progress --property hw_disk_bus=ide --property hw_vif_model=rtl8139


Nova
----

Delete flavors and re-create tiny flavors
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In order to have bootable tiny flavor we need to increase the size of the `m1.tiny` flavor::

    nova flavor-delete 1
    nova flavor-create m1.tiny 1 512 6 1


Boot Microsoft Windows instances to specific hypervisors
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Due to some licensing constraint, Microsoft Windows virtual machines must be booted on dedicated hypervisors.
Basically you only license a few amount of hypervisor.

.. warning::
    you will not be enable to boot the image on other flavors

.. note::
    however the other way around is not true, you will be able to boot other images with this flavor

First you must add a new filter `AggregateInstanceExtraSpecsFilter` to the scheduler filters::

    scheduler_default_filters=RetryFilter,AvailabilityZoneFilter,RamFilter,ComputeFilter,ImagePropertiesFilter,ServerGroupAntiAffinityFilter,ServerGroupAffinityFilter,AggregateInstanceExtraSpecsFilter

Then create the aggregate that will contain the Windows instances::

    nova aggregate-create microsoft-windows

.. note::
    you can use `nova host-list` to retrieve hypervisor names

Add host to your aggregate::

    nova aggregate-add-host <aggregate> <server>

Create a new metadata for this aggregate::

    nova aggregate-set-metadata <microsoft-windows aggregate ID> windowshypervisors=true

.. warning::
    be careful if you modify the name of an aggregate all the metadata will be deleted (behavior seen on Icehouse)

Create a new flavor for the Windows instances::

    nova flavor-create m1.microsoft.windows 6 4096 40 2

Assign to this flavor a special property::

    nova flavor-key m1.microsoft.windows set windowshypervisors=true


Neutron
-------

Initiale br-pub: Public Network
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Create a public network on br-pub, the default provider network::

    neutron net-create public --shared --router:external=True
    neutron subnet-create public --name ext-subnet --allocation-pool start=$ALLOCATION_PUBLIC_POOL_START,end=ALLOCATION_PUBLIC_POOL_END --disable-dhcp --gateway $ALLOCATION_PUBLIC_POOL_GW 193.191.68


Create a new provider network (on VLAN 100)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Create a new provider on VLAN 100::

    neutron net-create public --provider:network_type vlan --provider:physical_network public --provider:segmentation_id 100 --shared --router:external=True


Create some metering labels for traffic passing through routers external interface
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The following will meter all traffic going through routers' external interfaces::

    neutron meter-label-create public-in
    neutron meter-label-rule-create public-in 0.0.0.0/0 --direction ingress

    neutron meter-label-create public-out
    neutron meter-label-rule-create public-out 0.0.0.0/0 --direction egress

Rules can be more specific and include or exclude some IP ranges. See http://docs.openstack.org/admin-guide-cloud/content/metering_operations.html for more details.


Cinder
------

Create a QoS for a volume type
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Originally both QEMU and KVM support rate limitation.
This is obviously implemented through libvirt and available as an extra xml flag within the `<disk>` section called iotune.

QoS options are:

- `total_bytes_sec`: the total allowed bandwidth for the guest per second
- `read_bytes_sec`: sequential read limitation
- `write_bytes_sec`: sequential write limitation
- `total_iops_sec`: the total allowed IOPS for the guest per second
- `read_iops_sec`: random read limitation
- `write_iops_sec`: random write limitation

Set QoS options::

    cinder qos-create high-iops consumer="front-end" read_iops_sec=2000 write_iops_sec=1000
    cinder type-create high-iops
    cinder qos-associate c38d72f8 9c746ca5

