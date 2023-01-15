# docker command

## Docker Hub
- check image on docker hub
docker login
docker search centos --filter is-official=true
docker search centos --filter=is-automated=false		#GithubにUploadされたDockerfileと連携して自動Buildを行う。


docker pull  	#Pull repository from ducker hub
docker tag centos:7 shige7822/centos:7-git		#name repository
docker build -t shige7822/centos:1.1 .  	# build docker image
docker run --name hello1 -d hello1
docker exec -it hello1 bash
docker exec -it mycentos bash				#connect the running container (alpineの場合はash)
docker commit b62ee003e44b centos:7-git		
docker push shige7822/centos:7-git		
docker network create mynetwork
docker network ls
docker run —-name mycentos —network mynetwork -d centos:7   	#run as background
docker run -—name websv -p 8080:80 -d nginx:latest
docker run -—name websv1 -p 8080:80 -d hello1:latest

docker ps -a  	# list all containers
docker log [containerid]	# see logs
docker stop
docker kill

docker system df #サイズ確認

docker image prune -a		#remove all unused images
docker rmi <image>	# remove image one by one

- check automatic startup for docker and disable
docker inspect -f "{{.Name}} {{.HostConfig.RestartPolicy.Name}}" $(docker ps -aq) | grep always
docker update --restart=no [container name]


## docker compose 
docker-compose up -d
docker-compose down					# shutdown and remove container and netowrk
docker-compose down --rmi all --volumes	# Remove all image and volumes
docker container stop node-exporter

- prometeus [Tutorial for Prometeus](https://grafana.com/tutorials/grafana-fundamentals/?utm_source=grafana_gettingstarted)
docker run -d --name node_exporter -p 9100:9100 prom/node-exporter
docker run -d --name prometheus -p 9090:9090  -v /Users/yas/Desktop/VSCode-Python/K8s/Prometheus/volume:/prometheus -v /Users/yas/Desktop/VSCode-Python/K8s/Prometheus/Prometheus.yaml:/etc/prometheus/prometheus.yml prom/prometheus
