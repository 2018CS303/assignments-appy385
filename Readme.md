# **Docker Case Study**

### **Objective**:-
- Dynamic Allocation of Linux systems for users
- Each user should have independent Linux System
- Specific training environment should be created in Container
- Monitor participants containers
- Automate container creation and deletion.

## Create training environment for L&D
The `ubuntu` or any other OS image might not be sufficient since it might not contain all necessary programs for the training. We can create a new image upon the original `ubuntu` image with the necessary programs and files and use that for each user.

- First build a new `ubuntu` container and use it's shell by running `docker run --name train -it ubuntu /bin/bash`. The container's shell should now be available.
- Make necessary changes there like installing new packages or adding new files.
- We can create a new image from the `train` container by running `docker commit train trainContainer:v1` (`trainContainer` is image name and `v1` is its tag indicating the version1 of the container) from the bash of your machine and not from your container bash.
- Now the `trainContainer` image will be used to create containers for each user.

## Allocate Linux systems for users
1.  To dynamically allocate the users a linux system create a shell script `create_userContainers.sh` to automatically create docker containers using a specific environment docker image for every user.

      ```
        a1
        a2
        a3
        a4
        a5
      ```
      ```sh
        echo -n "Enter user list file: "
        read file

        while read user
            do 
                docker create -it --name $user <Training Image> /bin/bash
            done < $file
      ```
2.  This creates a docker container corresponding to each username from that file.
3.  The user can then use the container allocated to him/her.

      ```sh
        echo -n "Enter your username: "
        read name
        docker start $name
        docker attach $name
      ```
4.  This allows user to enter to his/her allocated ubuntu system.

## Monitoring the container
- One can monitor the participants container using this script.

    ```sh
        echo -n "Enter container name to be monitored: "
        read name
        docker logs -f $name
    ```
- This shows the real time display of their bash which helps the participants if they have any doubts/bug in running applications.

- Incase the administrator wants to access the participants container bash for debugging purposes, he can use `docker exec -it <Container> bin/bash`

## Automating deletion of the containers
- Automate the deletion using  this script.

     ```sh
        echo -n "Enter 'all' to delete all user containers or enter 'user' to delete a specific user container: "
        read typ
        if [ "$typ" == "all" ]
        then
            echo -n "Enter the user list file: "
            read file
            while read user
                do
                    docker rm $user
                done < $file
        else
            echo -n "Enter the username: "
            read name
            docker rm $name
        fi
     ```
- This gives two options ie. to either delete all users containers at once or delete a specific user container.
