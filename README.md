# Webserver
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
    Run `sudo nano /etc/systemd/system/webserver.service` to create the service. Paste the following in the edit window.
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
    Run the following commands to handle the service.
    ```
    sudo systemctl daemon-reload
    sudo systemctl start webserver
    sudo systemctl enable webserver
    sudo systemctl stop webserver
    ```

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

## Watch dog
Automating the process of restarting Gunicorn after each website update can be achieved through various methods.
One common approach is to use a process manager or a script to monitor changes in your codebase and restart the Gunicorn server when necessary.
Here's a simple way to achieve this using a tool called watchdog along with a shell script:

### Install watchdog
`pip install watchdog`

### Create a shell script
Create a shell script (e.g., restart_gunicorn.sh) with the following content:

```
#!/bin/bash

# Replace the following with your Gunicorn command
GUNICORN_CMD="gunicorn -w 4 -b 0.0.0.0:8000 your_app:app"

# Restart Gunicorn
pkill -f "$GUNICORN_CMD"
$GUNICORN_CMD
```

Make sure to replace your_app:app with the actual location of your Flask application.

### Watch for Changes with Watchdog
Create a Python script (e.g., watch_for_changes.py) to watch for changes in your codebase:
```
from watchdog.observers import Observer
from watchdog.events import FileSystemEventHandler
import subprocess

class CodeChangeHandler(FileSystemEventHandler):
    def on_any_event(self, event):
        if event.is_directory or not event.src_path.endswith('.py'):
            return
        print(f'Restarting Gunicorn due to changes in {event.src_path}')
        subprocess.call(['bash', 'restart_gunicorn.sh'])

def watch_for_changes():
    event_handler = CodeChangeHandler()
    observer = Observer()
    observer.schedule(event_handler, path='.', recursive=True)
    observer.start()

    try:
        while True:
            pass
    except KeyboardInterrupt:
        observer.stop()
    observer.join()

if __name__ == "__main__":
    watch_for_changes()

```

### Run the Watcher Script
`python watch_for_changes.py &`
This script will keep running and monitor changes in your codebase. When changes are detected, it will execute the restart_gunicorn.sh script, which stops the existing Gunicorn process and starts a new one.

Remember to adjust the paths and configurations according to your specific setup. Additionally, ensure that the scripts have the necessary permissions to execute. This approach provides a basic setup, and you may need to customize it based on your specific deployment environment and requirements.
