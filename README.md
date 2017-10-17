## CREATE VM
install VM
and set remote user ansible
and set ip in hosts.ini file

для различного окружения создаются разные списки ип адресов 
для окружения разработчиков списки вм буду храниться в файле
```
hosts/prod/hosts.ini 
```


## KEYS
create pair of keys here
```
ssh-keygen
Generating public/private key pair.
Enter file in which to save the key (/home/username/.ssh/id_ecdsa): roles/authorized_key/keys

```

## AUTHORIZED KEY (копируем ключи на сервера ВМ)
copy ssh-keys and add sudoers line on remote machine
test key  ssh ansible@192.168.56.101 -i roles/authorized_key/keys/deploy
may be some permission trouble chmod 600 roles/authorized_key/keys/deploy
```
ansible-playbook -i hosts/prod/hosts.ini task/system/authorized_key.yml  -k --ask-sudo-pass
```

## COMMANDS (выполняем любые команды на группе серверов)
you can run command as
```
ansible all -i hosts/prod.ini -m shell -a 'ls /etc'
```

## INSTALL services
install nginx,php-fpm,..,posgresql,redis for services and apps
```
ansible-playbook -i hosts/dev/hosts.ini task/system/soft-install.yml
```

## UPDATE CODE
```
ansible-playbook -i hosts/dev/hosts.ini task/update-code.yml
```

## Проблемы с подключением
```bash
 UNREACHABLE! => {
    "changed": false, 
    "msg": "Failed to connect to the host via ssh: Permission denied (publickey,password).\r\n", 
    "unreachable": true
}
```
Try registering the private key to your keychain
```
ssh-agent bash
ssh-add <path to private key>
```
