$ip_manager = "10.11.12.200"

$qtd_workers = 8
$ip_workers = [
  "10.11.12.201",
  "10.11.12.202",
  "10.11.12.203",
  "10.11.12.204",
  "10.11.12.205",
  "10.11.12.206",
  "10.11.12.207",
  "10.11.12.208"
]

$docker = <<SCRIPT
apt-get update
apt-get install -y docker.io
systemctl start docker
systemctl enable docker
usermod -aG docker vagrant
SCRIPT

$manager = <<SCRIPT
docker swarm init --advertise-addr #{$ip_manager}
docker swarm join-token --quiet worker > /vagrant/worker_token
SCRIPT

$worker = <<SCRIPT
docker swarm join --token $(cat /vagrant/worker_token) #{$ip_manager}
SCRIPT

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"

  config.vm.provider "virtualbox" do |vb|
    vb.memory = 1024
    vb.cpus = 1
  end

  config.vm.define "manager" do |manager|
    manager.vm.network "private_network", ip: $ip_manager
    manager.vm.hostname = "manager"

    manager.vm.provision "shell", inline: $docker
    manager.vm.provision "shell", inline: $manager

    manager.vm.provider "virtualbox" do |vb|
      vb.name = "manager"
    end
  end

  (1..$qtd_workers).each do |i|
    config.vm.define "worker0#{i}" do |worker|
      $id_ip = (i - 1)
      worker.vm.network "private_network", ip: $ip_workers[$id_ip]
      worker.vm.hostname = "worker0#{i}"

      worker.vm.provision "shell", inline: $docker
      worker.vm.provision "shell", inline: $worker

      worker.vm.provider "virtualbox" do |vb|
        vb.name = "worker0#{i}"
      end
    end
  end

end
