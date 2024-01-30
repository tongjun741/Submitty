Vagrant.configure(2) do |config|
  if Vagrant.has_plugin?('vagrant-env')
    config.env.enable
  end

  config.vm.box = "bento/ubuntu-22.04"

  # The time in seconds that Vagrant will wait for the machine to boot and be accessible.
  config.vm.boot_timeout = 600

  # vm_name = 'ubuntu-22.04'

  config.vm.provider 'virtualbox' do |vb, override|
    # We limit resources when running on CI to avoid resource exhaustion and it isn't used for grading stuff or
    # other things we do in dev.
    # vb.memory = ON_CI ? 1024 : 2048
    # vb.cpus = ON_CI ? 1 : 2
    vb.memory = 1024
    vb.cpus = 1

    # When you put your computer (while running the VM) to sleep, then resume work some time later the VM will be out
    # of sync timewise with the host for however long the host was asleep. Of course, the VM by default will
    # detect this and if the drift is great enough, it'll resync things such that the time matches, otherwise
    # the VM will just slowly adjust the timing so they'll eventually match. However, this can cause confusion when
    # times are important for late day calculations and building so we set the maximum time the VM and host can drift
    # to be 10 seconds at most which should make things work generally ideally
    vb.customize ['guestproperty', 'set', :id, '/VirtualBox/GuestAdd/VBoxService/--timesync-set-threshold', 10000 ]

    # VirtualBox sometimes has isseus with getting the DNS to work inside of itself for whatever reason.
    # While it will sometimes randomly work, we can just set VirtualBox to use a DNS proxy from the host,
    # which seems to be far more reliable in having the DNS work, rather than leaving it to VirtualBox.
    # See https://serverfault.com/a/453260 for more info.
    # vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]

    # Sometimes Vagrant will error on trying to SSH into the machine when it starts, due to a bug in how
    # Ubuntu 20.04 and later setup the virtual serial port, where the machine takes minutes to start, plus
    # occasionally restarting. Modifying the behavior of the uart fields, as well as disabling features like USB and
    # audio (which we don't need) seems to greatly reduce boot times and make vagrant work consistently. See
    # https://github.com/hashicorp/vagrant/issues/11777 for more info.
    vb.customize ["modifyvm", :id, "--uart1", "0x3F8", "4"]
    vb.customize ["modifyvm", :id, "--uartmode1", "file", File::NULL]
    vb.customize ["modifyvm", :id, "--audio", "none"]
    vb.customize ["modifyvm", :id, "--usb", "off"]
    vb.customize ["modifyvm", :id, "--uart1", "off"]
    vb.customize ["modifyvm", :id, "--uart2", "off"]
    vb.customize ["modifyvm", :id, "--uart3", "off"]
    vb.customize ["modifyvm", :id, "--uart4", "off"]

    if ARGV.include?('ssh')
      override.ssh.timeout = 20
    end
  end

  config.vm.provision :shell, :inline => " sudo timedatectl set-timezone America/New_York", run: "once"

  if ARGV.include?('ssh')
    config.ssh.username = 'root'
    config.ssh.password = 'vagrant'
    config.ssh.insert_key = 'true'
  end
end
