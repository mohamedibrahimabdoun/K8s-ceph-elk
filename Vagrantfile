
Vagrant.configure("2") do |config|

   config.vm.box = "centos/7"
    master01_disk = './master01_secondDisk.vmdk'
	master02_disk = './master02_secondDisk.vmdk'
	master03_disk = './master03_secondDisk.vmdk'
	
	node01_disk = './node01_secondDisk.vmdk'
	node02_disk = './node02_secondDisk.vmdk'
	
    config.vm.define "master01" do |kube|
    kube.vm.hostname = "master01.myk8sdomain.com"
    kube.vm.network "private_network", ip: "10.10.1.21"
	kube.vm.network "private_network", ip: "192.168.1.21"
    config.vm.provider :virtualbox do |vb|
       vb.customize ["modifyvm", :id, "--memory", "2048"]
       vb.customize ["modifyvm", :id, "--cpus", "1"]
	if not File.exists?(master01_disk)
      vb.customize ['createhd', '--filename', master01_disk, '--variant', 'Fixed', '--size', 5 * 1024]
    end
	#vb.customize ['storagectl', :id, '--name', 'SATA Controller', '--add', 'sata', '--portcount', 6]
	vb.customize ['storageattach', :id,  '--storagectl', 'IDE', '--port', 1, '--device', 1, '--type', 'hdd', '--medium', master01_disk]
    end  
    kube.vm.provision "shell", inline: $script_master
  end
  
  
    config.vm.define "master02" do |kube|
    kube.vm.hostname = "master02.myk8sdomain.com"
    kube.vm.network "private_network", ip: "10.10.1.22"
	kube.vm.network "private_network", ip: "192.168.1.22"
    config.vm.provider :virtualbox do |vb|
       vb.customize ["modifyvm", :id, "--memory", "2048"]
       vb.customize ["modifyvm", :id, "--cpus", "1"]
	if not File.exists?(master02_disk)
      vb.customize ['createhd', '--filename', master02_disk, '--variant', 'Fixed', '--size', 5 * 1024]
    end
	#vb.customize ['storagectl', :id, '--name', 'SATA Controller2', '--add', 'sata', '--portcount', 6]
	vb.customize ['storageattach', :id,  '--storagectl', 'IDE', '--port', 1, '--device', 1, '--type', 'hdd', '--medium', master02_disk]
    end  
    kube.vm.provision "shell", inline: $script_master
  end
  
    config.vm.define "master03" do |kube|
    kube.vm.hostname = "master03.myk8sdomain.com"
    kube.vm.network "private_network", ip: "10.10.1.23"
	kube.vm.network "private_network", ip: "192.168.1.23"
    config.vm.provider :virtualbox do |vb|
       vb.customize ["modifyvm", :id, "--memory", "2048"]
       vb.customize ["modifyvm", :id, "--cpus", "1"]
	if not File.exists?(master03_disk)
      vb.customize ['createhd', '--filename', master03_disk, '--variant', 'Fixed', '--size', 5 * 1024]
    end
	#vb.customize ['storagectl', :id, '--name', 'SATA Controller3', '--add', 'sata', '--portcount', 6]
	vb.customize ['storageattach', :id,  '--storagectl', 'IDE', '--port', 1, '--device', 1, '--type', 'hdd', '--medium', master03_disk]
    end  
    kube.vm.provision "shell", inline: $script_master
  end
 
    config.vm.define "node-01" do |kube|
    kube.vm.hostname = "node-01.myk8sdomain.com"
    kube.vm.network "private_network", ip: "10.10.1.31"
	kube.vm.network "private_network", ip: "192.168.1.31"
    config.vm.provider :virtualbox do |vb|
       vb.customize ["modifyvm", :id, "--memory", "2048"]
       vb.customize ["modifyvm", :id, "--cpus", "1"]
	if not File.exists?(node01_disk)
      vb.customize ['createhd', '--filename', node01_disk, '--variant', 'Fixed', '--size', 5 * 1024]
    end
	#vb.customize ['storagectl', :id, '--name', 'SATA Controller4', '--add', 'sata', '--portcount', 6]
	vb.customize ['storageattach', :id,  '--storagectl', 'IDE', '--port', 1, '--device',1, '--type', 'hdd', '--medium', node01_disk]
    end  
    kube.vm.provision "shell", inline: $script_master
  end

  
 config.vm.define "node-02" do |kube|
    kube.vm.hostname = "node-02.myk8sdomain.com"
    kube.vm.network "private_network", ip: "10.10.1.32"
	kube.vm.network "private_network", ip: "192.168.1.32"
    config.vm.provider :virtualbox do |vb|
       vb.customize ["modifyvm", :id, "--memory", "2048"]
       vb.customize ["modifyvm", :id, "--cpus", "1"]
	if not File.exists?(node02_disk)
      vb.customize ['createhd', '--filename', node02_disk, '--variant', 'Fixed', '--size', 5 * 1024]
    end
	#vb.customize ['storagectl', :id, '--name', 'SATA Controller5', '--add', 'sata', '--portcount', 6]
	vb.customize ['storageattach', :id,  '--storagectl', 'IDE', '--port', 1, '--device', 1, '--type', 'hdd', '--medium', node02_disk]
    end  
    kube.vm.provision "shell", inline: $script_master
  end


$script_master = <<SCRIPT
echo I am provisioning...
echo "r00tme" | sudo passwd --stdin  root
echo "###########Setting Kubernetes Params################"

sudo cp /vagrant/hosts /etc/hosts
sudo cp /vagrant/kubernetes.repo /etc/yum.repos.d/kubernetes.repo
sudo cp /vagrant/centos.repo /etc/yum.repos.d/centos.repo
sudo cp /vagrant/k8s.conf /etc/sysctl.d/k8s.conf
sudo sysctl --system
sudo echo 1 > /proc/sys/net/ipv4/ip_forward
sudo setenforce 0
sudo sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
sudo yum -y update 
sudo yum -y install kubelet kubeadm kubectl docker
sudo systemctl enable docker
sudo systemctl start docker
sudo systemctl start kubelet
sudo systemctl enable kubelet

sudo useradd k8s
echo "k8s123" | sudo passwd --stdin k8s

echo "###########Setting Elastic Search Parameters###############"
sudo su -
yum install -y vim net-tools java-1.8.0-openjdk
echo "elastic - nofile 65536" >>  /etc/security/limits.conf
echo  "vm.max_map_count=262144" >> /etc/sysctl.conf
sysctl -p
sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
systemctl restart sshd.service
echo "###########Downloading TARS###############"
sudo cp /vagrant/hosts /etc/hosts
sudo useradd elastic
echo "elastic" | sudo passwd --stdin elastic
sudo su - elastic
cd /home/elastic/
curl -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.5.0.tar.gz
#curl -O https://artifacts.elastic.co/downloads/kibana/kibana-6.5.0-linux-x86_64.tar.gz

tar xfvz elasticsearch-6.5.0.tar.gz
#tar xfz kibana-6.5.0-linux-x86_64.tar.gz

sudo chown elastic:elastic -R /home/elastic/elasticsearch-*
#sudo chown elastic:elastic -R /home/elastic/kibana-*






sudo swapoff -a

SCRIPT
end
