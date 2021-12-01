# Install OHS and OAM Webgate
* Create new OHS instance
  * First stop node manager by navigate to $DOMAIN_HOME/bin and execute following command
  ```
  ./stopNodeManager.sh
  ```
  * then invoke wlst offline and execute follwing commands
  ```
  /u01/oracle/Middleware/Oracle_Home/oracle_common/common/bin/wlst.sh
  wls:/offline> readDomain("/u01/oracle/Middleware/Oracle_Home/user_projects/domains/WebGate_OHS/")
  wls:/offline/WebGate_OHS>cd("/")
  wls:/offline/WebGate_OHS>create('ohs2', 'SystemComponent')
  Proxy for ohs2: Name=ohs2, Type=SystemComponent
  wls:/offline/WebGate_OHS>cd('/SystemComponent/ohs2')
  wls:/offline/WebGate_OHS/SystemComponent/ohs2>cmo.setComponentType('OHS')
  wls:/offline/WebGate_OHS/SystemComponent/ohs2>set('Machine', 'localmachine')
  wls:/offline/WebGate_OHS/SystemComponent/ohs2>cd('/OHS/ohs2')
  wls:/offline/WebGate_OHS/OHS/ohs2>cmo.setAdminHost('127.0.0.1')
  wls:/offline/WebGate_OHS/OHS/ohs2>cmo.setAdminPort('9999')
  wls:/offline/WebGate_OHS/OHS/ohs2>cmo.setListenAddress('zywg01s')
  wls:/offline/WebGate_OHS/OHS/ohs2>cmo.setListenPort('8090')
  wls:/offline/WebGate_OHS/OHS/ohs2>cmo.setSSLListenPort('8091')
  wls:/offline/WebGate_OHS/OHS/ohs2>cmo.setServerName('http://zywg01s:8090')
  wls:/offline/WebGate_OHS/OHS/ohs2>updateDomain()
  wls:/offline/WebGate_OHS/OHS/ohs2>exit()
  ```
  Admin Host: The listen address to use for the selected OHS server for communication with Node Manager. The address should only allow loopback communication within the host (for example, 127.0.0.1).
  Admin Port: The listen port to use for the selected OHS server for communication with Node Manager on this system. The port must be unique.
  * Start node manager using following command
  ```
  nohup ./startNodeManager.sh >../nodemanager/nodemanager.log 2>&1 &
  ```
  * Start components
  ```
  [oracle@zywg01s bin]$ ./startComponent.sh ohs1
  Starting system Component ohs1 ...

  Initializing WebLogic Scripting Tool (WLST) ...

  Welcome to WebLogic Server Administration Scripting Shell

  Type help() for help on available commands

  Reading domain from /u01/oracle/Middleware/Oracle_Home/user_projects/domains/WebGate_OHS


  Please enter Node Manager password:
  Connecting to Node Manager ...
  Successfully Connected to Node Manager.
  Starting server ohs1 ...
  Successfully started server ohs1 ...
  Successfully disconnected from Node Manager.


  Exiting WebLogic Scripting Tool.

  Done
  [oracle@zywg01s bin]$ ./startComponent.sh ohs2
  Starting system Component ohs2 ...

  Initializing WebLogic Scripting Tool (WLST) ...

  Welcome to WebLogic Server Administration Scripting Shell

  Type help() for help on available commands

  Reading domain from /u01/oracle/Middleware/Oracle_Home/user_projects/domains/WebGate_OHS


  Please enter Node Manager password:
  Connecting to Node Manager ...
  Successfully Connected to Node Manager.
  Starting server ohs2 ...
  Successfully started server ohs2 ...
  Successfully disconnected from Node Manager.


  Exiting WebLogic Scripting Tool.

  Done
  ```
  
  
