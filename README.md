# About

Homework set: 29th May 2019 / Week 10 / Wednesday

Task: To make post page work development.local:3000/posts

This is a Chef Cookbook that provisions a local environment with Vagrant and Virtualbox.  Previously a similar task
was completed using a Shell script, however this is considered inefficient so the process was improved by configuring
the environment with a Chef Cookbook, the Chef Cookbook included a MongoDB and a node cookbook to meet dependency requirements.


# Steps I took to make it work

1. I made sure that the development.local:3000 was working as it should be at http://development.local:3000
2. Removed the DB folder and the synced_folder line
3. Added the following line of code to app.vm.provision "shell", inline: set_env({DB_HOST: "mongodb://192.168.10.150:27017/posts"}), privileged: false
4. Added the following method to the Vagrant file to set the environment variables.

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

5. If you now go to http://development.local:3000/posts  you should see the recent posts.
# Sparta-Packer-Production-Jenkins
