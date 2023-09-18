# rancherHandsOn

declare -a IPS=( c6eb1619612c.mylabserver.com c6eb1619611c.mylabserver.com c6eb1619613c.mylabserver.com)

ssh-copy-id cloud_user@c6eb1619612c.mylabserver.com
ssh-copy-id cloud_user@c6eb1619611c.mylabserver.com
ssh-copy-id cloud_user@c6eb1619613c.mylabserver.com



ansible all -i inventory/mycluster/hosts.yaml -m shell -a "sudo systemctl stop firewalld && sudo systemctl disable firewalld" -u cloud_user
ansible all -i inventory/mycluster/hosts.yaml -m shell -a "echo 'net.ipv4.ip_forward=1' | sudo tee -a /etc/sysctl.conf" -u cloud_user
ansible all -i inventory/mycluster/hosts.yaml -m shell -a "sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab && sudo swapoff -a" -u cloud_user

ansible-playbook -i inventory/mycluster/hosts.yaml --become --become-user=root cluster.yml -u cloud_user


troubleshoot:
etcd : Gen_certs | run cert generation script fails on SSL #2343
https://github.com/kubernetes-sigs/kubespray/issues/2343

/usr/local/bin/etcdctl endpoint --cluster status && /usr/local/bin/etcdctl endpoint --cluster health 2>&1 | grep -v 'Error: unhealthy cluster' >/dev/null