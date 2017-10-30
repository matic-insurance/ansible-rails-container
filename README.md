rails-container-app
=========
[![Build Status](https://travis-ci.org/matic-insurance/ansible-rails-container-app.svg?branch=master)](https://travis-ci.org/matic-insurance/ansible-rails-container-app)

Role used to download, configure and run docker container with rails app, before running app role runs migrations

Requirements
------------

Ubuntu 14.04 is tested.

This role uses Ansible's docker module, so requirements are [the same](https://docs.ansible.com/ansible/docker_image_module.html#requirements-on-host-that-executes-module).

Role Variables
--------------

Here is the list of Required variables with default values:
```yaml
# Docker repository image and tag
app_docker_image: 'alpine'
app_docker_image_tag: 'latest'

#Name of the container when it's running
app_container_name: 'rails'
# List of exposed ports for container
app_ports_mapping: []
#Command to run in docker
app_command: 'bundle exec rails s'
# Dict of environment variables
app_environment_vars: {}
# Configuration files to deploy on server and mount to image
app_configuration_files: {}
```
If you pulling image from private docker repository - specify docker credentials:
```yaml
# Docker credentials for private image
app_docker_login: 'login'
app_docker_password: 'password'
app_docker_email: 'email'
```

These variables are optional and can be changed if needed
```yaml
# Environment to run rails
app_environment: production
# Folder with all config files on local machine
app_files_local_folder: './files'
#Force image pull
app_force_image_pull: true
#Docker container restart policy
app_container_restart_policy: always
#Migration command
app_migration_command: 'bundle exec rake db:migrate'
#Container memory limit
container_memory_limit: 1g
```

Dependencies
------------

No dependencies

Example Playbook
----------------

Simpliest playbook can be following:

```yaml
- hosts: webservers
  roles:
    - role: rails-container-app
      app_docker_image: 'maticinsurace/rails-app'
      app_docker_tag: 'latest'
      app_ports_mapping: ['3000:3000']
```
This playbook will pull image maticinsurace/rails-app:latest, 
run migrations `bundle exec rake db:migrate` and start rails app `bundle exec rails s`

If you want to specify additional environment variables:
```yaml
- hosts: webservers
  roles:
    - role: rails-container-app
      app_command: 'bundle exec sidekiq'
      app_environment_vars: 
        REDIS_URL: redis://redis.host:6379
        DATABASE_URL: postgress://db.host:5432
```
THis will execute sidekiq and add redis/postgress variables

If you want custom files to be deployed to the app:
```yaml
- hosts: webservers
  roles:
    - role: rails-container-app
      app_files_local_folder: './files/webserver'
      app_configuration_files: 
        settings.yaml: /app/config/settings.local.yaml
        apns_cert.pem: certs/apns.pem
```
This will read file from local machine using key as path after `app_files_local_folder`
and mount them to docker image using value as path. E.g `./files/webserver/settings.yaml` 
will be mounted as `/app/config/settings.local.yam:ro`

License
-------

MIT

Author Information
------------------

Matic is a communication platform that connects lenders and borrowers originating a new home loan. A borrower now knows where they are in the loan process and what they need to do to complete the loan.
