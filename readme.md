Kubernets sample yaml
===

## Overview

This is just reminder from 
[kubernetes.io](https://kubernetes.io/docs/tutorials/configuration/configure-redis-using-configmap/)

## Description

## Requirement


## Install

## Usage
- phpapp-redis.yml
  - kubectl port-forward svc/frontend 8080:80
  - kubectl scale deployment frontend --replicas=5

## Cleanup
- redis-configmap
  - kubectl delete pod/redis configmap/example-redis-config

- phpapp-redis.yml
  - kubectl delete deployment -l app=redis
  - kubectl delete service -l app=redis
  - kubectl delete deployment frontend
  - kubectl delete service frontend

## Licence

## Author
[y-shigeta](https://github.com/y-shigeta)