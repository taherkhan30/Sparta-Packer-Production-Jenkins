
required_plugins = %w( vagrant-hostsupdater vagrant-berkshelf vagrant-omnibus)
required_plugins.each do |plugin|
  exec "vagrant plugin install #{plugin};vagrant #{ARGV.join(" ")}" unless Vagrant.has_plugin? plugin ||
ARGV[0] == 'plugin'
end

def set_env vars
  command = <<~HEREDOC
    echo "Setting Environment Variables"
    source ~/.bashrc
  HEREDOC
  vars.each do |key,value|
    command += <<~HEREDOC
      if [ -z "$#{key}" ]; then
        echo "export #{key}=#{value}" >> ~/.bashrc
      fi
    HEREDOC
  end
  return command
end


Vagrant.configure("2") do |config|
  config.vm.define "app" do |app|
    app.vm.box = "ubuntu/xenial64"
    app.vm.network "private_network", ip: "192.168.10.100"
    app.hostsupdater.aliases = ["development.local"]
    app.vm.provision "chef_solo" do |chef|
      chef.add_recipe "node::default"
      chef.version = '14.12.9'
    end
    app.vm.provision "shell", inline: set_env({DB_HOST: "mongodb://192.168.10.150:27017/posts"}), privileged: false
  end


config.vm.define "db" do |db|
  db.vm.box = "ubuntu/xenial64"
  db.vm.network "private_network", ip: "192.168.10.150"
  db.hostsupdater.aliases =["database.local"]
  db.vm.provision "chef_solo" do |chef|
    chef.add_recipe "mongo-server::default"
    chef.version = '14.12.9'
  end
 end
end
