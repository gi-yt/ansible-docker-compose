Docker Compose Declarative
=========

A playbook that allows you to declaratively define docker compose

Role Variables
--------------

See templates/docker-compose.yml.j2. List coming soon :P

Some extra mandatory envvars:
`app`: the variable that contains nested data about compose
`app_name`: name of the stack to deploy
`default_restart_policy`: unless-stopped / never / always
`configs_dir`: Directory where configs for your stacks (like volumes basically) will be stored on server
`configs_dir_local`: Directory where configs for your stacks (like volumes basically) will be stored in the repo
`compose_dir`: Directory where compose file for the stacks will be stored on your server
`data_dir`: Directory where stack data will be stored on the server

Example Playbook
----------------

```yaml
- name: Docker
  hosts: in
  vars:
	apps:
	  groups:
	    APP_NAME:
		  needs_configs_dir: true
		  needs_data_dir: true
	      docker_settings:
	        services:
	          - name: CONTAINER-1
	            image: REGISTRY_PATH
	            ports:
	              - "1234:5678"
	            environment:
	              A: "BCD"
				mounts:
				  - "{{configs_dir}}/APP_NAME/config.yaml:/var/lib/app/config.yaml"
				  - "{{data_dir}}/APP_NAME/uploads:/var/lib/app/uploads"
				command: "--some-arg-here"
				user: 1000
  tasks:
    - name: Deploy stack role
      ansible.builtin.include_role:
        name: aryak.docker_compose_declarative
      vars:
        app: "{{ item.value }}"
        app_name: "{{ item.key | lower }}"
        default_restart_policy: unless-stopped
        configs_dir: "/opt/configs"
        configs_dir_local: "./configs/{{ item.key }}"
        compose_dir: "/opt/docker"
        data_dir: "/opt/docker"
      loop: "{{ apps.groups | dict2items }}"
      when: item.value.docker_settings
```

License
-------

AGPL 3.0

Author Information
------------------

Arya Kiran <me@aryak.me>
