# Project 2 Documentation

## Install Docker in Ubuntu System
If any issues are encountered that are not mentioned in this documentation, reference the [Official Docker Installation Guide](https://docs.docker.com/engine/install/ubuntu/)

1. Access an Ubuntu System. This installation is specifically for an Ubuntu System. 

2. Add Docker's offical GPG key using these commands: 
    
        sudo apt update
        sudo apt install ca-certificates curl
        sudo install -m 0755 -d /etc/apt/keyrings
        sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
        sudo chmod a+r /etc/apt/keyrings/docker.asc 
    
3. Add the repository to Apt sources with:

        sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
        Types: deb
        URIs: https://download.docker.com/linux/ubuntu
        Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
        Components: stable
        Signed-By: /etc/apt/keyrings/docker.asc
        EOF

        sudo apt update

4. Install docker packages using: 

        sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
5. Ensure docker is enabled: 

        sudo systemctl status docker

    If not, use: 

        sudo systemctl start docker

6. Test docker. This command should show a confirmation ouput

        sudo docker run hello-world

7. Ensure docker compose is installed: 

        docker compose version

    If not installed, run: 
        sudo apt install docker-compose-plugin
        

## Run Example Voting App

1. Obtain App Files from Git at this link:

        https://github.com/dockersamples/example-voting-app

   Git should be installed on the system. If not, run: 

        sudo apt install git

   Find the `<> Code` dropdown and copy the HTTPS link. Open a terminal create a project directory. Navigate into the directory and run: 
        
        git clone {HTTPS link from above}

2. Run the app inside the directory you created and cloned the application into using: 

        sudo docker compose up

   After the app is running, access it from: 

        http://localhost:8080/

   The results can be found at: 
        
        http://localhost:8081/

