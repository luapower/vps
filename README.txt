# luapower vps installation

needs 1GB RAM; ubuntu 14.04

## prerequisites:

make a user and login

apt-get update
apt-get install git build-essential make libssl-dev libreadline-dev libpcre3-dev libncurses5-dev imagemagick nodejs npm pandoc redis-server zip mc tig dos2unix

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
	mkdir -p files/htags
	ln -s ../../luajit-htags/dynasm.htags/HTML files/htags/dynasm
	ln -s ../../luajit-htags/luajit.htags/HTML files/htags/luajit
cron & boot
	mkdir ~/logs
	crontab crontab
