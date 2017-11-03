# Using System Clipboard with Vim on Vagrant VM

## On Host

Adding the following to VagrantFile to enable x11 forwarding:

    config.ssh.forward_x11 = true
	config.ssh.forward_agent = true

Use 'vagrant ssh -- -Y' when connecting to VM.

##ReadHat/CentOS VM specific:

Install vim with x11 support:

    sudo yum install vim-x11

Use `vimx`


References:
[1] http://vim.wikia.com/wiki/Accessing_the_system_clipboard
[2] https://gergap.wordpress.com/2015/07/31/using-the-system-clipboard-in-vim-over-an-ssh-session/
