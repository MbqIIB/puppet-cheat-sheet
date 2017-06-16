# puppet-cheat-sheet

### Managing Puppet

Managing Puppet Services:
~~~~
service puppetserver start   # start puppet server service
chkconfig puppetserver on    # enable puppet server service on boot
service start puppet         # start puppet agent service
chkconfig puppet on          # enable puppet agent service on boot
~~~~

Managing Certificates (Master):
~~~~
puppet cert list           # lists available nodes to sign
puppet cert list --all     # lists all signed nodes
puppet cert sign <name>    # manually sign specific node
puppet cert sign --all     # sign all nodes
puppet cert clean <name>   # removes cert
~~~~

Managing Nodes (Master):
~~~~
puppet node clean <name>   # removes node + cert
~~~~

Managing Modules (Master):
~~~~
puppet module list              # lists current installed modules
puppet module install <name>    # downloads/installs modules from http://forge.puppetlabs.com
puppet module uninstall <name>  # removes/deletes module
puppet module upgrade <name>    # upgrades to new version of module
puppet module search <name>     # search modules from http://forge.puppetlabs.com
~~~~

Managing Puppet Agent (Master & Node):
~~~~
puppet agent --test                            # run puppet agent on demand
puppet agent --disable                         # disabled puppet agent
puppet agent --enable                          # enable puppet agent
puppet agent --configprint config              # print location of puppet agent configuration file
puppet agent -t --noop                         # see what puppet is going to change without making the changes
puppet agent -t --noop /path/to/puppetcode.pp  # see what puppet is going to change for a paticular module
puppet agent --configprint runinterval         # check runtime interval
~~~~

### Configuring Puppet

Setup Auto Cert Sign on Puppet Master (Master):
~~~~
vi /etc/puppetlabs/puppet/autosign.conf
*.<DOMAIN>                             # your domain name "example.com"
~~~~

Changing Puppet Agent Run Interval (Master/Node):
~~~~
vi /etc/puppetlabs/puppet/puppet.conf
[agent]
runinterval = 1800                    # default is every 30minutes (1800 seconds)
~~~

Changing Puppet Agent Environment(Master/Node):
~~~~
vi /etc/puppetlabs/puppet/puppet.conf
[main]
environment = <ENVIRONMENT>           # default is "production"
~~~~

Changing Puppet Agent Default Puppet Master Server(Master/Node):
~~~~
vi /etc/puppetlabs/puppet/puppet.conf
[main]
server = <PUPPET_SERVER>              # default is "puppet"
~~~~

### Troubleshooting

Connection To The Puppet Master:
~~~~
ping IP                               # make sure puppet master is reachable via IP first
ping puppet                           # make sure short domain name can reach the puppet master
ping puppet.example.com               # makesure FQDN can reach the puppet master
vi /etc/hosts			                    # check that both FQDN / Short Domain name are entered on client side DNS
nslookup puppet.example.com           # if using DNS Server Side then check if you can reach the nameservers + name
vi /etc/resolv.conf                   # if using DNS Server Side check dns configuration is correct
service network restart               # restart connection check if any errors
vi /etc/puppetlabs/puppet/puppet.conf # if using a custom puppet server check config to see if configured correctly to non default server
telnet puppet.example.com 8140        # test connection to puppet server for port 8140
date -R                               # if time is out of sync, get it in sync with the puppet master
~~~~

SSL Regeneration:
~~~~
Run the following on the master:
puppet cert clean node.example.com            # clean node on master
Then, run the following on the agent node:
rm -rf $(puppet agent --configprint ssldir)   # Remove SSL Certificate
puppet agent --test                           # run puppet agent
~~~~
