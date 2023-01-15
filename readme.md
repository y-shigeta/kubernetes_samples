# Kubernets sample yaml

# Overview

This is just reminder from
[kubernetes.io](https://kubernetes.io/docs/tutorials/configuration/configure-redis-using-configmap/)
[docker compose sample](https://github.com/docker/awesome-compose.git)

## Install

- mysql.yaml
  - gcloud services enable container.googleapis.com sqladmin.googleapis.com

## Usage

- phpapp-redis.yml

  - kubectl port-forward svc/frontend 8080:80
  - kubectl scale deployment frontend --replicas=5

- zookeeper.yml

  - kubectl exec -it -- zkCli.sh

- statefulset-web.yml
  - for i in 0 1; do kubectl exec "web-$i" -- sh -c 'echo "$(hostname)" > /usr/share/nginx/html/index.html'; done
  - for i in 0 1; do kubectl exec web-$i -- chmod 755 /usr/share/nginx/html; done
  - for i in 0 1; do kubectl exec -i -t "web-$i" -- curl http://localhost/; done

## Cleanup

- redis-configmap

  - kubectl delete pod/redis configmap/example-redis-config

- phpapp-redis.yml

  - kubectl delete deployment -l app=redis
  - kubectl delete service -l app=redis
  - kubectl delete deployment frontend
  - kubectl delete service frontend

- statefulset-web.yml
  - kubectl delete sts web
  - kubectl delete svc nginx

## Licence

## Author

[y-shigeta](https://github.com/y-shigeta)
