A simple rc.d script for running multiple [Rails](http://rubyonrails.org) apps using [unicorn](http://unicorn.bogomips.org) and [bundler](http://bundler.io) on FreeBSD.

#Features

* Run the rails apps as unprivileged system users
* Run multiple rails apps using one rc.d script
* The rails app is run using `bundle exec unicorn`

#Quick setup

Copy the `unicorn` file to `/usr/local/etc/rc.d` and mark it as executable using `chmod a+x unicorn`.

Edit your `rc.conf`. In this example, we'll add a Redmine and a GitLab installation:

	unicorn_enable="YES"
	unicorn_children="redmine gitlab"

	#redmine
	unicorn_redmine_enable="YES"
	unicorn_redmine_directory="/var/www/redmine/redmine-2.5.2"
	unicorn_redmine_config="/var/www/redmine/unicorn.rb"
	unicorn_redmine_user="redmine"
	
	#gitlab
	unicorn_gitlab_enable="YES"
	unicorn_redmine_directory="/var/www/gitlab"
	unicorn_redmine_config="/var/www/gitlab/config/unicorn.rb"
	unicorn_redmine_user="gitlab"
	

#Defaults

The following variables are inferred by default.

	unicorn_<child>_group=unicorn_redmine_user
	unicorn_<child>_pidfile=unicorn_<child>_directory/tmp/pids/unicorn.pid
	unicorn_<child>_env="production" #unicorn -E production
	
	


#Debugging / Developing

##Debug messages

Enable `rc_debug` to get more information on which variable-value-assignments.

##function _set_child_var

This function sets global variables for a child specified in `_child`. Usage as follows:

	_set_child_var 'global_variable_name' 'child_variable' 'yes/no' 'alternative'
	
###Example

	_child="redmine"
	_set_child_var "unicorn_group" 'group' 'no' "${unicorn_user}"
	# => unicorn_group=$unicorn_redmine_group if specified
	# OR unicorn_group=${unicorn_user}

