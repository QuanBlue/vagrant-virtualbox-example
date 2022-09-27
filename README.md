# VAGRANT WITH VIRTUALBOX EXAMPLE

-  Vagrant is written in: **Ruby**

-  It can be used in projects written in other programming languages such as **PHP**,**Python**, **Java**, **C#**, and **JavaScript**

**Vagrant cheat sheet:** [Cheat sheet](https://gist.github.com/wpscholar/a49594e2e2b918f4d0c4)  
**Vagrant machine setting:** [Machine setting](https://www.vagrantup.com/docs/vagrantfile/machine_settings)

## <u>Create virtual machine with **Vagrant** and **VirtualBox**</u>

-  Create folder contain virtual machine (ex: vagrant_ex)

```
mkdir vagrant_ex
```

-  Create **Vagrantfile** in this new folder (vargrant_ex). Custom **Vagrantfile**

   -  Create virtual machine **ubuntu 22.04** with image [fasmat/ubuntu2204-desktop](https://app.vagrantup.com/fasmat/boxes/ubuntu2204-desktop)

   ```sh
   config.vm.box = "fasmat/ubuntu2204-desktop"
   ```

   -  Disk store virtual machine **30GB**

   ```sh
   config.disksize.size = "30GB"
   ```

   -  Create user for virtual machine **vagrant** and **root**

   ```sh
   config.vm.provision "shell", inline: <<-'SHELL'

     sed -i 's/^#* *\(PermitRootLogin\)\(.*\)$/\1 yes/' /etc/ssh/sshd_config
     sed -i 's/^#* *\(PasswordAuthentication\)\(.*\)$/\1 yes/' /etc/ssh/sshd_config
     systemctl restart sshd.service
     echo -e "vagrant\nvagrant" | (passwd vagrant)
     echo -e "root\nroot" | (passwd root)
   SHELL
   ```

   -  Config virtual machine with **name**, **memory**, **cpu**,....

   ```ruby
   config.vm.provider "virtualbox" do |v|
         v.name = "test"
         v.gui = true
         v.memory = 2048
         v.cpus = 2
       end
   ```

   -  Install **apache2** on virtual machine by **bootstrap.sh**

   ```ruby
   config.vm.provision :shell, path: "bootstrap.sh"
   ```

   -  Config network connection

   ```ruby
   config.vm.network "forwarded_port", guest: 80, host: 8080, auto_correct: true
   config.vm.network "public_network", ip: "192.168.0.108"
   ```

   -  Run command line on new created virtual machine

   ```sh
   config.vm.provision "shell", inline: <<-SHELL
       export DEBIAN_FRONTEND=noninteractive
       apt-get update
       apt-get install -y git
       apt-get autoremove -y
       sudo chmod -R 777 /var/www/html
     SHELL
   ```

   -  Copy directory **./v-root** on my machine to directory **/vagrant** in vm

   ```ruby
   config.vm.synced_folder 'v-root', '/vagrant'
   ```

-  Start install virtual machine auto

```sh
cd vagrant_ex
vagrant up
```

It will automatic `download image`, `install` and `config` our new virtual machine through **Vagrantfile**. When this process done, **VirtualBox** will auto start and run this virtual machine.
