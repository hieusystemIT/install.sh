#!/bin/bash

set -e

echo "Updating system packages..."
sudo apt update -y && sudo apt upgrade -y

echo "Installing Supervisor..."
sudo apt install -y supervisor

echo "Installing Nginx..."
sudo apt install -y nginx
sudo systemctl enable nginx
sudo systemctl start nginx

echo "Installing OpenJDK 17..."
sudo apt install -y openjdk-17-jdk
java -version

echo "Installing PHP 7.4 and required modules..."
sudo apt install -y software-properties-common
sudo add-apt-repository ppa:ondrej/php -y
sudo apt update
sudo apt install -y php7.4 php7.4-cli php7.4-fpm php7.4-mysql php7.4-curl php7.4-xml php7.4-mbstring php7.4-zip
sudo systemctl enable php7.4-fpm
sudo systemctl start php7.4-fpm
php7.4 -v

echo "Installing Redis..."
sudo apt install -y redis-server

echo "Installing MariaDB..."
sudo apt install -y mariadb-server mariadb-client

echo "Enabling and restarting services..."
for service in supervisor redis-server mariadb; do
  sudo systemctl enable $service
  sudo systemctl restart $service
done

echo "Checking service status:"
systemctl status supervisor nginx php7.4-fpm redis-server mariadb --no-pager

echo "Installation completed."
