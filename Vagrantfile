# -*- mode: ruby -*-
# vi: set ft=ruby :

# To use this script and prepare your build environment, run the following
# command in the same directory as the Vagrantfile.
# B2G_PATH={path to your B2G directory} vagrant up

VAGRANTFILE_API_VERSION = "2"

# This script will be run on the first start and it will set up the build
# environment.
# All you need to do afterwards is:
# * vagrant ssh
# * Unplug/Plug the phone; run `adb devices` to make sure that the phone is
# listed.
# * cd B2G
# * ./configure.sh {your device}
# * ./build.sh
$bootstrap = <<SCRIPT

# Installing all build prerequisites.
apt-get update
apt-get install -y python-software-properties
add-apt-repository -y ppa:nilarimogard/webupd8
apt-get update
apt-get install -y autoconf2.13 bison bzip2 ccache curl flex gawk gcc g++ g++-multilib git ia32-libs lib32ncurses5-dev lib32z1-dev libgl1-mesa-dev libx11-dev libasound2 make zip android-tools-adb

# Set ccache max size to 3GB
ccache --max-size 3GB

# Set the permission filters to the right devices.
# cat <<EOF >> /etc/udev/rules.d/android.rules
# SUBSYSTEM=="usb", ATTR{idVendor}=="19d2", MODE="0666", GROUP="vagrant"
# SUBSYSTEM=="usb", ATTR{idVendor}=="05c6", MODE="0666", GROUP="vagrant"
# EOF
cat <<EOF >> /etc/udev/rules.d/51-android.rules
#Geekphone
SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", MODE="0666", GROUP="vagrant"
#Acer      0502
SUBSYSTEM=="usb", SYSFS{idVendor}=="0502", MODE="0666", GROUP="vagrant"
#Dell     413c
SUBSYSTEM=="usb", SYSFS{idVendor}=="413c", MODE="0666", GROUP="vagrant"
#Foxconn     0489
SUBSYSTEM=="usb", SYSFS{idVendor}=="0489", MODE="0666", GROUP="vagrant"
#Asus     091E
SUBSYSTEM=="usb", SYSFS{idVendor}=="091e", MODE="0666", GROUP="vagrant"
#HTC     0bb4
SUBSYSTEM=="usb", SYSFS{idVendor}=="0bb4", MODE="0666", GROUP="vagrant"
#Huawei     12d1
SUBSYSTEM=="usb", SYSFS{idVendor}=="12d1", MODE="0666", GROUP="vagrant"
#Kyocera     0482
SUBSYSTEM=="usb", SYSFS{idVendor}=="0482", MODE="0666", GROUP="vagrant"
#LG     1004
SUBSYSTEM=="usb", SYSFS{idVendor}=="1004", MODE="0666", GROUP="vagrant"
#Motorola     22b8
SUBSYSTEM=="usb", SYSFS{idVendor}=="22b8", MODE="0666", GROUP="vagrant"
#Nvidia     0955
SUBSYSTEM=="usb", SYSFS{idVendor}=="0955", MODE="0666", GROUP="vagrant"
#Pantech     10A9
SUBSYSTEM=="usb", SYSFS{idVendor}=="10A9", MODE="0666", GROUP="vagrant"
#Samsung     04e8
SUBSYSTEM=="usb", SYSFS{idVendor}=="04e8", MODE="0666", GROUP="vagrant"
#Sharp     04dd
SUBSYSTEM=="usb", SYSFS{idVendor}=="04dd", MODE="0666", GROUP="vagrant"
#Sony Ericsson     0fce
SUBSYSTEM=="usb", SYSFS{idVendor}=="0fce", MODE="0666", GROUP="vagrant"
#ZTE     19d2
SUBSYSTEM=="usb", SYSFS{idVendor}=="19d2", MODE="0666", GROUP="vagrant"
#TCL
SUBSYSTEM=="usb", ATTR{idVendor}=="05c6", MODE="0666", GROUP="vagrant"
EOF
#chmod a+r /etc/udev/rules.d/android.rules
chmod a+r /etc/udev/rules.d/51-android.rules
service udev restart

# Not sure if it's necessary but the build complaints about the Java version.
apt-get purge -y openjdk*
add-apt-repository -y ppa:webupd8team/java
apt-get update
echo debconf shared/accepted-oracle-license-v1-1 select true | sudo debconf-set-selections
echo debconf shared/accepted-oracle-license-v1-1 seen true | sudo debconf-set-selections
apt-get install -y oracle-java7-installer

SCRIPT

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.box = "precise64"

  # Run the bootsrap script on start.
  config.vm.provision "shell", inline: $bootstrap

  # Use ubuntu 12.04
  config.vm.box_url = "http://files.vagrantup.com/precise64.box"

  # Assign static IP to be able to use nfs option (if you have a conflict,
  # change it to something else).
  config.vm.network "private_network", ip: "192.168.50.4"

  # Use B2G_PATH environment variable to sync with vm's /home/vagrant/B2G
  # directory.
  config.vm.synced_folder ENV['B2G_PATH'], "/home/vagrant/B2G", nfs: true

  config.vm.provider "virtualbox" do |v|
    # Enable 4GB of RAM
    v.customize ["modifyvm", :id, "--memory", "4096"]
    # Enable usb
    v.customize ["modifyvm", :id, "--usb", "on"]
    # Filter the following devices: inari, keon, android
    v.customize ['usbfilter', 'add', '0', '--target', :id, '--name', 'inari', '--vendorid', '0x19d2']
    v.customize ['usbfilter', 'add', '0', '--target', :id, '--name', 'full_keon', '--vendorid', '0x05c6']
    v.customize ['usbfilter', 'add', '0', '--target', :id, '--name', 'android', '--vendorid', '0x18d1']
  end

end