Sandbox with docker
====

This is sandbox to run apps
Dont think too much, just like a toy

## Build with compose
```
$ docker-compose up -d --build
```

## Get into container
```
$ docker exec -it sandbox-workspace_1 bash
```

## Stop running
```
$ docker-compose stop
```

## IP(Docker for Mac)
```
127.0.0.1
```

## ADD HOST
```
$ sudo sh -c "echo 127.0.0.1 sandbox.local >> /etc/hosts"
```

## ADD KEYS(Modify $PATH by yourself)
```
$ ssh-keygen -f ~/$PATH/sandbox/workspace/id_rsa
```

## ADD SSH CONFIG(Modify $PATH by yourself)
```
printf "\
Host sandbox.local
  HostName 127.0.0.1
  User sandbox
  Port 10080
  UserKnownHostsFile /dev/null
  StrictHostKeyChecking no
  LogLevel FATAL
  IdentityFile ~/$PATH/sandbox/workspace/id_rsa
" | cat >> ~/.ssh/config
```

## Get into container with ssh
```
$ ssh sandbox.local
```

## Run this command when you log into container
```
$ bash ~/init.sh
```

## Run this command if you want to use dotfiles
```
$ git clone -b sandbox-use https://github.com/hirokisan/dotfiles.git ~/dotfiles
$ bash ~/dotfiles/install.sh
```

## Referense
* [docker-compose](https://docs.docker.com/compose/compose-file/)
* [docker-compose-build](https://docs.docker.com/compose/reference/build/)
* [Install Docker for Mac](https://docs.docker.com/docker-for-mac/install/)
