
**this proj is based on links:**
 * https://www.youtube.com/watch?v=HXW4Cew6sRI
 * https://www.youtube.com/watch?v=qTQZZpb-m7Q

```
cgr-ocs :
    - freeswitch
    - cgrates
        * redis
        * mysql
```
## 1. Debian 11 Bullseye:

    vmware Debian [11 Bullseye] img https://www.osboxes.org/debian/
    1.1 sudo su
        ln -sf /usr/share/zoneinfo/Asia/Aden /etc/localtime
## 2. freeswitch installation:

    [https://developer.signalwire.com/freeswitch/FreeSWITCH-Explained/Installation/Linux/Debian_67240088/#about]


 ```console

        sudo su
        TOKEN=YOURSIGNALWIRETOKEN
        apt-get update && apt-get install -y gnupg2 wget lsb-release
        wget --http-user=signalwire --http-password=$TOKEN -O /usr/share/keyrings/signalwire-freeswitch-repo.gpg https://freeswitch.signalwire.com/repo/deb/debian-release/signalwire-freeswitch-repo.gpg
        echo "machine freeswitch.signalwire.com login signalwire password $TOKEN" > /etc/apt/auth.conf
        chmod 600 /etc/apt/auth.conf
        echo "deb [signed-by=/usr/share/keyrings/signalwire-freeswitch-repo.gpg] https://freeswitch.signalwire.com/repo/deb/debian-release/ `lsb_release -sc` main" > /etc/apt/sources.list.d/freeswitch.list
        echo "deb-src [signed-by=/usr/share/keyrings/signalwire-freeswitch-repo.gpg] https://freeswitch.signalwire.com/repo/deb/debian-release/ `lsb_release -sc` main" >> /etc/apt/sources.list.d/freeswitch.list

        apt-get update  
        apt-get install -y freeswitch-meta-all

        systemctl status freeswitch

 ```
## 3. CGrateS Installation:
https://cgrates.readthedocs.io/en/latest/installation.html

     wget https://apt.cgrates.org/apt.cgrates.org.gpg key -O /etc/apt/trusted.gpg.d/apt.cgrates.org.asc
    echo "deb http://apt.cgrates.org/debian/ nightly main" >> /etc/apt/sources.list.d/cgrates.list
    sudo apt-get update
    sudo apt-get install cgrates

manual installation:

    wget http://pkg.cgrates.org/deb/nightly/cgrates_current_amd64.deb
    dpkg -i cgrates_current_amd64.deb

## 4. redis db Installation:
  Can be used as DataDB. Optimized for real-time information access. Once installed there should be no special requirements in terms of setup since no schema is necessary.

    curl -fsSL https://packages.redis.io/gpg | sudo gpg --dearmor -o /usr/share/keyrings/redis-archive-keyring.gpg

    echo "deb [signed-by=/usr/share/keyrings/redis-archive-keyring.gpg] https://packages.redis.io/deb $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/redis.list

    sudo apt-get update
    sudo apt-get install redis-server

## 5. mysql Installation:
Can be used as StorDB. Optimized for CDR archiving and offline Tariff Plan versioning.
link https://linuxize.com/post/how-to-install-mysql-on-debian-10/


    wget http://repo.mysql.com/mysql-apt-config_0.8.13-1_all.deb
    sudo apt install ./mysql-apt-config_0.8.13-1_all.deb
    # follow the link above
    sudo apt update
    sudo apt install mysql-server
if failed, try ```wget http://repo.mysql.com/mysql-apt-config_0.8.20-1_all.deb``` or ```wget http://repo.mysql.com/mysql-apt-config_0.8.22-1_all.deb```.

you'll be prompted to set MYSQL_ROOT_PASSWORD, set it to *CGRateS.org*

<br>
    


#  6. Configuration & run:
freeswitch default config based on /etc/freeswitch and we need to change it. 1st stop the service
    `systemctl stop freeswitch` and stop cgrates service `systemctl stop cgrates`.
now go to the path */usr/share/cgrates/tutorials/fs_evsock*.
```
cd /usr/share/cgrates/tutorials/fs_evsock
cd freeswitch/etc/freeswitch
vi vars.xml
  <X-PRE-PROCESS cmd="set" data="domain=$${local_ip_v4}"/>
vi sip_profiles/internal.xml
    <param name="rtp-ip" value="$${local_ip_v4}"/>
    <param name="sip-ip" value="$${local_ip_v4}"/>
    <param name="presence-hosts" value="$${domain},$${local_ip_v4}"/>

cd /usr/share/cgrates/tutorials/fs_evsock/freeswitch/etc/init.d
./freeswitch start
./freeswitch status

root@cgrates:~/fs_evsock/freeswitch# fs_cli -x 'sofia status'
                     Name          Type                                       Data      State
=================================================================================================
          192.168.229.135         alias                                   internal      ALIASED
                  cgrtest       profile               sip:mod_sofia@127.0.0.1:5080      RUNNING (0)
                 internal       profile         sip:mod_sofia@192.168.229.135:5060      RUNNING (0)
=================================================================================================
2 profiles 1 alias

cd /usr/share/cgrates/storage/mysql/
root@cgrates:/usr/share/cgrates/storage/mysql# ./setup_cgr_db.sh root CGRateS.org

cgr-migrator -exec=*set_versions -config_path=/usr/share/cgrates/tutorials/fs_evsock/cgrates/etc/cgrates/
cd /usr/share/cgrates/tutorials/fs_evsock/cgrates/etc/init.d/
root@cgrates:/usr/share/cgrates/tutorials/fs_evsock/cgrates/etc/init.d# ./cgrates start
root@cgrates:/usr/share/cgrates/tutorials/fs_evsock/cgrates/etc/init.d# ./cgrates status
cgrates is running.
root@cgrates:/usr/share/cgrates/tutorials/fs_evsock/cgrates/etc/init.d# cgr-console status
{
 "ActiveGoroutines": 52,
 "GoVersion": "go1.18",
 "MemoryUsage": "8.4MiB",
 "NodeID": "CGRFreeswitch",
 "RunningSince": "Sun Feb 19 20:48:53 +03 2023",
 "Version": "CGRateS@v0.11.0~dev-20221130185305-258492fcf3a9"
}
root@cgrates:/usr/share/cgrates/tutorials/fs_evsock/cgrates/etc/init.d#

```


cd /usr/share/cgrates/data/tariffplan/prepaid1centpsec/ && cgr-loader
cd /etc/cgrates/ && vi cgrates.cfg
systemctl restart cgrates
tail -f /var/log/syslog
