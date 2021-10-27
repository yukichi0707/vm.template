# -*- mode: ruby -*-
# vi: set ft=ruby :

require "fileutils"

#=======================================================
# 設定情報読み込み
#=======================================================
# ユーザ設定ファイル読み込み
path = __dir__ + '/' + 'user_env'
if !File.file?(path)
  default_path = __dir__ + '/' + 'user_env.default'
  FileUtils.cp(default_path, path)
  puts 'created a file.(' + path + ')'
end
load path
#puts '#======================================================='
#puts '# ユーザ設定'
#puts '#======================================================='
#puts $user_env
#puts ''


# vagrant設定ファイル読み込み
path = __dir__ + '/' + 'vagrant_config'
if !File.file?(path)
  default_path = __dir__ + '/' + 'vagrant_config.default'
  FileUtils.cp(default_path, path)
  puts 'created a file.(' + path + ')'
end
load path
#puts '#======================================================='
#puts '# vagrant設定情報'
#puts '#======================================================='
#puts $vagrant_config
#puts ''


# 設定情報をマージ
user_proxy = $user_env[:proxy]
user_single_vm = $user_env[:vagrant_config][:vm][0]
single_vm = $vagrant_config[:vm][0]
single_vm[:provision] = [].concat(user_single_vm[:provision]).concat(single_vm[:provision])


#=======================================================
# vagrant setup
#=======================================================
Vagrant.configure("2") do |config|
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "CentOS7_x8664_devenv"
  config.vm.box_url = "file://./boxes/CentOS7_x8664_devenv.box"

  # VirtualBoxスペック設定
  config.vm.provider "virtualbox" do |pv|
    spv = single_vm[:provider]

    pv.name = spv[:name] if spv.key?(:name)
    pv.gui = spv[:gui] if spv.key?(:gui)
    pv.default_nic_type = spv[:default_nic_type] if spv.key?(:default_nic_type)
    pv.linked_clone = spv[:linked_clone] if spv.key?(:linked_clone)
    pv.memory = spv[:memory] if spv.key?(:memory)
    pv.cpus = spv[:cpus] if spv.key?(:cpus)
    pv.customize = spv[:customize] if spv.key?(:customize)
  end

  # 共有フォルダ
  #
  # vagrantディレクトリ
  config.vm.synced_folder ".",
    "/home/vagrant/vagrant", 
    owner: "vagrant",
    group: "vagrant",
    mount_options: ['dmode=777', 'fmode=777']
  # ホスト-ゲスト間の共有フォルダ
  config.vm.synced_folder "./share",
    "/home/vagrant/share", 
    owner: "vagrant",
    group: "vagrant",
    mount_options: ['dmode=777', 'fmode=777']

  # ポートの自動割り当て可能範囲の設定
  config.vm.usable_port_range = single_vm[:usable_port_range] if single_vm.key?(:usable_port_range)
  # network
  single_vm[:network].each do | nw |
    case nw[:network_type]
    when 'forwarded_port' then
      config.vm.network "forwarded_port",
        auto_correct: nw.key?(:guest) ? nw[:auto_correct] : false,
        guest: nw[:guest],
        guest_ip: nw.key?(:guest_ip) ? nw[:guest_ip] : nil,
        host: nw[:host],
        host_ip: nw.key?(:host_ip) ? nw[:host_ip] : nil,
        protocol: nw.key?(:protocol) ? nw[:protocol] : 'tcp',
        id: nw.key?(:id) ? nw[:id] : nil

    when 'private_network' then
      if nw.key?(:ip) && nw.key?(:netmask)
        config.vm.network "private_network",
          ip: nw[:ip],
          netmask: nw[:netmask],
          auto_config: nw.key?(:auto_config) ? nw[:auto_config] : true
      elsif nw.key?(:ip)
        config.vm.network "private_network",
          ip: nw[:ip],
          auto_config: nw.key?(:auto_config) ? nw[:auto_config] : true
      else
        # dhcp
        config.vm.network "private_network",
          type: nw.key?(:type) || 'dhcp'
      end

    when 'public_network' then
      if nw.key?(:ip)
        config.vm.network "public_network",
          ip: nw[:ip]
      elsif nw.key?(:bridge)
        config.vm.network "public_network",
          bridge: nw[:bridge]
      elsif nw.key?(:auto_config)
        config.vm.network "public_network",
          auto_config: nw[:auto_config]
      else
        # dhcp
        config.vm.network "public_network",
          use_dhcp_assigned_default_route: nw.key?(:use_dhcp_assigned_default_route) ? nw[:use_dhcp_assigned_default_route] : false
      end
    end
  end

  # SSH設定
  config.ssh.insert_key = false
  config.ssh.username = "vagrant"
  config.ssh.host = '127.0.0.1'
  config.ssh.guest_port = 22
  config.ssh.private_key_path = "./ssh/insecure_private_key"

  # プロクシ設定
  proxy = user_proxy
  if Vagrant.has_plugin?("vagrant-proxyconf") && proxy[:use]
    http_proxy = "http://#{proxy[:user]}:#{proxy[:pass]}@#{proxy[:host]}:#{proxy[:port]}"
    config.proxy.http = http_proxy
    config.proxy.https = http_proxy
    config.proxy.no_proxy = "localhost,127.0.0.1"
  end

  # VirtualBox GuestAdditionsの自動アップデート設定
  if Vagrant.has_plugin?("vagrant-vbguest")
    config.vbguest.auto_update = true
  end

  # SHELL
  single_vm[:provision].each do | pv |
    case pv[:type]
    when 'shell' then
      if pv.key?(:inline) then
        config.vm.provision pv[:type],
          privileged: pv.key?(:privileged) ? pv[:privileged] : true,
          inline: pv[:inline],
          reboot: false
      else
        puts "Sorry! `inline` only support!!"
      end
    else
      puts "Sorry! provision_type `#{pv[:type]}` is not supported!!"
    end
  end
end
