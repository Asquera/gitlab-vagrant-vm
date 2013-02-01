Vagrant::Config.run do |config|
  config.vm.box = "gentoo_test"
  config.vm.box_url = "http://files.vagrantup.com/precise32.box"

  # vagrant dns config
  config.dns.tld = "dev"
  config.vm.host_name = "gitlab"
  config.dns.patterns = [/^.*gitlab.dev$/]

  config.vm.network :hostonly, '192.168.3.14'
  config.vm.customize ["modifyvm", :id, "--memory", 768]

  # Default user/group id for vagrant in precise32
  host_user_id  = 1000
  host_group_id = 1000
  
  config.vm.provision :chef_solo do |chef|
    chef.cookbooks_path = ['cookbooks', 'site-cookbooks']

    chef.add_recipe('rvm::vagrant')
    chef.add_recipe('rvm::user')

    chef.add_recipe('mysql::server')
    chef.add_recipe('mysql::ruby')

    chef.add_recipe('postgresql::server')
    chef.add_recipe('postgresql::ruby')

    chef.add_recipe('database::mysql')

    chef.add_recipe('phantomjs')

    # This is where all the magic happens.
    # see site-cookbooks/gitlab/
    chef.add_recipe('gitlab::vagrant')

    chef.json = {
      :rvm => {
        :user_installs => [
          { :user         => 'vagrant',
            :default_ruby => '1.9.3'
          }
        ],
        :vagrant => {
          :system_chef_solo => '/opt/vagrant_ruby/bin/chef-solo'
        },
        :global_gems => [{ :name => 'bundler'}]
      },
      :openssh => {
        :server => {
          :accept_env => ["GIT_*", "LANG", "LC_*"]
        }
      },
      :mysql => {
        :server_root_password => "nonrandompasswordsaregreattoo",
        :server_repl_password => "nonrandompasswordsaregreattoo",
        :server_debian_password => "nonrandompasswordsaregreattoo"
      },
      :gitlab => {
        :host_user_id => host_user_id,
        :host_group_id => host_group_id
      }
    }
  end
end
