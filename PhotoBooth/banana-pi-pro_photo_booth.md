# Armbian

https://www.armbian.com/banana-pi-pro/

## WIFI Setup

https://forum.armbian.com/topic/44964-wifi-setup/

# Software

https://github.com/PhotoboothProject/photobooth

## Prerequisites

https://nodejs.org/en/download/package-manager

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
```

### gphoto2

```bash
wget -O gphoto2-updater.sh https://raw.githubusercontent.com/gonzalo/gphoto2-updater/master/gphoto2-updater.sh
wget -O .env https://raw.githubusercontent.com/gonzalo/gphoto2-updater/master/.env
chmod +x gphoto2-updater.sh
sudo ./gphoto2-updater.sh
```


```bash
env LANG=C gphoto2 --debug --debug-logfile=my-logfile.txt --capture-image-and-download --filename=/var/www/html/data/tmp/20241213_035129.jpg
```

### Photobooth installation

```bash
sudo chown -R www-data:www-data /var/www/
```

```bash
cd /var/www/
sudo -u www-data -s
rm -r html/*
git clone https://github.com/PhotoboothProject/photobooth html
cd /var/www/html
git submodule update --init
npm install
npm run build
exit
```

