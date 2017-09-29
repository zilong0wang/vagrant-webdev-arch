#
# vim: syntax=ruby
#

$script = <<'SCRIPT'

  # begin
  startAt=$(date +"%s")

  # run root script
  sudo -i /vagrant/env/scripts/provision-root.sh
  
  # run vagrant script
  sudo -u vagrant -i /vagrant/env/scripts/provision-vagrant.sh
  
  # TODO: run check command
  # sudo -i /vagrant/env/scripts/provision-check.sh

  # echo configuration
  endAt=$(date +"%s")
  diff=$(($endAt-$startAt))

  echo 
  echo "======================================================================="
  echo '                    _                       _                       '
  echo '                   (_)                     | |                      '
  echo '              __  ___ _ __  ___ _ __   __ _| | _____                '
  echo '              \ \/ / | '"'"'_ \/ __| '"'"'_ \ / _` | |/ / _ \       '
  echo '               >  <| | | | \__ \ | | | (_| |   <  __/               '
  echo '              /_/\_\_|_| |_|___/_| |_|\__,_|_|\_\___|               '
  echo 
  echo "Provisioning finished, just few things you need to do before you start!"
  echo
  echo "  1. Please add the following lines to your hosts file:"
  echo "      192.168.155.10    localhost.dev"
  echo "      192.168.155.10    pm.localhost.dev"
  echo
  echo "  2. Try to visit http://localhost.dev/ and you should see phpinfo()"
  echo
  echo "  3. Try to visit http://pm.localhost.dev/ and you should see phpMyAdmin"
  echo
  echo "Provisioning took $(($diff / 60)) minutes and $(($diff % 60)) seconds"
  echo
  echo "======================================================================="
  echo 

SCRIPT

Vagrant.configure("2") do |config|
  config.vm.box = "terrywang/archlinux"
  config.vm.box_url = "https://vagrantcloud.com/terrywang/boxes/archlinux/versions/3.17.0719/providers/virtualbox.box"
  
  config.vm.provision "shell", inline: $script
  config.ssh.shell = "bash -c 'BASH_ENV=/etc/profile exec bash'"

  config.vm.network "private_network", ip: "192.168.155.10", auto_config:false
  #config.vm.synced_folder '.', '/vagrant', nfs: true

  config.vm.provider "virtualbox" do |v|
    host = RbConfig::CONFIG['host_os']

    # Give VM 1/4 system memory
    if host =~ /darwin/
      # sysctl returns Bytes and we need to convert to MB
      mem = `sysctl -n hw.memsize`.to_i / 1024
    elsif host =~ /linux/
      # meminfo shows KB and we need to convert to MB
      mem = `grep 'MemTotal' /proc/meminfo | sed -e 's/MemTotal://' -e 's/ kB//'`.to_i
    elsif host =~ /mswin|mingw|cygwin/
      # Windows code via https://github.com/rdsubhas/vagrant-faster
      mem = `wmic computersystem Get TotalPhysicalMemory`.split[1].to_i / 1024
    end

    mem = mem / 1024 / 4
    v.customize ["modifyvm", :id, "--memory", mem]
  end
end
