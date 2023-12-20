# messi-ticket-price-tracker
web app to track Lionel Messi ticket prices

# Server Setup Steps
## Setup AWS
Launch a EC2 Linux Instance, set security group to allow HTTP request. 

## Virtual Environment
Run `python3 -m venv venvName` to prepare a virtual environment folder, them run `source venv/bin/activate` to start the virtual environment. Use [requirements.txt] to track dependencies and version numbers.

## Setup gunicorn
gunicorn is a wsgi NEED explaination

    gunicorn -b 0.0.0.0:8000 app:app
    sudo nano /etc/systemd/system/helloworld.service

    #check existing processes on port 8000
    sudo lsof -i :8000

## Setup a service for gunicorn
    sudo nano /etc/systemd/system/webserver.service
    ```
    [Unit]
    Description=Gunicorn instance for a simple hello world app
    After=network.target
    [Service]
    User=ubuntu
    Group=www-data
    WorkingDirectory=/home/ubuntu/repo/WebServer
    ExecStart=/home/ubuntu/repo/WebServer/venv/bin/gunicorn -b localhost:8000 app:app
    Restart=always 
    [Install]
    WantedBy=multi-user.target
    ```
    sudo systemctl daemon-reload
    sudo systemctl start webserver
    sudo systemctl enable webserver
    sudo systemctl stop webserver

Try run `curl localhost:8000`, the server will return the file to the terminal.

## Setup nginx
Nginx is a web server and reverse proxy server that is typically installed and managed separately from Python packages. To install Nginx on Ubuntu, you can use `sudo apt install nginx`.

Run the following commands, then other users on the internet can sent http requests to the ip address of this webserver.
```
    sudo systemctl start nginx
    sudo systemctl enable nginx
```
We need to edit the default nginx settings to point to gunicorn by running `sudo nano /etc/nginx/sites-available/default`.

upstream flaskwebserver{
        server 127.0.0.1:8000;
}

server{
    ...
}

Also edit the location to:
proxy_pass http://flaskwebserver;