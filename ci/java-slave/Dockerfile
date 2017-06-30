FROM ubuntu:latest

RUN apt-get -qq update \
     && DEBIAN_FRONTEND=noninteractive apt-get -qqy install --no-install-recommends \
      apt-utils

RUN apt-get -qq update \
     && DEBIAN_FRONTEND=noninteractive apt-get -qqy install --no-install-recommends \
      openssh-server \
      nano \
      wget \
      git \
      zip \
      unzip \
      python-pip \
      build-essential \
      libstdc++6 libgcc1 zlib1g libncurses5 \
      curl \
      nodejs \
      npm \
      cron \
      sshpass \
      uuid-runtime 

RUN mkdir /var/run/sshd

RUN useradd -m -p pwkbQPPgL7QXc -s /bin/bash jenkins

COPY files/start-services.sh /var/opt/start-services.sh
RUN chmod +x /var/opt/start-services.sh

# Allow jenkins to talk to docker host
RUN groupadd -g 999 docker && usermod -aG docker jenkins

# Install and Configure Ansible
## To be able to deploy ansible 2.1.x.x and docker-compose we need pip to be installed and configured.
RUN apt-get -qq update \
   && DEBIAN_FRONTEND=noninteractive apt-get -qqy install python-pip python-dev build-essential

RUN pip install --upgrade pip \
    && pip install -U setuptools \
    && pip install ansible --upgrade -q \
    && pip install boto \
    && pip install awscli

COPY files/ansible.cfg /etc/ansible/ansible.cfg
# END Ansible


# Install Docker-compose
RUN pip install docker-compose -q
# END Install Docker-compose


# Setup CRON jobs
# Add crontab file in the cron directory
ADD files/crontab /etc/cron.d/sfly-cron
# Set execution rights on the cron job and create the log file
RUN chmod 0644 /etc/cron.d/sfly-cron && touch /var/log/cron.log
# END Setup CRON jobs

# Install OpenJDK
RUN apt-get -qq update \
    && DEBIAN_FRONTEND=noninteractive apt-get -qqy install openjdk-8-jdk \
    && echo "export PATH=$PATH:/usr/lib/jvm/java-1.8.0-openjdk-amd64/bin" >> /etc/profile.d/default_path.sh
# END OpenJDK

# Install Maven
RUN cd /tmp &&\
   wget -nv http://www-eu.apache.org/dist/maven/maven-3/3.5.0/binaries/apache-maven-3.5.0-bin.tar.gz &&\
   cd /opt/ && tar -xzvf /tmp/apache-maven-3.5.0-bin.tar.gz
RUN update-alternatives --install /usr/bin/mvn maven /opt/apache-maven-3.5.0/bin/mvn 1001
# END Install Maven



# nodejs configuration
RUN ln -s `which nodejs` /usr/bin/node &&\
   npm cache clean -f &&\
   npm install -gsq n &&\
   n -q stable &&\
   ln -sf /usr/local/n/versions/node/$(n --stable)/bin/node /usr/bin/node &&\
   npm install -gsq npm &&\
   ln -sf /usr/local/lib/node_modules/npm/bin/npm-cli.js /usr/bin/npm

# END nodejs


USER jenkins

# Set Appropriate Environmental Variables
ENV GRADLE_HOME /usr/lib/gradle
ENV JAVA_HOME /usr/lib/jvm/java-8-openjdk-amd64

USER root

# Caches
VOLUME ["/root/.gradle/caches", "/usr/bin/app"]

EXPOSE 22

CMD ["/var/opt/start-services.sh"]
