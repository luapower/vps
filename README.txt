# luapower vps installation

needs 1GB RAM; ubuntu 14.04

## functionality

  * luapower.com website (openresty+luapower)
  * luapower.com forum (nodebb+redis)
  * compiler tools (static files)
  * luajit browsable sources (htags-generated static website)

## crontab

  * update/push luapower-all (each half hour, so that master.zip from the download button reflects the current state of the code)
  * release-tag luapower-all (every day, so that we can download old releases)
  * download github.com/luapower/luapower-all/archive/master.zip (every day, so that its reported size on the download button is correct)
  * pull official LuaJIT (every hour)
  * update the LuaJIT htags (every day)

## prerequisites

### update/install packages

	apt-get update
	apt-get install apt-mirror build-essential cscope dos2unix dpkg-dev fcgiwrap git git-core global htop imagemagick lib32bz2-1.0 lib32ncurses5 lib32z1 libncurses5-dev libpcre3-dev libreadline-dev libssl-dev make mc nodejs nodejs-legacy npm pandoc python-software-properties software-properties-common tig zip

### add a new user and login

	adduser cosmin
	usermod -aG sudo cosmin
	echo "cosmin ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
	passwd -d cosmin
	su - cosmin

### enable pushing to github

	ssh-keygen -t rsa -C "cosmin.apreutesei@gmail.com"	
	gpto https://github.com/settings/keys and add a new key with the contents of ~/.ssh/id_rsa.pub


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
redis
	sudo apt-add-repository ppa:chris-lea/redis-server
	sudo apt-get update
	sudo apt-get install redis-server
nodebb
	mkdir nodebb
	cd nodebb
	mgit clone git@github.com:luapower/forum.git
	mgit clone-release current
	(get secret from safe)> .mgit/secret & config.json
	
openresty
	wget https://openresty.org/download/ngx_openresty-1.7.10.1.tar.gz
	tar xvfz ngx_openresty-1.7.10.1.tar.gz
	cd ngx_openresty-1.7.10.1
	./configure --prefix=/home/cosmin/openresty
	make
	make install
ssl-cert
	mkdir .ssl-cert
	cd .ssl-cert
	make file luapower.com.key with contents from safe
	make file luapower.com.crt with contents from globessl.com
	openssl dhparam -out dhparam.pem 4096 (this will take 15min!!!)
files
	cd files
	./get-all.sh
website
	git clone git@github.com:luapower/website.git
	cd website
	ln -s ../luapower luapower
	ln -s ../openresty openresty
	ln -s ../files files
	cd nginx/conf
	ln -s nginx.prod.conf nginx.conf
luajit-htags
	git clone git@github.com:capr/luajit-htags.git
	ln -s luajit-htags/htags files/htags
cron & boot
	mkdir logs
	crontab crontab
