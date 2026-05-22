# DHCP-PXE
## Configuring the PXE server for automatic installation
### Задание:

1. Настроить загрузку по сети дистрибутива Ubuntu 24

2. Установка должна проходить из HTTP-репозитория.

3. Настроить автоматическую установку c помощью файла user-data

### Решение:

#### Подготовим Vagrantfile в котором будут описаны 2 виртуальные машины:

• pxeserver (хост к которому будут обращаться клиенты для установки ОС)

• pxeclient (хост, на котором будет проводиться установка)

mkdir /home/maria/homework/my_vagrant_pxe

cd /home/maria/homework/my_vagrant_pxe

touch Vagrantfile

#### Создадим Ansible playbook

mkdir /home/maria/homework/my_vagrant_pxe/ansible

cd /home/maria/homework/my_vagrant_pxe/ansible

touch provision.yml

#### Cоздадим inventory file, ключи подключения vagrant создаст автоматически

touch hosts

#### После автоматической настройки клиента, ВМ выключится. Необходимо прописать в Vagrantfile приоритет загрузки с диска вместо сети - '--boot1', 'disk' 
