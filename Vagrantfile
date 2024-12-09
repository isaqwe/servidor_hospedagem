Vagrant.configure("2") do |config|
  # Defina a imagem base
  config.vm.box = "ubuntu/focal64"
  
  # Configure a rede privada
  config.vm.network "private_network", type: "dhcp"
  
  # Configure os recursos de hardware
  config.vm.provider "virtualbox" do |vb|
    vb.name = "ServidorHospedagem"
    vb.memory = "2048"
    vb.cpus = 2
  end

  # Provisionamento do servidor
  config.vm.provision "shell", inline: <<-SHELL
    # Atualização do sistema
    sudo apt update && sudo apt upgrade -y
    
    # Instalação do Docker e Docker Compose
    sudo apt install -y docker.io docker-compose
    
    # Configuração inicial de segurança
    sudo ufw allow 22
    sudo ufw enable
    sudo apt install -y fail2ban
    
    # Configuração de SSH segura
    echo "PermitRootLogin no" | sudo tee -a /etc/ssh/sshd_config
    sudo systemctl restart sshd
  SHELL
end
