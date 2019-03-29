# LOG8371 - TP2 - Team Alpha
* Francis Forget
* Michaël Salmon
* Vincent Labonté
* Samuel Chapleau

## GitHub
`https://github.com/vincentlabonte/jguwekarest`

## Video
`https://youtu.be/HTc6p5JIkZw`

## Setup
1. Download, Install and [Setup](https://docs.docker.com/get-started/) **Docker**
2. [Download](https://www.ej-technologies.com/download/jprofiler/files), Install and Setup **JProfiler**
3. [Download](https://jmeter.apache.org/download_jmeter.cgi), Install and [Setup](http://jmeter.apache.org/usermanual/get-started.html) **JMeter**
4. Download or Fork our [**GitHub Repository**](https://github.com/vincentlabonte/jguwekarest)
5. Follow instructions as specified below

## Docker

### Build the Docker Image

* Download the source code from our Github repository  
  `git clone git@github.com:vincentlabonte/jguwekarest.git`
* Change into code directory  
  `cd jguwekarest`
* Checkout branch ***master*** for OpenAPI 3.0.1  
  `git checkout master`
* Compile the war (Web Application Archive) file with maven  
  `mvn clean package`
* Build the docker image (replace dockerhubuser with your docker hub account user)  
  `docker build -t dockerhubuser/jguweka:OAS3 .`
* Check images  
  `docker images`

### Run the Docker Container

* If you run the container locally don't forget to start also a mongodb container as a data base with:  
`docker pull mongo; docker run --name mongodb -d mongo`
* Run the image as a local container (replace dockerhubuser with your docker hub account user)  
`docker run -p 8080:8080 --link mongodb:mongodb dockerhubuser/jguweka:OAS3`
* Load the Swagger-UI representation in a web-browser at port 8080  
  e.g.: `firefox http://0.0.0.0:8080`

## JProfiler

### Profile the execution using JProfiler
* Replace dockerhubuser with your docker hub account user in the docker-compose.yml file  
* Launch the containers  
  `docker-compose up`
* Connect to the first container running the WEKA RESTful API Webservice  
  `docker exec -it jguwekarest_jguweka_1 bash`
* Attach JProfiler to the running process in the Weka container `/usr/local/jprofiler10.1.5/bin/jpenable -g -p 8849`
* JProfiler GUI -> Session -> New Session
* Session Settings -> Session Type -> Attach Type -> Attach to remote JVM
* Execute docker ps and take note of the port `{rest_port}` linked to port `8849` of the `jguwekarest_jguweka_1` container  
* Profiled JVM Settings -> Direct network connection to `127.0.0.1` -> Profiling port `{rest_port}`
* Session Startup -> Initial Profiling Settings -> Sampling (Recommended) -> OK
* Send requests to the server
* JProfiler GUI -> Save Snapshot

## JMeter

### Create a Test Plan
* Open JMeter  
  `jmeter`
* Add a Thread Group to the Test Plan
  * Set the number of threads and the ramp-up period
* Add a HTTP Request Defaults to the Thread Group
  * Set the protocol (`http`), the server ip (`127.0.0.1`) and the port number (`80`)
  * Set the client implementation to `Java` in the Advanced tab
* Add a HTTP Header Manager to the Thread Group
  * Add header `Content-Type:multipart/form-data`
  * Add header `Accept:application/json`
* Add a HTTP Request to the Thread Group
  * Set the method (`POST`), the path (`/algorithm/BayesNet`)
  * Check `multipart/form-data`
  * Add a File Upload with the file path, the parameter name (`file`) and the MIME Type (`text/plain`)
* Add a listener to the Thread Group  
  e.g.: `View Results Tree`
* Save the Test Plan as a jmx file

### Execute the Test Plan
* Launch the containers  
  `docker-compose up`
* Execute the test  
  `jmeter -n -t [jmx file] -l [csv results file]`
  
### Profile the execution of scenarios with JProfiler
* Profile the execution using JProfiler
* Create a Test Plan using JMeter
* Execute the Test Plan
 * Save a snapshot using JProfiler
* Repeat for each scenario (small, average, augmented, exceptional)
