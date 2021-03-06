Vagrant.configure("2") do |config|
  config.vm.define "dapps" do |dapps|
    dapps.vm.box = "ubuntu/trusty64"
    dapps.vm.network "private_network", type: "dhcp"
    # Change from "~/Projects/hackathon-ethereum-app" to an existing, and non-encrypted, folder on your host if the mount fails
    dapps.vm.synced_folder "~/Projects/hackathon-ethereum-app", "/home/vagrant/dapps", nfs: true, nfs_udp: false, create: true

    # React server
    dapps.vm.network :forwarded_port, guest: 3000, host: 3000

    # Webpack server
    dapps.vm.network :forwarded_port, guest: 8000, host: 8000

    # Eth node
    dapps.vm.network :forwarded_port, guest: 8545, host: 8545
    dapps.vm.network :forwarded_port, guest: 9545, host: 9545

    # IPFS
    # dapps.vm.network :forwarded_port, guest: 4001, host: 4001
    # dapps.vm.network :forwarded_port, guest: 5001, host: 5001
    # dapps.vm.network :forwarded_port, guest: 8080, host: 8080

    dapps.vm.provider "virtualbox" do |v|
      host = RbConfig::CONFIG['host_os']

      # Setup hardware config, host-os specific. Your host should have at least 6GB memory
      if host =~ /darwin/
        # mac
        cpus = `sysctl -n hw.ncpu`.to_i
        # sysctl returns Bytes and we need to convert to MB
        # mem = `sysctl -n hw.memsize`.to_i / 1024 / 1024 / 2
        mem = 3072
      elsif host =~ /linux/
        # linux
        cpus = `nproc`.to_i
        # meminfo shows KB and we need to convert to MB
        # mem = `grep 'MemTotal' /proc/meminfo | sed -e 's/MemTotal://' -e 's/ kB//'`.to_i / 1024 / 4
        mem = 3072
      else
        # windows
        cpus = 2
        mem = 3072
      end

      v.customize ["modifyvm", :id, "--memory", mem]
      v.customize ["modifyvm", :id, "--cpus", cpus]
      v.customize ["guestproperty", "set", :id, "/VirtualBox/GuestAdd/VBoxService/--timesync-set-threshold", 1000]
    end

    dapps.vm.provision "file", source: "dotscreenrc", destination: "~/.screenrc"

    dapps.vm.provision :shell, path: "bootstrap.sh"
  end
end
