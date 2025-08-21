# **OTOMATISASI DEPLOYMENT NEXTCLOUD HIGH AVAILABILITY dengan ANSIBLE: INTEGRASI SSL dan DNS SERVER**

**Halo perkenalkan saya Reihan Maulana disini saya membagikan cara menginstall dan mengkonfigurasi Nextcloud menggunakan ansible**

## **First Step**
Pertama siapkan 4 vm dan atur network interface nya menjadi nat dan host-only adapter dan pastikan sudah terinstall sudo dan ssh.
jika belum menginstall sudo dan ssh anda dapat install telebih dahulu dengan menggunakan
```
apt install sudo openssh-server -y
```
lalu buat user baru:
```
sudo adduser admin
```
lalu tambahkan user baru itu ke group sudo:
```
sudo usermod -aG sudo admin
```
lalu ubah konfigurasi sudo agar user "admin" dapat menggunakan sudo
```
sudo visudo
```
dan ubah dan tambahkan bagian ini
```
# User privilege specification
root    ALL=(ALL:ALL) ALL
admin   ALL=(ALL:ALL) NOPASSWD: ALL

# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) NOPASSWD: ALL
```
lalu ubah hostname dan hosts nya (optional)
```
sudo nano /etc/hostname
sudo nano /etc/hosts
```
lalu ubah ip interface host-only adapter setiap mesinnya
| Name | IP |
| --- | --- |
| Ansible Control Node | 192.168.1.10/24 |
| DNS & SSL Server | 192.168.1.11/24 |
| Webserver | 192.168.1.12/24 |
| Database | 192.168.1.13/24 |
```
sudo nano /etc/network/interfaces
```
dan jika sudah di ubah ip nya restart
```
sudo systemctl restart networking
```

## **Second Step**
Masuk ke mesin Ansible Control Node lalu buat ssh-key dan copy key nya ke setip mesin
```
ssh-keygen -t rsa
ssh-copy-id admin@192.168.1.10
ssh-copy-id admin@192.168.1.11
ssh-copy-id admin@192.168.1.12
ssh-copy-id admin@192.168.1.13
```
lalu install ansible pada mesin ini
```
sudo apt install ansible sshpass -y
```
setelah itu git clone projek ini dengan mengetik
```
git clone https://github.com/reisukacommit/projek-nextcloud.git
```

## **Final Step**
masuk ke direktori projke-nextcloud
```
cd projek-nextcloud/
```
lakukan ping pada setiap target host
```
ansible all -i hosts -m ping
```
setelah semuanya menjawab pong lakukan run playbook
```
ansible-playbook -i hosts site.yml
```
