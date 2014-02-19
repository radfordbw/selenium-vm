chef_solo_cookbook_path = ["cookbooks", "site-cookbooks"]
CHEF_CLIENT_INSTALL = <<-EOF
#!/bin/sh
dpkg -l curl|grep -q ^ii || {
  apt-get update
  apt-get install -y curl
}

test -d /opt/chef || {
  echo "Installing chef-client via omnibus"
  curl -L -s https://www.opscode.com/chef/install.sh | bash
}
EOF

Vagrant::configure("2") do |config|
  config.vm.box = "opscode-ubuntu-12.04-i386"
  config.vm.box_url = "https://opscode-vm.s3.amazonaws.com/vagrant/opscode_ubuntu-12.04-i386_provisionerless.box"
  
  # Configure Selenium Grid
  config.vm.define :'selenium-grid' do |selenium_grid|
    selenium_grid.vm.network :private_network, ip: "192.168.1.99"
        selenium_grid.vm.hostname = "selenium.local.vm"
        selenium_grid.vm.provider :virtualbox do |vb|
          vb.customize [
                        "modifyvm", :id,
                        "--name", "selenium-grid",
                        "--memory", "512",
                        "--cpus", 1,
                       ]
        end
      selenium_grid.vm.provision :shell, :inline => CHEF_CLIENT_INSTALL

      selenium_grid.vm.provision :chef_solo do |chef_solo|
        chef_solo.cookbooks_path = chef_solo_cookbook_path
        chef_solo.add_recipe 'selenium-grid'
      end
  end
end