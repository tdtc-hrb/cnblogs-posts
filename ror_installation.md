---
title: "安装Ruby On Rails"
date: 2025-01-08T08:08:08+08:00
---


- [Ruby & Rails Compatibility Table](https://www.fastruby.io/blog/ruby/rails/versions/compatibility-table.html)

|Rails Version|Required Ruby Version|Recommended Ruby Version|Required Rubygems Version|Status|
|-|-|-|-|-|
|8.1.Z|>= 3.2.0|3.4.Z|>= 1.8.11|Maintained|
|8.0.Z|>= 3.2.0|3.4.Z|>= 1.8.11|Maintained|
|7.2.Z|>= 3.1.0|3.4.Z|>= 1.8.11|Maintained|


# RbEnv
  Install ruby using rbenv.

## Dependencies

- Debian    
> v12
After enabling [sudo](https://tdtc-hrb.github.io/csdn/post/apt-oper-ubuntu/), 
the operation is the same as Ubuntu.

- Ubuntu    
  Ubuntu 20.04 default Ruby version is v2.7
```
sudo apt install git curl libssl-dev libreadline-dev zlib1g-dev autoconf bison build-essential \
 libyaml-dev libncurses5-dev libffi-dev libgdbm-dev
```


- RHEL    
  RHEL8(Alma / Rocky) default Ruby version is v2.5.5
```bash
sudo dnf -y install git make gcc curl openssl-devel zlib-devel libffi-devel readline-devel sqlite-devel
```

install tar:
```bash
sudo yum install tar
```

## install
first install RbEnv Dependencies;    
Later, upload rbenv-installer shell script.
```cmd
D:\programs\putty079>PSCP.EXE d:\rbenv-installer tdtc@192.168.3.99:/home/tdtc/
```
or
```
export http_proxy=http://tdtc:qaz1xsw2@192.168.3.128:8580/
export https_proxy=http://tdtc:qaz1xsw2@192.168.3.128:8580/
curl -fsSL https://github.com/rbenv/rbenv-installer/raw/HEAD/bin/rbenv-installer | bash
```

set path:
```bash
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
source ~/.bashrc
```

test:
```bash
$ rbenv -v
rbenv 1.3.2-16-gba96d7e
```

show list:
```bash
rbenv install -l
```

### installation
- online
```
rbenv install 3.4.8
```
- offline    
down:
```
curl -O https://cache.ruby-lang.org/pub/ruby/3.4/ruby-3.4.8.tar.gz
```
```
1. Move the downloaded file to `~/.rbenv/cache/`
2. Run `rbenv install x.y.z` (ex. rbenv install 3.4.8`)
```

### set and test
- set global
```bash
rbenv global 3.4.8
```
- test
```bash
$ ruby --version
ruby 3.4.8 (2025-12-17 revision 995b59f666) +PRISM [x86_64-linux]
```



# Rails
```bash
gem install rails
```
info(39 gems installed)
```
Fetching zeitwerk-2.7.4.gem
Fetching thor-1.5.0.gem
Fetching rackup-2.3.1.gem
Fetching rack-3.2.4.gem
Fetching concurrent-ruby-1.3.6.gem
Fetching tzinfo-2.0.6.gem
Fetching i18n-1.14.8.gem
Fetching connection_pool-3.0.2.gem
Fetching activesupport-8.1.1.gem
Fetching useragent-0.16.11.gem
Fetching nokogiri-1.19.0-x86_64-linux-gnu.gem
Fetching crass-1.0.6.gem
Fetching loofah-2.25.0.gem
Fetching rails-html-sanitizer-1.6.2.gem
Fetching rails-dom-testing-2.3.0.gem
Fetching rack-test-2.2.0.gem
Fetching rack-session-2.1.1.gem
Fetching erubi-1.13.1.gem
Fetching builder-3.3.0.gem
Fetching actionview-8.1.1.gem
Fetching actionpack-8.1.1.gem
Fetching railties-8.1.1.gem
Fetching marcel-1.1.0.gem
Fetching activemodel-8.1.1.gem
Fetching activerecord-8.1.1.gem
Fetching globalid-1.3.0.gem
Fetching activejob-8.1.1.gem
Fetching activestorage-8.1.1.gem
Fetching action_text-trix-2.1.16.gem
Fetching actiontext-8.1.1.gem
Fetching mini_mime-1.1.5.gem
Fetching mail-2.9.0.gem
Fetching rails-8.1.1.gem
Fetching actionmailer-8.1.1.gem
Fetching actionmailbox-8.1.1.gem
Fetching websocket-extensions-0.1.5.gem
Fetching websocket-driver-0.8.0.gem
Fetching nio4r-2.7.5.gem
Fetching actioncable-8.1.1.gem
```
test:
```bash
$ rails -v
Rails 8.1.1
```

## exec list
```
rails db:create
rails db:migrate
rake assets:clean
rake assets:clobber
rake tmp:clear
rake assets:precompile
./bin/dev
```

# Ref
- [Install Ruby 3 on RHEL 8|CentOS 8|Rocky Linux 8](https://computingforgeeks.com/install-ruby-on-rhel-centos-rocky-linux/)
- [How To Install Ruby on Rails with rbenv on Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-install-ruby-on-rails-with-rbenv-on-ubuntu-20-04)
- [Install ruby using rbenv's downloaded file](https://stackoverflow.com/a/62421065)
