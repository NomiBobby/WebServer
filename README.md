# messi-ticket-price-tracker
web app to track Lionel Messi ticket prices

# Server Setup Steps
## Setup AWS
Launch a EC2 Linux Instance, set security group to allow HTTP request. 

## Setup gunicorn
    gunicorn -b 0.0.0.0:8000 app:app
    sudo nano /etc/systemd/system/helloworld.service

## Setup a service for gunicorn
    sudo systemctl daemon-reload
    sudo systemctl start helloworld
    sudo systemctl enable helloworld
    sudo systemctl stop helloworld

## Setup nginx
    sudo systemctl start nginx
    sudo nano /etc/nginx/sites-available/default
    sudo systemctl restart nginx
