sshForShits
===========

Framework for a high interaction SSH honeypot.  The provided "shell" is just a go routine with callbacks which handle command output.


The emulated commands are extremely limited right now, but we welcome more interactive replacements.
Checkout the commands.go file to add or modify some.


A web frontend specifically designed to show shits that got shells can be found at https://github.com/e1z0/sshForShitsGooey

A live instance is running at http://shellsforshits.com

Thanks remasis for rolling the GUI, because I have no sense of design...

Install
===========
Prepare mongodb database
```
use sshforshits
```
if mongodb version is 2.6 or higher:
```
db.createUser(
  {
    user: "sshforshits",
    pwd: "password",
    roles: [ { role: "readWrite", db: "sshforshits" } ]
  }
)
```
If the mongodb version is below 2.6
```
db.addUser( { user: "sshforshits",
              pwd: "password",
              roles: [ "readWrite", "dbAdmin" ]
            } )
```
Install from source
```
git clone https://github.com/e1z0/sshForShits
cd sshForShits
```

Renerate the ssh machine key
```
ssh-keygen -f ssh_host_rsa_key -N '' -t rsa
```

Compile:
```
go get ./
go build ./
mkdir log
```

Start using ssh port 2222
```
./sshForShits -e ./log/shellsForShits.log -k ssh_host_rsa_key -l ./log/attempts.log -p 2222 -pass mongodb_password -s 127.0.0.1:27017 -user mongodb_user
```

Here is the systemd service unit (sshForShits.service)
```
[Unit]
Description=sshForShits     
After=network-online.target
Wants=network-online.target

[Service]
Type=Simple
User=<username>
Group=<username>
WorkingDirectory=/home/<username>/sshForShits                 
ExecStart=/home/<username>/sshForShits/sshForShits -e ./log/shellsForShits.log -k ssh_host_rsa_key -l ./log/attempts.log -p 2222 -pass mongodb_password -s 127.0.0.1:27017 -user mongodb_user
Restart=on-failure

[Install]
WantedBy=multi-user.target
```



Redirect to default ssh port 22 using iptables:
```
iptables -t nat -A PREROUTING -p tcp --dport 22 -j REDIRECT --to-port 2222
```
