# Assignment_infracloud
**Assignment_infracloud:**

The usecase asks us to run a container using the image `infracloudio/csvserver:latest` however when we try to run the container it appears the container immediately exits, we can verify that using `docker container ls` and to investigate we have to use the command `docker logs <container id>` it gives us an error message `error while reading the file "/csvserver/inputdata": read /csvserver/inputdata: is a directory `
so it expects a file to be read while container is running.

I went ahead to inspect the docker image `infracloudio/csvserver:latest` and use the command `docker exec -it <container-name> bash` this allowed me to enter the docker container using the bash mode. I went ahead to observe that it takes us to the WORKDIR `csvserver`, here I understood that there is a `csvserver` directory within which there is a file `csvserver` and we need to place the `inputdata` file in this path, so it should technically be `/csvserver/inputdata` 

I exited the container and removed the container traces by command `docker rm <container-id> `

Also, as per the instructions from the usecase there should be a `gencsv.sh ` file which is a bash script which should generate a `inputFile` containing a key, value format with index, Random_number.

Created a script name `gencsv.sh ` with content as
```
#!/bin/bash

for (( i=$1 ; i<=$2 ; ++i )); do
    echo "$i,$RANDOM"
done > inputFile
```

This would expect 2 arguments at run time, so it would be like `bash gencsv.sh 2 8` so it generate the `inputFile` with 7 values.

**Actual Implementation:**


I tried to run the command `docker run -d --name <cont_name> -v /home/ubuntu/csvserver/inputdata/inputFile:/csvserver/inputdata infracloudio/csvserver:latest`

This command not just created the container, but also mounted the path `/home/ubuntu/csvserver/inputdata/inputFile` inside the container at `/csvserver/inputdata` this made the container get the inputFile that it was expecting at the running and made it stable and not exited.

So as per the usecase it asked us to add the environment variables as `CSVSERVER_BORDER= Orange` and map a port number, based on `docker inspect infracloudio/csvserver:latest` we understood, container is listening on port 9300 and as per the instruction mapped it to the `host port: 9393` and re-run the above command 

`docker run -d --name <cont_name>  -v /home/ubuntu/csvserver/inputdata/inputFile:/csvserver/inputdata  -e CSVSERVER_BORDER=Orange -p 9393:9300 infracloudio/csvserver:latest` This made the docker container run stable and it exposed the port `localhost:9393` and output can be seen as below:

```
Welcome to the CSV Server
Index	Value
2	23835
3	22778
4	20883
5	15780
6	8004
7	4237
8	1530
```

The environment variable  `CSVSERVER_BORDER= Orange` created a Orange border around the `Welcome to CSV Server`.
