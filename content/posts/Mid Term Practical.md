---
title: "Mid Term Practical"
date: 2023-08-15T22:04:52-04:00
tags:
- docker
draft: false
---
### **CST8254**

**Important: Once you are asked to make a screenshot, you must upload the screenshot to Bright space right away.**

### **Summer 23**

- Install **Docker** on your raspberry Pi.
    
    > 
    > 
    > 1. Update the system:
    >     
    >     ```bash
    >     sudo apt update
    >     sudo apt upgrade
    >     ```
    >     
    > 2. Install Docker dependencies:
    >     
    >     ```bash
    >     sudo apt install apt-transport-https ca-certificates software-properties-common
    >     ```
    >     
    > 3. Add Docker's GPG key:
    >     
    >     ```bash
    >     curl -fsSL https://download.docker.com/linux/raspbian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
    >     
    >     ```
    >     
    > 4. Add Docker's official repository:
    >     
    >     ```bash
    >     echo "deb [arch=armhf signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/raspbian $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    >     ```
    >     
    > 5. Update the package lists again:
    >     
    >     ```bash
    >     sudo apt update
    >     ```
    >     
    > 6. Install Docker:
    >     
    >     ```bash
    >     sudo apt install docker-ce docker-ce-cli containerd.io
    >     ```
    >     
    > 7. Add your username to the **`docker`** group to run Docker commands without sudo:
    >     
    >     ```bash
    >     sudo usermod -aG docker <username>
    >     ```
    >     
    > 8. Restart the Raspberry Pi:
    >     
    >     ```bash
    >     sudo reboot
    >     ```
    >     
    >     After the reboot, Docker should be installed and running on your Raspberry Pi. You can verify the installation by running the following command:
    >     
    >     ```bash
    >     docker version
    >     ```
    >     
    
- Install **docker-compose** on your raspberry Pi.
    
    > To install Docker Compose on your Raspberry Pi, you can follow these steps:
    > 
    > 1. Update the system:
    >     
    >     ```bash
    >     sudo apt update
    >     sudo apt upgrade
    >     ```
    >     
    > 2. Install dependencies:
    >     
    >     ```bash
    >     sudo apt install -y libffi-dev libssl-dev python3 python3-pip
    >     ```
    >     
    > 3. Install Docker Compose using pip3:
    >     
    >     ```bash
    >     sudo pip3 install docker-compose
    >     ```
    >     
    > 4. Verify the installation by checking the Docker Compose version:
    >     
    >     ```bash
    >     docker-compose --version
    >     ```
    >     
    
- In your home Pi folder,
    - Create a folder `MT302S23`
    - Create a folder `wordpress` in your `MT302S23` folder
    - Inside the `wordpress` folder, create a file `docker-compose.yml`
        
        Cut and paste the following code into that file
        

```yaml
version: '3.3'

services:
   db:
     image: hypriot/rpi-mysql
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: password
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress
     networks:
       - wpsite

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - '8080:80'
     restart: always
     volumes: ['./:/var/www/html']
     environment:
       WORDPRESS_DB_HOST: db:33060
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: wordpress
     networks:
       - wpsite
networks:
   wpsite:
volumes:
   db_data:
```

- Modify the file so that:
    - your pi is mapping its wordpress port 8080 to port 80
    - your pi is mapping the mysql port 33060 to port 3306
    - the MYSQL_ROOT_PASSWORD is your userid/username (first part of your email address)
    - the MYSQL_DATABASE is u<student number>.
        - for instance if your student number is 040123456 the db name is `u040123456`
    - the MYSQL_USER is your userid/username (first part of your email address)
    - the MYSQL_PASSWORD is mywordpress
    
    > You need to ensure that wordpress and sql configuration information is the same, you need to follow the `<enviroment>` part below to adjust the part, the other parts according to the task requirements can be changed. (Remember to replace your student information:`<username>` `u<student number>`)
    > 
    > 
    > ```yaml
    > version: '3.3'
    > 
    > services:
    >    db:
    >      image: hypriot/rpi-mysql
    >      volumes:
    >        - db_data:/var/lib/mysql
    >      restart: always
    >      environment:
    >        MYSQL_ROOT_PASSWORD: <username>
    >        MYSQL_DATABASE: u<student number>
    >        MYSQL_USER: <username>
    >        MYSQL_PASSWORD: mywordpress
    >      networks:
    >        - wpsite
    > 
    >    wordpress:
    >      depends_on:
    >        - db
    >      image: wordpress:latest
    >      ports:
    >        - '8080:80'
    >      restart: always
    >      volumes: ['./:/var/www/html']
    >      environment:
    >        WORDPRESS_DB_HOST: db:33060
    >        WORDPRESS_DB_USER: <username>
    >        WORDPRESS_DB_PASSWORD: mywordpress
    > 			 WORDPRESS_DB_NAME:u<student number>
    >      networks:
    >        - wpsite
    > networks:
    >    wpsite:
    > volumes:
    >    db_data:
    > ```
    > 
- Once the file is saved, issue the following command:

`COMPOSE_HTTP_TIMEOUT=200`

