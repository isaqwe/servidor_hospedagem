Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"
  config.vm.network "private_network", ip: "192.168.56.101"
  
  config.vm.provider "virtualbox" do |vb|
    vb.name = "ServidorHospedagem"
    vb.memory = "2048"
    vb.cpus = 2
  end

  # Mapeamento de portas da VM para o host (localhost)
  config.vm.network "forwarded_port", guest: 80, host: 8080
  config.vm.network "forwarded_port", guest: 443, host: 8443

  # Provisionamento
  config.vm.provision "shell", inline: <<-SHELL
    sudo apt update && sudo apt upgrade -y
    sudo apt install -y docker.io
    sudo systemctl enable docker
    sudo systemctl start docker
    sudo apt install -y docker-compose
    sudo groupadd docker || true
    sudo usermod -aG docker vagrant
    sudo ufw default deny incoming
    sudo ufw default allow outgoing
    sudo ufw allow 22
    sudo ufw allow 80
    sudo ufw allow 443
    sudo ufw --force enable
    sudo apt install -y fail2ban
    sudo systemctl enable fail2ban
    sudo systemctl start fail2ban
    docker run -d -p 80:80 --name webserver nginx
    echo "Provisionamento concluído com sucesso."
  SHELL
end
