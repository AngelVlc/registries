https://computingforgeeks.com/how-to-install-harbor-docker-image-registry-on-centos-debian-ubuntu/

vagrant plugin install vagrant-vbguest

STEPS

* Install docker
  - sudo apt update && sudo apt install -y curl vim
  - curl -fsSL https://get.docker.com -o get-docker.sh
  - sudo sh get-docker.sh
  - sudo usermod -aG docker vagrant

* Install docker-compose
  - sudo curl -L "https://github.com/docker/compose/releases/download/1.26.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
  - sudo chmod +x /usr/local/bin/docker-compose

* Install certifactes to allow https (https://goharbor.io/docs/2.0.0/install-config/configure-https/)
    - openssl genrsa -out ca.key 4096
    - openssl req -x509 -new -nodes -sha512 -days 3650 -subj "/C=CN/ST=Beijing/L=Beijing/O=example/OU=Personal/CN=yourdomain.com" -key ca.key -out ca.crt
    - openssl genrsa -out yourdomain.com.key 4096
    - openssl req -sha512 -new -subj "/C=CN/ST=Beijing/L=Beijing/O=example/OU=Personal/CN=yourdomain.com" -key yourdomain.com.key -out yourdomain.com.csr
    - cat > v3.ext <<-EOF
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names

[alt_names]
DNS.1=yourdomain.com
DNS.2=yourdomain
DNS.3=hostname
EOF
    - openssl x509 -req -sha512 -days 3650 -extfile v3.ext -CA ca.crt -CAkey ca.key -CAcreateserial -in yourdomain.com.csr -out yourdomain.com.crt
    - sudo mkdir /data
    - sudo mkdir /data/cert
    - sudo cp yourdomain.com.crt /
    - sudo cp yourdomain.com.key /
    - openssl x509 -inform PEM -in yourdomain.com.crt -out yourdomain.com.cert
    - sudo mkdir /etc/docker/certs.d
    - sudo mkdir /etc/docker/certs.d/yourdomain.com
    - sudo cp yourdomain.com.cert /etc/docker/certs.d/yourdomain.com/
    - sudo cp yourdomain.com.key /etc/docker/certs.d/yourdomain.com/
    - sudo cp ca.crt /etc/docker/certs.d/yourdomain.com/

* Install harbor
  - cp /vagrant/data/harbor-offline-installer-v2.0.2.tgz .
  - tar xvzf harbor-offline-installer*.tgz
  - cp /vagrant/harbor.yml harbor/harbor.yml
  - sudo harbor/install.sh

* Add yourdomain.com to etc/hosts


~/harbor/common/config/registry/config.yml:
proxy:
  remoteurl: https://registry-1.docker.io


Logs: /var/log/harbor