####################################################
# Basis-Image
####################################################
FROM ubuntu:20.04

####################################################
# Argumente setzen
####################################################
ARG SHELL_USER_SSHKEY
ARG PHP_VERSION
ARG GIT_EMAIL
ARG GIT_USER

# Interaktiv machen, denn manche APT-Pakete wollen interagieren. Wir nicht.
ARG DEBIAN_FRONTEND=noninteractive

####################################################
# SSH Key hinzufügen
####################################################
RUN mkdir -p /root/.ssh
RUN chmod 0700 /root/.ssh
RUN echo $SHELL_USER_SSHKEY > /root/.ssh/authorized_keys
RUN chmod 600 /root/.ssh/authorized_keys


####################################################
# APT Pakete installieren. Dazu gibt es eine Best-Practise:
# https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#run
####################################################
RUN apt-get update && apt-get install -y \
	curl \
	htop \
	openssh-server \
	mc \
	software-properties-common \
	git 
	


####################################################
# Alles auf Deutsch setzen
####################################################
RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y locales \
    && sed -i -e 's/# de_DE.UTF-8 UTF-8/de_DE.UTF-8 UTF-8/' /etc/locale.gen \
    && dpkg-reconfigure --frontend=noninteractive locales \
    && update-locale LANG=de_DE.UTF-8
ENV LANG de_DE.UTF-8 
ENV LC_ALL de_DE.UTF-8


####################################################
# Andere APT Repos/Pakete/Installationen
####################################################

# Yarn
RUN curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
RUN echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list

# NodeJS
RUN curl -sL https://deb.nodesource.com/setup_12.x | bash -

# PHP
RUN add-apt-repository ppa:ondrej/php -y

# Git
RUN git config --global user.email "${GIT_EMAIL}"
RUN git config --global user.name "${GIT_USER}"

####################################################
# Zusätzliche Pakete installieren
####################################################
RUN apt-get update && apt-get install -y \
	nodejs \
	yarn \
	php$PHP_VERSION \
	php$PHP_VERSION-mysql \
	php$PHP_VERSION-xml \
	php$PHP_VERSION-sqlite \
	php$PHP_VERSION-mbstring



####################################################
# Verschiedenes 
####################################################

# SSH Daemon konfigurieren
RUN mkdir /var/run/sshd
COPY ./sshd_config /etc/ssh/sshd_config

# Composer installieren
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
# RUN php -r "if (hash_file('sha384', 'composer-setup.php') === '756890a4488ce9024fc62c56153228907f1545c228516cbf63f885e036d37e9a59d27d63f46af1d4d07ee0f76181c7d3') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
RUN php composer-setup.php
RUN php -r "unlink('composer-setup.php');"
RUN mv composer.phar /usr/local/bin/composer

# Symfony CLI installieren
RUN wget https://get.symfony.com/cli/installer -O - | bash
RUN mv /root/.symfony/bin/symfony /usr/local/bin/symfony

# Port freigeben
EXPOSE 22

# Den SSH Daemon starten
CMD    ["/usr/sbin/sshd", "-D", "-e"]