- Create the container (it may take a few minutes).
    - If you get a timeout error, recreate the container after a few seconds using the `-force-recreate` option.
    
    > 
    > 
    > 1. Navigate to the directory where your **`docker-compose.yml`** file is located. In your case, it should be inside the **`wordpress`** folder within the **`MT302S23`** folder.
    >     
    >     ```bash
    >     cd ~/MT302S23/wordpress
    >     ```
    >     
    > 2. Run the **`docker-compose up`** command to create and start the containers:
    >     
    >     ```bash
    >     docker-compose up
    >     ```
    >     
    >     Docker Compose will read the **`docker-compose.yml`** file and create the specified services, which in your case are the **`db`** and **`wordpress`** services.
    >     
    >     The first time you run this command, Docker will pull the necessary Docker images if they are not already present on your Raspberry Pi. This step may take a few minutes depending on your internet connection and the size of the images.
    >     
    >     If you encounter a timeout error during the image pull or container creation, you can try to recreate the container by adding the **`--force-recreate`** option:
    >     
    >     ```bash
    >     docker-compose up --force-recreate
    >     ```
    >     
    >     This option forces the recreation of containers even if they are already running.
    >     
    > 3. Once the containers are successfully created and started, you should see the logs from the containers in the terminal. You can keep the terminal open to monitor the logs or open a new terminal window to continue using the command line.
    >     
    >     If everything goes well, you should be able to access your WordPress site by opening a web browser and navigating to **`http://localhost`** or **`http://<Raspberry Pi IP address>`**. The WordPress installation process should start, and you can proceed with the setup.
    >     
    >     ### Other available commands
    >     
    >     - **If something wrong, use these command to check**.
    >         1. Check the database container logs for any error messages or connection issues. Run the following command to view the logs:
    >             
    >             ```bash
    >             docker-compose logs db
    >             ```
    >             
    >             Look for any error messages related to the database server.
    >             
    >         2. Confirm that the database container is running. Run the following command to check the status of the containers:
    >             
    >             ```bash
    >             docker-compose ps
    >             ```
    >             
    >             Ensure that both the **`db`** and **`wordpress`** containers are in the "Up" state.
    >             
    >     
    >     - **Delete the original image and recreate it after updating the configuration file.**
    >         1. Clean up existing containers and volumes: Run the following command to stop and delete existing databases and WordPress containers, and delete the associated volumes. Please note that this will delete existing data.
    >             
    >             ```bash
    >             docker-compose down -v
    >             ```
    >             
    >         2. Update the image and recreate the container: Run the following command to pull the latest image and recreate the container.
    >             
    >             ```bash
    >             docker-compose pull
    >             docker-compose up -d
    >             
    >             ```
    >             
    >             - **`d`**flag will run the container in the background.
    >         3. Check the container status: Run the following command to check the status of the container.
    >             
    >             ```bash
    >             docker-compose ps
    >             ```
    >             
    >             Ensure that both services are running.（**`db`** & **`wordpress`**）
    >             
    
- From the `MT302S23` folder, issue the `tree` command and save the output to a file named `mt.txt`.
- From the `MT302S23` folder, issue the `clear` command .
- From the `MT302S23` folder, issue the following commands one by one
    
    ```bash
    date
    ls -als
    hostname -i
    docker ps -a
    docker images
    ```
    
- Take a screenshot of the resulting window. `screen1.png`.
- Take a screenshot of the output of the command that displays which tcp port the Pi is listening to. `screen2.png`
    
    > Run the following command to display the listening TCP ports:
    > 
    > 
    > ```bash
    > sudo netstat -tuln
    > ```
    > 
- From your laptop, connect to the Pi wordpress site and set up wordpress with values of your choice for the site title, username and password. Once wordpress is installed, log on to wordpress.
    - Take a screenshot of the Wordpress admin site and a screenshot of the site itself. The 2 screenshots must show the IP of the Pi in the address bar. `screen3.png` (admin) and `screen4.png` (site)
- On your Pi, find the IP address that has been assigned to the database container:
    - Take a screenshot of the command used and its result. `screen5.png`
    
    > 
    > 
    > 1. Run the following command to get the container ID of the database container:
    >     
    >     ```bash
    >     docker ps -f name=wordpress_db_1 -q
    >     ```
    >     
    >     This command filters the running containers and returns the container ID of the database container (**`wordpress_db_1`** in this case).
    >     
    > 2. Once you have the container ID, run the following command to inspect the container and retrieve its IP address:
    >     
    >     ```bash
    >     docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <container_id>
    >     ```
    >     
    >     Replace **`<container_id>`** with the actual container ID you obtained in the previous step.
    >     
    > 3. The command will output the IP address assigned to the database container. Note down this IP address for further use.
    > 
    > By following these steps, you will be able to find the IP address assigned to the database container on your Pi.
    > 
- Connect to the mySql server using a TCP connection to that IP address. Access the container via Bash then connect to mysql.
    - Take a screenshot of the command used and its result. `screen6.png`
    
    > 
    > 
    > 1. Use the **`docker ps`** command to find the container ID of the database container. Run the following command:
    >     
    >     ```bash
    >     docker ps -f name=wordpress_db_1 -q
    >     ```
    >     
    >     This command will display the container ID of the database container (**`wordpress_db_1`** in this case).
    >     
    > 2. Once you have the container ID, access the container's Bash shell using the **`docker exec`** command. Run the following command:
    >     
    >     ```bash
    >     docker exec -it <container_id> bash
    >     ```
    >     
    >     Replace **`<container_id>`** with the actual container ID you obtained in the previous step.
    >     
    > 3. You will now be inside the container's Bash shell. To connect to the MySQL server, run the following command:
    >     
    >     ```bash
    >     mysql -h localhost -u <username> -p
    >     ```
    >     
    >     This command connects to the MySQL server running inside the container using the specified hostname (**`localhost`**), username (`**<username>**`), and prompts for the password.
    >     
    > 4. Enter the MySQL root password when prompted. Press Enter.
    > 5. You should now be connected to the MySQL server and have access to the MySQL command prompt.