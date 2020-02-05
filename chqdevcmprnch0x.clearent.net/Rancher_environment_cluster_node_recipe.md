First, follow the [[Linux_Server_Commission_Checklist]]

Then proceed with the docker stuff:

<pre>
#plug in the ip of the host being built.  This is used for the 
#gluster mount point and the brick creation later
HOST_IP=10.30.4.129

#create the docker volume
pvcreate /dev/sdb
vgcreate vg_docker /dev/sdb
lvcreate -n lv_docker vg_docker -l 100%VG
/sbin/mkfs.xfs /dev/vg_docker/lv_docker
mkdir -p /var/lib/docker
echo "/dev/mapper/vg_docker-lv_docker  /var/lib/docker  xfs defaults 0 0" >> /etc/fstab

#create the gluster volume
pvcreate /dev/sdc
vgcreate vg_gluster /dev/sdc
lvcreate -n lv_gluster vg_gluster -l 100%VG
/sbin/mkfs.xfs /dev/vg_gluster/lv_gluster
mkdir -p /gluster/brick
echo "/dev/mapper/vg_gluster-lv_gluster /gluster/brick  xfs defaults  0 0" >> /etc/fstab

mount -a

tee /etc/firewalld/services/rancher_controlplane.xml << EOF
<?xml version="1.0" encoding="utf-8"?>
<service>
  <short>Rancher controlplane node</short>
  <description>Ports required for communication between Rancher controlplane nodes</description>
  <!-- controlplane node inbound rules-->
  <port protocol="tcp" port="80"/>
  <port protocol="tcp" port="443"/>
  <port protocol="tcp" port="2376"/>
  <port protocol="tcp" port="6443"/>
  <port protocol="udp" port="8472"/>
  <port protocol="tcp" port="9099"/>
  <port protocol="tcp" port="10250"/>
  <port protocol="tcp" port="10254"/>
  <port protocol="tcp" port="30000-32767"/>
  <port protocol="udp" port="30000-32767"/>
</service>
EOF

tee /etc/firewalld/services/rancher_etcd.xml << EOF
<?xml version="1.0" encoding="utf-8"?>
<service>
  <short>Rancher etcd node</short>
  <description>Ports required for communication between Rancher etcd nodes</description>
  <!-- etcd node inbound rules -->
  <port protocol="tcp" port="2376"/>
  <port protocol="tcp" port="2379"/>
  <port protocol="tcp" port="2380"/>
  <port protocol="udp" port="8472"/>
  <port protocol="tcp" port="9099"/>
  <port protocol="tcp" port="10250"/>
</service>
EOF

tee /etc/firewalld/services/rancher_gluster.xml << EOF
<?xml version="1.0" encoding="utf-8"?>
<service>
  <short>Rancher gluster node</short>
  <description>Ports required for the Gluster storage cluster</description>
  <port protocol="tcp" port="24007"/>
  <port protocol="tcp" port="24008"/>
  <port protocol="tcp" port="49152"/>
  <port protocol="tcp" port="49153"/>
  <port protocol="tcp" port="49154"/>
</service>
EOF

/bin/firewall-cmd --zone=public --add-service rancher_controlplane --permanent
/bin/firewall-cmd --zone=public --add-service rancher_etcd --permanent
/bin/firewall-cmd --zone=public --add-service rancher_gluster --permanent
/bin/firewall-cmd --reload

yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo

yum install -y docker-ce-18.09.5-3.el7.x86_64
systemctl start docker
systemctl enable docker

# install gluster
yum install -y centos-release-gluster
yum install -y glusterfs-server
systemctl enable glusterd
systemctl start glusterd

</pre>

[[Category: Linux]]
