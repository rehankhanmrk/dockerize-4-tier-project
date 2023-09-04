
# Dockerize 4 tier application such as Nginx,Postgres-db,Phyton,Redis

## Installation instructions

### 1. Launch amazon ubuntu server in aws 

### 2. ssh to ubuntu to install packages

```sh
ssh -i <key.pem> ubuntu@<ip-address> 
```

### 3. Update and Upgrade linux machine 

```sh
sudo apt update
```

```sh
sudo apt upgrade
```


#### 3.1 install docker 


#### First, update your existing list of packages:
```sh

sudo apt update
```
#### Next, install a few prerequisite packages which let apt use packages over HTTPS:
```sh
sudo apt install apt-transport-https ca-certificates curl software-properties-common
```

#### Then add the GPG key for the official Docker repository to your system:

```sh
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

#### Add the Docker repository to APT sources:

```sh
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

#### Update your existing list of packages again for the addition to be recognized:

```sh
sudo apt update
```

#### Make sure you are about to install from the Docker repo instead of the default Ubuntu repo:

```sh
apt-cache policy docker-ce
```

#### You’ll see output like this, although the version number for Docker may be different:

Output of apt-cache policy docker-ce
docker-ce:
  Installed: (none)
  Candidate: 5:20.10.14~3-0~ubuntu-jammy
  Version table:
     5:20.10.14~3-0~ubuntu-jammy 500
        500 https://download.docker.com/linux/ubuntu jammy/stable amd64 Packages
     5:20.10.13~3-0~ubuntu-jammy 500
        500 https://download.docker.com/linux/ubuntu jammy/stable amd64 Packages
Notice that docker-ce is not installed, but the candidate for installation is from the Docker repository for Ubuntu 22.04 (jammy).

#### Finally, install Docker:

```sh
sudo apt install docker-ce
```

#### Docker should now be installed, the daemon started, and the process enabled to start on boot. Check that it’s running:

```sh
sudo systemctl status docker
```
#### The output should be similar to the following, showing that the service is active and running:

Output
● docker.service - Docker Application Container Engine
     Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
     Active: active (running) since Fri 2022-04-01 21:30:25 UTC; 22s ago
TriggeredBy: ● docker.socket
       Docs: https://docs.docker.com
   Main PID: 7854 (dockerd)
      Tasks: 7
     Memory: 38.3M
        CPU: 340ms
     CGroup: /system.slice/docker.service
             └─7854 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock



#### If you want to avoid typing sudo whenever you run the docker command, add your username to the docker group:

```sh
sudo usermod -aG docker ${USER}
```
```sh
newgrp docker 
```

#### 4. Clone 4_tier_project Nginx-webserver,Phyton,Postgres-db,Redis

```sh
cd /home/ubuntu
```

```sh
git clone https://github.com/rehankhanmrk/dockerize-4-tier-project.git
```

```sh
cd dockerize-4-tier-project
```

#### show Dockerfile

```sh 
cat Dockerfile
```
#### Dockerfile

```
FROM tiangolo/uwsgi-nginx-flask:python3.8

WORKDIR /app

COPY ./app/requirements.txt /app/
RUN pip3 install -r /app/requirements.txt

COPY ./app /app
```

#### show docker-compose file
```sh
cat docker-compose.yml
```

#### show index.html file

```sh
cat app/templates/index.html
```
#### index.html file

```
<body style="background-color: black;">
<center style="background-color: blue;">
<h1> Wish-List App From Rehan DevOps For Tech_bridge</h1>

<b> Create your wishlist record here</b>

<form action="/save" method="POST">
	
	<label for="username">Username</label>
	<input name="username" id="username" required><br><br>

	<label for="place">Favorite Place</label>
	<input name="place" id="place" required><br><br>

	<label for="food">Favorite Food</label>
	<input name="food" id="food" required><br><br>

	<input type="submit" value="Save">

</form>

<b>Type in the username and fetch the WishList</b>
<form action="/get" method="POST">
	<label for="username">Username</label>
	<input name="username" id="username" required><br><br>
	<input type="submit" value="Submit">
</form>

{% if get %}
<font color="blue">The Wishlist for <b>{{username}}</b> is <b>{{place}}</b> and <b>{{food}}</b></font>
<small>{{msg}}</small>
{% endif %}

{% if no_record %}
<font color="red">{{msg}}</font>
{% endif %}

{% if user_exists %}
<font color="red">User <b>{{username}}</b> already exists with the wishlist <b>{{place}}</b> and <b>{{food}}</b></font>
<small>{{msg}}</small>
{% endif %}

{% if saved %}
<font color="green"> Successfully saved data for <b>{{username}}</b> with Wishlist <b>{{place}}</b> and <b>{{food}}</b>!!!</font>
{% endif %}

</center>
<br>
<a href="/keys">Keys</a>
{% if keys %}
	<h3> Keys </h3>
	<small>(From DataBase)</small>
	<ul>
	{% for username in usernames %}
		<li>{{username}}</li>
	{% endfor %}
	</ul>
{% endif %}


</body>
```

#### show docker-compose file
```sh
cat docker-compose.yml
```
##### docker-compose file

```
version: '3'
services:
  nginx:
    build: ./
    environment:
      - PYTHONBUFFERED=1
    ports:
      - "80:80"
    links:
      - redis
      - db
  redis:
    image: redis:6.0.8
    ports:
      - "6379:6379"
  db:
    image: postgres:12-alpine
    volumes:
      - postgres_data:/var/lib/postgresql/data/
    environment:
      - POSTGRES_USER=hello_flask
      - POSTGRES_PASSWORD=hello_flask
      - POSTGRES_DB=hello_flask_dev
    ports:
      - "5432:5432"
    
volumes:
  postgres_data:
```

#### run this Docker Compose file, navigate to the directory containing the docker-compose.yml file and execute the following command:

```sh
docker-compose up -d
```
#### check docker-compose running condition

```sh
docker ps
```
#### OR

```sh
docker-compose ps
```
#### then access your application through the Nginx web server at http://publicip

```sh
http://12.23.43.44
```

### 11. Visit your website HTTPS://<your website>
  Enjoy Your website serve


  
  
## Support 🙏😃
 
Thanks for your support :)

