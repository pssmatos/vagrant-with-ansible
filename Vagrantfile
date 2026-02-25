# See: https://manski.net/2016/09/vagrant-multi-machine-tutorial/
# for information about machine names on private network
Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-24.04"

  config.vm.provision "shell", inline: <<-SHELL
    export DEBIAN_FRONTEND=noninteractive
    apt-get update --yes 
    apt-get install --yes iputils-ping
    apt-get install --yes python3
  SHELL

  config.vm.define "host1" do |host1|
    host1.vm.box = "bento/ubuntu-24.04"
    host1.vm.hostname = "host1"
    host1.vm.network "private_network", ip: "192.168.56.11"

    # We want to access tomcat from the host using port 8080
    host1.vm.network "forwarded_port", guest: 8080, host: 8080
  end

  config.vm.define "host2" do |host2|
    host2.vm.box = "bento/ubuntu-24.04"
    host2.vm.hostname = "host2"
    host2.vm.network "private_network", ip: "192.168.56.12"
  end

  config.vm.define "ansible" do |ansible|
    ansible.vm.box = "bento/ubuntu-24.04"
    ansible.vm.hostname = "ansible"
    ansible.vm.network "private_network", ip: "192.168.56.10"

    # For some Windows and for running ansible "inside" jenkins
    # ansible.vm.synced_folder ".", "/vagrant", mount_options: ["dmode=777,fmode=777"]
    # It seems that ansible has security issues with the previous command. Use instead:
    ansible.vm.synced_folder ".", "/vagrant", mount_options: ["dmode=775,fmode=600"]

    # For acessing jenkins in 8081
    # ansible.vm.network "forwarded_port", guest: 8080, host: 8081

    ansible.vm.provision "shell", inline: <<-SHELL
      export DEBIAN_FRONTEND=noninteractive
      apt-get install --yes --no-install-recommends apt-utils
      apt-get install --yes software-properties-common
      apt-add-repository --yes ppa:ansible/ansible
      apt-get install --yes ansible
      # For jenkins
      # wget http://mirrors.jenkins.io/war-stable/latest/jenkins.war
      # java -jar jenkins.war
    SHELL
  end
end
