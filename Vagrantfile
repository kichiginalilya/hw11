# -*- mode: ruby -*-
# vim: set ft=ruby :
home = ENV['HOME']
ENV["LC_ALL"] = "en_US.UTF-8"

MACHINES = {
  :lvm => {
        :box_name => "centos/7",
        :box_version => "1804.02",
        :ip_addr => '192.168.56.152',
    :disks => {
        :sata1 => {
            :dfile => home + '/VirtualBox VMs/sata1.vdi',
            :size => 10240,
            :port => 1
        },
        :sata2 => {
            :dfile => home + '/VirtualBox VMs/sata2.vdi',
            :size => 2048, # Megabytes
            :port => 2
        },
        :sata3 => {
            :dfile => home + '/VirtualBox VMs/sata3.vdi',
            :size => 1024, # Megabytes
            :port => 3
        },
        :sata4 => {
            :dfile => home + '/VirtualBox VMs/sata4.vdi',
            :size => 1024,
            :port => 4
        }
    }
  },
}

Vagrant.configure("2") do |config|

    config.vm.box_version = "1804.02"
    MACHINES.each do |boxname, boxconfig|

        config.vm.define boxname do |box|

            box.vm.box = boxconfig[:box_name]
            box.vm.host_name = boxname.to_s

            #box.vm.network "forwarded_port", guest: 3260, host: 3260+offset

            box.vm.network "private_network", ip: boxconfig[:ip_addr]

            box.vm.provider :virtualbox do |vb|
                    vb.customize ["modifyvm", :id, "--memory", "256"]
                    needsController = false
            boxconfig[:disks].each do |dname, dconf|
                unless File.exist?(dconf[:dfile])
                  vb.customize ['createhd', '--filename', dconf[:dfile], '--variant', 'Fixed', '--size', dconf[:size]]
                                  needsController =  true
                            end

            end
                    if needsController == true
                       vb.customize ["storagectl", :id, "--name", "SATA", "--add", "sata" ]
                       boxconfig[:disks].each do |dname, dconf|
                           vb.customize ['storageattach', :id,  '--storagectl', 'SATA', '--port', dconf[:port], '--device', 0, '--type', 'hdd', '--medium', dconf[:dfile]]
                       end
                    end
            end

        box.vm.provision "shell", inline: <<-SHELL
            mkdir -p ~root/.ssh
            cp ~vagrant/.ssh/auth* ~root/.ssh
            yum install -y mdadm smartmontools hdparm gdisk wget
            vgrename VolGroup00 OtusRoot
            wget https://gist.github.com/lalbrekht/cdf6d745d048009dbe619d9920901bf9/raw/f9ae66d2d2fc727791d5ea69d67cc5760c4c5fea/gistfile1.txt -O /tmp/fstab
            mv -f /tmp/fstab /etc/fstab
            wget https://gist.github.com/lalbrekht/ef78c39c236ae223acfb3b5e1970001c/raw/3bdf1d1a374eff4a5696dcea226ae5c4ca4d6374/gistfile1.txt -O /tmp/grub
            mv -f /tmp/grub /etc/default/grub
            wget https://gist.github.com/lalbrekht/1a9cae3cb64ce2dc7bd301e48090bd56/raw/aa1cf0b3fd794d454dfa7fc2770784ef29ae89ea/gistfile1.txt -O /tmp/grub.cfg
            mv -f /tmp/grub.cfg /boot/grub2/grub.cfg
            mkinitrd -f -v /boot/initramfs-$(uname -r).img $(uname -r)
            mkdir /usr/lib/dracut/modules.d/01test
            wget https://gist.github.com/lalbrekht/e51b2580b47bb5a150bd1a002f16ae85/raw/80060b7b300e193c187bbcda4d8fdf0e1c066af9/gistfile1.txt -O /tmp/module-setup.sh
            mv -f /tmp/module-setup.sh /usr/lib/dracut/modules.d/01test/module-setup.sh
            wget https://gist.github.com/lalbrekht/ac45d7a6c6856baea348e64fac43faf0/raw/69598efd5c603df310097b52019dc979e2cb342d/gistfile1.txt -O /tmp/test.sh
            mv -f /tmp/test.sh /usr/lib/dracut/modules.d/01test/test.sh
            mkinitrd -f -v /boot/initramfs-$(uname -r).img $(uname -r)
            dracut -f -v
          SHELL

        end
    end
  end

