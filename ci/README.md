# How to start the ci infrastrucutre

## Initial Setup
mkdir -p /opt/sfly
mkdir -p /opt/sfly/jenkins
mkdir -p /opt/sfly/jenkins-slave/java-slave/files/ssh
set jenkins pub.key to authorized_keys and known_hosts in /opt/sfly/jenkins-slave/java-slave/files/ssh
chown -R 1000:1000 /opt/sfly

## Run Jenkins Infrastrucutre
docker-compose up -d
checked java-slave0 is attached

## Login instructions

### Jenkins
http://hostname:8000

### Portianer
http://hostname:9000

### java-slave
ssh jenkins@hostname -p 10020
