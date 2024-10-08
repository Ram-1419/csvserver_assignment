# csvserver_assignment solution 

**Part I:**


**bash** 
docker run -d infracloudio/csvserver:latest docker ps -a

Check logs if it's failing:

**bash** 
docker logs container_id

Create gencsv.sh:

**bash
#!/bin/bash** 
start=$1 end=$2 for i in $(seq $start $end); do echo "$i, $((RANDOM % 1000))" >> inputFile done
Make it executable and run:

**bash**
chmod +x gencsv.sh ./gencsv.sh 2 8
Run container with inputFile:

**bash** 
docker run -d -v $(pwd)/inputFile:/csvserver/inputdata infracloudio/csvserver:latest

Access shell and find port:
**bash**
docker exec -it container_id /bin/bash netstat -tuln 

Note the port, then exit and stop the container.

Run with correct port and environment variable:

**bash** 
docker run -d -p 9393:9300 -v $(pwd)/inputFile:/csvserver/inputdata -e CSVSERVER_BORDER=Orange infracloudio/csvserver:latest
 Generate output:

 **Bash**
curl -o ./part-1-output http://localhost:9393/raw
Generate logs:

**bash** 
docker logs container_name > & part-1-logs

Commit and push changes to GitHub.


**Part II:**
Stop and remove all containers:

**bash** 
docker stop (dockerps−aq)dockerrm(docker ps -aq)
Create docker-compose.yaml:
version: '3' services: csvserver: image: infracloudio/csvserver:latest ports: - "9393:9300" volumes: - ./inputFile:/csvserver/inputdata env_file: - csvserver.env
Create csvserver.env:
CSVSERVER_BORDER=Orange
Test with:

**bash** 
docker-compose up -d
Commit and push changes.

**Part III:**
Stop containers:

**bash** 
docker-compose down
Update docker-compose.yaml:
version: '3' services: csvserver: image: infracloudio/csvserver:latest ports: - "9393:9300" volumes: - ./inputFile:/csvserver/inputdata env_file: - csvserver.env
prometheus: image: prom/prometheus:v2.45.2 ports: - "9090:9090" volumes: - ./prometheus.yml:/etc/prometheus/prometheus.yml

Create prometheus.yml:

global: scrape_interval: 15s
scrape_configs:
•	job_name: 'csvserver' static_configs:
o	targets: 'csvserver:9300'
Start services:

**bash** 
docker-compose up -d
Prometheus at http://localhost:9090


