ENV['VAGRANT_SERVER_URL'] = 'https://vagrant.elab.pro'

Vagrant.configure("2") do |config|
#Создаёт VM с именем pxeserver. Внутри блока |server| все настройки применяются только к этой VM
config.vm.define "pxeserver" do |server|
#Базовый образ — Ubuntu 22.04 от Bento (проверенный, без GUI)
server.vm.box = 'ubuntu/22.04'
server.vm.host_name = 'pxeserver'
server.vm.network "forwarded_port", guest: 80, host: 8080
#внутренняя изолированная сеть VirtualBox с именем pxenet. VM видят только друг друга внутри VirtualBox, хост - нет (для DHCP)
server.vm.network :private_network, ip: "10.0.0.20", virtualbox__intnet: 'pxenet'
#еще один интерфейс со статичным ip для взаимодействия хост(ansible)->ВМ
server.vm.network :private_network, ip: "192.168.56.20", adapter: 3
#настройки ВМ из vbox
server.vm.provider "virtualbox" do |vb|
vb.memory = "1024"
#--natdnshostresolver1 on — чинит DNS внутри VM, если у хоста проблемы с разрешением имён. Без этого иногда apt update не работаетs
vb.customize ["modifyvm", :id,
"--natdnshostresolver1", "on"]
end
#запуск ansible на хосте после поднятия pxeserver (playbook - provision.yml, inventory - hosts) 
server.vm.provision "ansible" do |ansible|
ansible.playbook = "ansible/provision.yml"
ansible.inventory_path = "ansible/hosts"
#отключение проверки SSH ключей, необходимо для тестирования в безопасной изолированной среде
ansible.host_key_checking = "false"
end
end
config.vm.define "pxeclient" do |pxeclient|
pxeclient.vm.box = 'ubuntu/22.04'
pxeclient.vm.host_name = 'pxeclient'
#указываем ip который клиент попросит у dhcp сервера
pxeclient.vm.network :private_network, ip: "10.0.0.21"
pxeclient.vm.provider :virtualbox do |vb|
vb.memory = "6148"
vb.customize ["modifyvm", :id,
"--natdnshostresolver1", "on"]
vb.customize [
'modifyvm', :id,
# переопределяем тип сети (по умолчанию host-only на intnet) и указываем её название (будет работать dhcp сервер этой сети, а не vbox)
'--nic1', 'intnet',
'--intnet1', 'pxenet',
#сеть для выхода в интернет через хост
'--nic2', 'nat',
# указываем загрузку ОС по сети 
'--boot1', 'net',
'--boot2', 'none',
'--boot3', 'none',
'--boot4', 'none'
]
vb.customize ["modifyvm", :id,
"--natdnshostresolver1", "on"]
end
end
end
