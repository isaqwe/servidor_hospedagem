# Documentação do Projeto: Implementação de um Servidor Web "Seguro"

### Descrição do Projeto

O projeto consistiu no planejamento e implementação de um servidor de hospedagem seguro, incluindo:

#### 1. Planejamento do hardware.

#### 2. Instalação e configuração do sistema operacional.

#### 3. Implementação de hardening no servidor.

#### 4. Instalação do Docker, Docker Compose e demais serviços necessários.


## Configuração do Ambiente

### Planejamento do Hardware

- Sistema Operacional: Ubuntu 20.04 (Focal Fossa)

- Memória RAM: 2 GB

-  Processadores: 2 CPUs

- Rede: IP privado 192.168.56.101 com redirecionamento de portas para o host.

#### Configuração do Vagrant

Vagrantfile

O arquivo Vagrantfile utilizado para provisionar o servidor foi:

```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"
  config.vm.network "private_network", ip: "192.168.56.101"

  config.vm.provider "virtualbox" do |vb|
    vb.name = "ServidorHospedagem"
    vb.memory = "2048"
    vb.cpus = 2
  end

  config.vm.network "forwarded_port", guest: 80, host: 8080
  config.vm.network "forwarded_port", guest: 443, host: 8443

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
```

#### Etapas de Implementação

1. Atualização e Instalação de Pacotes

- O provisionamento do servidor incluiu:

  - Atualização do sistema operacional: sudo apt update && sudo apt upgrade -y

- Instalação do Docker:

  - Instalado com sudo apt install -y docker.io.

  - Configurado para iniciar automaticamente com sudo systemctl enable docker.

  - Instalação do Docker Compose: sudo apt install -y docker-compose

  - E ao final do provisionamento a intalaçoa de um container Nginx utilizando o comando: `docker run -d -p 80:80 --name webserver nginx`

2. Configuração de Firewall (UFW)

    Padrão:

- Negar conexões de entrada: sudo ufw default deny incoming

- Permitir conexões de saída: sudo ufw default allow outgoing

- Exceções configuradas para:

  - SSH (porta 22)

  - HTTP (porta 80)

  - HTTPS (porta 443)

  - Firewall ativado com sudo ufw enable

3. Implementação de Hardening

- Configuração de Fail2Ban:

  - Instalado com sudo apt install -y fail2ban.

  - Configurado para iniciar automaticamente com sudo systemctl enable fail2ban.

  - Adicionada segurança para o Docker:

    - Grupo docker criado para permitir gerenciamento seguro dos containers.

#### Demonstração

- Provisionamento Automático

  -  O provisionamento será realizado automaticamente com o comando `vagrant up`

  - O ambiente foi configurado para hospedar serviços HTTP/HTTPS nas portas 8080 e 8443 no host.

  - Logs de provisionamento estão disponíveis para revisão no terminal.

  - E posteriormente para entrar no servidor `vagrant ssh`

#### Execução de Serviços

- Containers Docker:

  - Verificação com `docker ps`. 
  - Acessando `localhost:8080`, se o nginx aparecer esta rodando.

- Firewall:

  - Configuração validada com `sudo ufw status`. 

## Logs e Evidências

### Logs do Vagrant

- Os logs do provisionamento que foram capturados:

Visualizar no provisionamento.

### Logs de Hardening

- Verificar se os logs foram criados com `ls /var/log/`, verificar de os arquivos `ufw.log` e `fail2ban.log` existem.

- Firewall (UFW):

O UFW pode não ter criado o arquivo de log por falta de atividades que gerem entradas ou devido à configuração do nível de log. 

`sudo nano /etc/ufw/ufw.conf`

Certifique-se de que a linha LOGGING esteja configurada corretamente se não adicione ao script
```
LOGLEVEL=high
LOGGING=on
```
Salve o arquivo.

Reinicie o UFW:

```
sudo ufw disable
sudo ufw enable
```
Com isso verificar a criação do aquivo log com o `ls`

E por fim visualisar o log pelo `sudo cat /var/log/ufw.log`

- Fail2Ban:

Utilisar `sudo cat /var/log/fail2ban.log`

## Conclusão

O projeto atendeu aos objetivos propostos, garantindo um servidor web seguro e funcional. A utilização das ferramentas  Vagrant, Docker e Fail2Ban permitiu a implementação de um ambiente de produção.