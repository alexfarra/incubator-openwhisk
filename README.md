# Setup OpenWhisk
- Assuming Ubuntu installation:

```
# Install git if it is not installed
sudo apt-get install git -y

# Clone openwhisk
git clone https://github.com/apache/incubator-openwhisk.git openwhisk

# Change current directory to openwhisk
cd openwhisk
```

  - Open JDK 8 is installed by running the following script as the default Java environment.

```
# Install all required software
(cd tools/ubuntu-setup && ./all.sh)
```

  - Install dependencies

```
sudo apt-get install python-pip
sudo pip install ansible==2.5.2
sudo pip install jinja2==2.9.6
````

  - Change RAM and time cap:
    
    ```
    ~/openwhisk/incubator-openwhisk/common/scala/src/main/resources/application.conf
    ```

    This file holds the RAM and action time limit. Change the following values to what you want, we've been using a max of 8GB for:
    
    For RAM:
    ```
    # action memory configuration
    memory {
        min = 128 m
        max = 8192 m
        std = 512 m
    }
    ```
    
    For Action Time:
    ```
    # action timelimit configuration
    time-limit {
        min = 100 ms
        max = 1440 m
        std = 5 m
    }
    ```
    
    For Action Time Log:
    ```
    # action log-limit configuration
    log-limit {
        min = 0 m
        max = 1440 m
        std = 20 m
    }
    ```
  
  - Build it. (Might have to run the following command twice!)

  ```
  cd <home_openwhisk>
  ./gradlew distDocker
  ```  
    
  - Using Ansible now:
  
  ```
  Go to ansible folder in openwhisk and 
  sudo ansible-playbook setup.yml
  ```
  
  - Run other Ansible code now:
  
  ```
  cd ansible
  sudo ansible-playbook couchdb.yml
  sudo ansible-playbook initdb.yml
  sudo ansible-playbook wipe.yml
  sudo ansible-playbook openwhisk.yml

  # installs a catalog of public packages and actions
  sudo ansible-playbook postdeploy.yml
  
  # installs alarm package
  sudo ansible-playbook providers.yml

  # to use the API gateway
  sudo ansible-playbook apigateway.yml
  sudo ansible-playbook routemgmt.yml
  ```
  
  - Configure CLI:
    > The API host can be acquired from the edge.host property in whisk.properties file. Use its value below
  
      ```
      ./bin/wsk property set --apihost <openwhisk_baseurl>
      ```
    > Add guest authentication
    
     ```
     ./bin/wsk property set --auth `cat ansible/files/auth.guest`
     ```
  
  - Change local environment to include openwhisk. E.g. add following to the file ~/.profile
  
  ```
  # add path to OpenWhisk
  export PATH=$PATH:/home/nabeel/openwhisk/bin
  # source the file
  source ~/.profile 
  ```
  
- Setup in complete. Now run hello world to see if everything is working file. Make sure to use " -i " in wsk commands for bypassing certificate checking
https://github.com/apache/incubator-openwhisk/blob/master/docs/samples.md
