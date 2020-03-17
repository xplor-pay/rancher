First, follow the [[Linux_Server_Commission_Checklist]]

Then proceed with the docker stuff:

<pre>
#plug in the ip of the host being built.  This is used for the 
#gluster mount point and the brick creation later
HOST_IP=10.30.4.129

pvcreate /dev/sdb
vgcreate vg_docker /dev/sdb
lvcreate -n lv_docker vg_docker -l 100%VG
/sbin/mkfs.xfs /dev/vg_docker/lv_docker
mkdir -p /var/lib/docker
echo "/dev/mapper/vg_docker-lv_docker  /var/lib/docker  xfs defaults 0 0" >> /etc/fstab

mount -a

tee /etc/firewalld/services/rancher_worker.xml << EOF
<?xml version="1.0" encoding="utf-8"?>
<service>
  <short>Rancher worker node</short>
  <description>Ports required for communication between Rancher worker nodes</description>
  <!-- worker node inbound rules -->
  <port protocol="tcp" port="22"/>
  <port protocol="tcp" port="80"/>
  <port protocol="tcp" port="443"/>
  <port protocol="tcp" port="2376"/>
  <port protocol="tcp" port="8472"/>
  <port protocol="tcp" port="9099"/>
  <port protocol="tcp" port="10250"/>
  <port protocol="tcp" port="10254"/>
  <port protocol="tcp" port="30000-32767"/>
  <port protocol="udp" port="30000-32767"/>
</service>
EOF

/bin/firewall-cmd --zone=public --add-service rancher_worker --permanent
/bin/firewall-cmd --reload

yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo

yum install -y docker-ce-18.09.5-3.el7.x86_64
systemctl start docker
systemctl enable docker

</pre>

[[Category: Linux]]