* Install Webgate on OHS instance

  ```
  [oracle@zywg01s bin]$ cd /u01/oracle/Middleware/Oracle_Home/webgate/ohs/tools/deployWebGate/
  [oracle@zywg01s deployWebGate]$ ./deployWebGateInstance.sh -w /u01/oracle/Middleware/Oracle_Home/user_projects/domains/WebGate_OHS/config/fmwconfig/components
  /OHS/ohs2 -oh /u01/oracle/Middleware/Oracle_Home
  Copying files from WebGate Oracle Home to WebGate Instancedir
  
  [oracle@zywg01s deployWebGate]$ export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/u01/oracle/Middleware/Oracle_Home/lib/
  [oracle@zywg01s deployWebGate]$ cd /u01/oracle/Middleware/Oracle_Home/webgate/ohs/tools/setup/InstallTools
  
  [oracle@zywg01s InstallTools]$ ./EditHttpConf -w /u01/oracle/Middleware/Oracle_Home/user_projects/domains/WebGate_OHS/config/fmwconfig/components/OHS/ohs2 -oh /u01/oracle/Middleware/Oracle_Home  -o webgate.conf
  The web server configuration file was successfully updated
  /u01/oracle/Middleware/Oracle_Home/user_projects/domains/WebGate_OHS/config/fmwconfig/components/OHS/ohs2/httpd.conf has been backed up as /u01/oracle/Middleware/Oracle_Home/user_projects/domains/WebGate_OHS/config/fmwconfig/components/OHS/ohs2/httpd.conf.ORIG
  ```
  
* Create staff and int instances
 ```
 sudo firewall-cmd --zone=public --list-ports
 sudo firewall-cmd --permanent --zone=public --add-port=8100-8101/tcp
 sudo firewall-cmd --permanent --zone=public --add-port=8200-8201/tcp
 sudo firewall-cmd --reload
 sudo firewall-cmd --zone=public --list-ports
 5901/tcp 8080/tcp 8090/tcp 8100-8101/tcp 8200-8201/tcp
 
 
 wls:/offline> readDomain("/u01/oracle/Middleware/Oracle_Home/user_projects/domains/WebGate_OHS/")
 wls:/offline/WebGate_OHS>cd("/")
 wls:/offline/WebGate_OHS>create("ohs_staff", 'SystemComponent')
 Proxy for ohs_staff: Name=ohs_staff, Type=SystemComponent
 wls:/offline/WebGate_OHS>cd('/SystemComponent/ohs_staff')
 wls:/offline/WebGate_OHS/SystemComponent/ohs_staff>cmo.setComponentType('OHS')
 wls:/offline/WebGate_OHS/SystemComponent/ohs_staff>set('Machine', 'localmachine')
 wls:/offline/WebGate_OHS/SystemComponent/ohs_staff>cd('/OHS/ohs_staff')
 wls:/offline/WebGate_OHS/OHS/ohs_staff>cmo.setAdminHost('127.0.0.1')
 wls:/offline/WebGate_OHS/OHS/ohs_staff>cmo.setAdminPort('9997')
 wls:/offline/WebGate_OHS/OHS/ohs_staff>cmo.setListenAddress('zywg01s.test.oci.tafenswtest.edu.au')
 wls:/offline/WebGate_OHS/OHS/ohs_staff>cmo.setListenPort('8100')
 wls:/offline/WebGate_OHS/OHS/ohs_staff>cmo.setSSLListenPort('8101')
 wls:/offline/WebGate_OHS/OHS/ohs_staff>cmo.setServerName('http://zywg01s.test.oci.tafenswtest.edu.au:8070')
 wls:/offline/WebGate_OHS/OHS/ohs_staff>cd("/")
 wls:/offline/WebGate_OHS>create("ohs_int", 'SystemComponent')
 Proxy for ohs_int: Name=ohs_int, Type=SystemComponent
 wls:/offline/WebGate_OHS>cd('/SystemComponent/ohs_int')
 wls:/offline/WebGate_OHS/SystemComponent/ohs_int>cmo.setComponentType('OHS')
 wls:/offline/WebGate_OHS/SystemComponent/ohs_int>set('Machine', 'localmachine')
 wls:/offline/WebGate_OHS/SystemComponent/ohs_int>cd('/OHS/ohs_int')
 wls:/offline/WebGate_OHS/OHS/ohs_int>cmo.setAdminHost('127.0.0.1')
 wls:/offline/WebGate_OHS/OHS/ohs_int>cmo.setAdminPort('9996')
 wls:/offline/WebGate_OHS/OHS/ohs_int>cmo.setListenAddress('zywg01s.test.oci.tafenswtest.edu.au')
 wls:/offline/WebGate_OHS/OHS/ohs_int>cmo.setListenPort('8200')
 wls:/offline/WebGate_OHS/OHS/ohs_int>cmo.setSSLListenPort('8201')
 wls:/offline/WebGate_OHS/OHS/ohs_int>cmo.setServerName('http://zywg01s.test.oci.tafenswtest.edu.au:8060')
 wls:/offline/WebGate_OHS/OHS/ohs_int>updateDomain()
 wls:/offline/WebGate_OHS/OHS/ohs_int>exit()
 ```
* 
