Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"

  #config.vm.network "forwarded_port", guest: 8080, host: 8080

  config.vm.provision "shell", inline: <<-SHELL
    # Actualiza e instala dependencias
    apt-get update
    apt-get install -y openjdk-11-jdk curl gnupg2 apt-transport-https ca-certificates software-properties-common

    # Instala Docker
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
    add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
    apt-get update
    apt-get install -y docker-ce docker-ce-cli containerd.io
    usermod -aG docker vagrant

    # Instala Jenkins
    curl -fsSL https://pkg.jenkins.io/debian/jenkins.io.key | apt-key add -
    sh -c 'echo deb https://pkg.jenkins.io/debian binary/ > /etc/apt/sources.list.d/jenkins.list'
    apt-get update
    apt-get install -y jenkins

    # Inicia Jenkins
    systemctl enable jenkins
    systemctl start jenkins
  SHELL
end
