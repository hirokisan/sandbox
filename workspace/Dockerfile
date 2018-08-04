# Latest centos
FROM centos:7

#### Enable systemd ####
ENV container sandbox
RUN (cd /lib/systemd/system/sysinit.target.wants/; for i in *; do [ $i == \
systemd-tmpfiles-setup.service ] || rm -f $i; done); \
rm -f /lib/systemd/system/multi-user.target.wants/*;\
rm -f /etc/systemd/system/*.wants/*;\
rm -f /lib/systemd/system/local-fs.target.wants/*; \
rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
rm -f /lib/systemd/system/basic.target.wants/*;\
rm -f /lib/systemd/system/anaconda.target.wants/*;
VOLUME [ "/sys/fs/cgroup" ]
CMD ["/sbin/init"]

########################

#### Install tools ####

# System update
RUN yum -y update

# Install devtools
RUN yum install -y vim zsh git curl wget deltarpm rubygems

# Install build essential tools
RUN yum install -y gcc gcc-c++ kernel-devel make pcre pcre-devel

# Install other tools
RUN yum install -y automake libevent libevent-devel ncurses ncurses-devel

# Install sudo
RUN yum install -y sudo

# Install firewalld (if install firewalld and want to use git or connect to network, need to modify configure)
#RUN yum -y install firewalld

# Install NetworkManager
#RUN yum -y install NetworkManager

# Install sshd
RUN yum -y install openssh-server

# Clean cache
RUN yum clean all

#######################

#### Add user ####

# Add $USER
ENV user sandbox
RUN useradd --create-home --home-dir /home/$user --shell /usr/bin/zsh $user
## change password
RUN echo "$user:$user" | chpasswd
## use sudo command without password
RUN echo "$user ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

##################

#### Enable SSH ####

USER $user
## copy key
RUN mkdir -m 0700 -p ~/.ssh
COPY ./id_rsa     /home/$user/.ssh/id_rsa
COPY ./id_rsa.pub /home/$user/.ssh/authorized_keys
### avoid fingerprint yes or no
#COPY ./known_hosts /home/$user/.ssh/known_hosts
RUN sudo chown -R $user:$user ~/.ssh
RUN chmod -R 0700 ~/.ssh
RUN chmod -R 0600 ~/.ssh/*
## change config file
USER root
RUN sed -i 's/UsePAM yes/UsePAM no/g' /etc/ssh/sshd_config
RUN sed -i 's/PasswordAuthentication yes/PasswordAuthentication no/g' /etc/ssh/sshd_config
## avoid slow ssh https://www.serverpronto.com/accounts/knowledgebase.php?action=displayarticle&id=16&language=german
RUN sed -i 's/#UseDNS yes/UseDNS no/g' /etc/ssh/sshd_config

## Expose ports
EXPOSE 22

####################

#### Install dotfiles ####

#USER $user
#RUN git clone -b sandbox-use https://github.com/hirokisan/dotfiles.git ~/dotfiles
#USER root

##########################

#### Set up for php ####

USER $user
RUN sudo yum -y install epel-release
RUN sudo rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
USER root

########################

#### Install php7 ####

USER $user
RUN sudo yum -y install php --enablerepo=remi-php70
RUN sudo yum -y install php-pecl-redis --enablerepo=remi-php70
USER root

######################

#### Install redis ####

USER $user
RUN sudo yum -y install redis
USER root

#######################

#### Install Composer ####

USER $user
RUN mkdir -p /home/$user/local/bin/
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/home/$user/local/bin --filename=composer
USER root

##########################

#### Install MariaDB ####

USER $user
RUN sudo yum -y install mariadb mariadb-server
USER root

##########################

#### Install npm ####

RUN yum install -y epel-release
RUN yum install -y nodejs
RUN yum install -y npm --enablerepo=epel

#####################

#### Install nodebrew ####

USER $user
ENV PATH $PATH:/home/$user/.nodebrew/current/bin:$PATH
RUN curl -L git.io/nodebrew | perl - setup
RUN nodebrew install v8.9.4
RUN nodebrew use v8.9.4
RUN echo "export PATH=$HOME/.nodebrew/current/bin:$PATH" >> ~/.zshrc
USER root

##########################

#### Install yarn ####

RUN npm install -g yarn
ENV PATH "$PATH:`yarn global bin`"

########################

#### Systemctl ####
# Basically Docker have concept that each process has each container, but this is sandbox, so we use 1 container having some process
USER $user
COPY ./init.sh /home/$user/init.sh
RUN sudo chmod 755 /home/$user/init.sh
USER root

###################
