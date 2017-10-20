## CREATE VM
install VM
and set remote user ansible
and set ip in hosts.ini file

для различного окружения создаются разные списки ип адресов 
для окружения разработчиков списки вм буду храниться в файле
```
hosts/prod/hosts.ini 
hosts/dev/hosts.ini 
```


## KEYS
create pair of keys here __roles/authorized_key/keys__
```
ssh-keygen
Generating public/private key pair.
Enter file in which to save the key (/home/username/.ssh/id_ecdsa): roles/authorized_key/keys

```
### Зарегистрируем ключи 
после перезагрузки необходимо заново регитрировать ключи 
```
cp roles/authorized_key/keys/id_rsa ~/.ssh/id_rsa_deploy && chmod 700 ~/.ssh/id_rsa_deploy && ssh-add ~/.ssh/id_rsa_deploy
```
или прописать их в конфигурации клиента ссх 
```
cp roles/authorized_key/keys/id_rsa /etc/ssh/ssh_hg_deploy_key && chmod 700 /etc/ssh/ssh_hg_deploy_key

cat /etc/ssh/ssh_config
...
   IdentityFile /etc/ssh/ssh_hg_deploy_key
...
```

## AUTHORIZED KEY (копируем ключи на ВМ)
copy ssh-keys and add sudoers line on remote machine
test key  ssh ansible@192.168.56.101 -i roles/authorized_key/keys/deploy
may be some permission trouble chmod 600 roles/authorized_key/keys/deploy
```
ansible-playbook -i hosts/prod.ini task/system/authorized_key.yml  -k --ask-sudo-pass
```

## COMMANDS (выполняем любые команды на группе серверов)
you can run command as
```
ansible all -i hosts/prod.ini -m shell -a 'ls /etc'
```

## INSTALL services
install nginx,php-fpm,..,posgresql,redis for services and apps
```
ansible-playbook -i hosts/prod.ini task/system/soft-install.yml
```

## UPDATE CODE
```
ansible-playbook -i hosts/prod.ini task/update-code.yml
```

## CONFIGURE NGINX


Configure balancer nginx services 
```

hosts/group_vars/nginx-balancer.yml
...
    with_services:
      - sign
...
 
```

Run playbooks
```
ansible-playbook -i hosts/prod.ini task/nginx/balancer.yml 
ansible-playbook -i hosts/prod.ini task/nginx/front.yml 

```