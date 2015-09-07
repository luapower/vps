# luapower vps installation

needs 1GB RAM; ubuntu 14.04

## prerequisites:

make a user and login

apt-get update
apt-get install apt-mirror build-essential cscope dos2unix dpkg-dev fcgiwrap git git-core global htop imagemagick lib32bz2-1.0 lib32ncurses5 lib32z1 libncurses5-dev libpcre3-dev libreadline-dev libssl-dev make mc nodejs nodejs-legacy npm pandoc python-software-properties software-properties-common tig zip

sudo ln -sf ~/git-up /usr/lib/git-core/git-up
gen/add github key

## install:

~
	git clone git@github.com:luapower/vps.git ~
mgit
	git clone git@github.com:capr/mgit.git
	sudo ln -sf ~/mgit/mgit /usr/loca/bin/mgit
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
	get openresty-1.7.10.1+
	./configure --prefix=/home/cosmin/openresty
	make
	make install
ssl-cert
	get luapower.com.key from safe
	get luapower.com.crt from globessl.com
	gen dhparm.pem
files
	mkdir files
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
	mkdir ~/logs
	crontab crontab
