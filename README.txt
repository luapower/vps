# luapower vps installation

needs 1GB RAM; ubuntu 14.04


## functionality

  * luapower-related
    * luapower.com website (openresty+luapower)
    * luapower.com forum (nodebb+redis)
    * mirror for compiler tools (static files)
    * apt mirror for ubuntu's "test toolchain" PPA
    * git mirror for luapower repos (git-mirror; currently empty)
    * luajit browsable sources (htags-generated static website)
  * personal stuff
    * mokingburd.de (static website)
    * capr.github.io (static website)
    * luapower.com/otr (static website)
    * ifthen-dojo.io (openresty+luapower+webb)

## crontab

  * luapower source code
    * pull all packages (hourly, so that docs are updated and for code search; we have github hooks on all repos that trigger pulling, so this is just a safety measure)
  * luapower bundle
    * update/push luapower-all (each half hour, so that master.zip from the download button reflects the current state of the code)
    * release-tag luapower-all (daily, so that we can download old releases)
    * download github.com/luapower/luapower-all/archive/master.zip (every day, so that its reported size on the download button reflects the actual size of the archive)
  * luajit htags
    * pull official LuaJIT for htags (hourly)
    * update the LuaJIT htags (daily)
  * forum
    * backup the redis db and any new files (plugins, customization, etc.) to the `forum` repo (daily)


## prerequisites

### update/install packages

	apt-get update
	apt-get install apt-mirror build-essential cscope dos2unix dpkg-dev fcgiwrap git git-core global htop imagemagick lib32bz2-1.0 lib32ncurses5 lib32z1 libncurses5-dev libpcre3-dev libreadline-dev libssl-dev make mc nodejs nodejs-legacy npm pandoc python-software-properties software-properties-common tig zip curl mysql-server php5-common php5-cli php5-fpm php5-mysql
	
### install git-lfs

	curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.deb.sh | sudo bash
	sudo apt-get install git-lfs

### add a new user and login

	adduser cosmin
	usermod -aG sudo cosmin
	echo "cosmin ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
	passwd -d cosmin
	su - cosmin

### enable pushing to github

	ssh-keygen -t rsa -C "cosmin.apreutesei@gmail.com"	
	*** goto https://github.com/settings/keys and add a new key with the contents of ~/.ssh/id_rsa.pub

## enable logging in without a password

	*** import contents of .ssh/id_rsa into puttygen and make a putty ppk file to use with putty
	cp .ssh/id_rsa.pub .ssh/authorized_keys
	cp .ssh/id_rsa .ssh/id_rsa.pub .ssh/authorized_keys /root/.ssh/  # to login as root directly with the same key

## install

~
	git clone git@github.com:luapower/vps.git ~
	sudo ln -sf ~/git-up /usr/lib/git-core/git-up
mgit
	git clone git@github.com:capr/multigit.git
	sudo ln -sf ~/multigit/mgit /usr/local/bin/mgit
luapower
	mkdir luapower
	cd luapower
	mgit clone git@github.com:luapower/luapower-repos
	mgit clone-all
	*** generate luapower_db.lua by running ./luapower update-db on all platforms
luapower-all
	git clone git@github.com:luapower/luapower-all.git
	mv luapower-all/.git luapower/
	rm -R luapower-all
redis
	sudo apt-add-repository ppa:chris-lea/redis-server
	sudo apt-get update
	sudo apt-get install redis-server
nodebb
	mkdir nodebb
	cd nodebb
	mgit clone git@github.com:luapower/forum.git
	mgit clone-release fixed
	*** get secret from safe and put it into .mgit/secret and into config.json
	mgit restore   # restore the database
openresty
	wget https://openresty.org/download/ngx_openresty-1.7.10.1.tar.gz
	tar xvfz ngx_openresty-1.7.10.1.tar.gz
	cd ngx_openresty-1.7.10.1
	./configure --prefix=/home/cosmin/openresty
	make
	make install
ssl-cert
	mkdir ssl-cert
	cd ssl-cert
	*** make file luapower.com.key with contents from safe
	*** make file luapower.com.crt with contents from safe (paste both CRT and CA sections)
	*** make file ifthen-dojo.io.key with contents from safe
	*** make file ifthen-dojo.io.crt with contents from safe (paste both CRT and CA sections)
	openssl dhparam -out dhparam.pem 4096 (note: this will take 15min to complete!)
files
	cd files
	./get-all.sh   # actually get them from the mega backup
website
	git clone git@github.com:luapower/website.git luapower.com
	cd luapower.com
	ln -s ../luapower luapower
	ln -s ../openresty openresty
	ln -s ../files files
luajit-htags
	git clone git@github.com:capr/luajit-htags.git
	ln -s ../luajit-htags/htags files/htags
apt-mirror
	*** comment all lines in /etc/apt/mirror.list and enter these lines instead:
		deb-amd64 http://ppa.launchpad.net/ubuntu-toolchain-r/test/ubuntu lucid main
		deb-i386  http://ppa.launchpad.net/ubuntu-toolchain-r/test/ubuntu lucid main
cron & boot
	mkdir logs
	crontab crontab


## start the servers

	./nodebb/nodebb-start
	./luapower.com/ngx-start
	./ifthen-dojo.io/ngx-start
	./nginx/ngx-start


## personal stuff

mokingburd.de
	git clone git@github.com:capr/mokingburd.de.git  # nginx is already configured to serve it
capr.github.io
	git clone git@github.com:capr/capr.github.io.git  # nginx is already configured to serve it
luapower.com/otr
	git clone git@github.com:capr/otr.git  # nginx is already configured to serve it
ifthen-dojo (TODO)
	git clone git@github.com:capr/ifthen-dojo-website.git ifthen-dojo.io  # nginx is already configured to serve it

