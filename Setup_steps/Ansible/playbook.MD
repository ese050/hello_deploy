---
- hosts: ansible

  tasks:
  - name: create docker image
    command: docker build -t regapp:latest .
    args:
      chdir: /opt/docker

  - name: create tag to push image onto dockerhub
    command: docker tag regapp:latest ese11/regapp:latest

  - name: push docker image
    command: docker push ese11/regapp:latest


---

- hosts: dockerhost
  tasks:

  - name: stop existing container
    command: docker stop regapp-server
    ignore_errors: yes

  - name: remove the container
    command: docker rm regapp-server
    ignore_errors: yes

  - name: remove image
    command: docker rmi ese11/regapp:latest
    ignore_errors: yes

  - name: create container
    command: docker run -d --name regapp-server -p 8082:8080 ese11/regapp:latest