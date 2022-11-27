# Vagrant Basic Tutorial

1. Install vagrant  
    Buka https://developer.hashicorp.com/vagrant/downloads. Pilih sesuai OS yang anda gunakan. Ikuti prosesnya langkah demi langkah. Sampai selesai.

    Ketik perintah di bawah untuk memastikan apakah dia sudah berhasil terinstall.
    ```
    vagrant -h
    ```

2. Buat direktori kerja
    ```
    mkdir work && cd work
    ```

3. Buat `Vagrantfile`
    ```
    vagrant init hashicorp/bionic64
    ```

    `init` : digunakan untuk membuat file `Vagrantfile` yang berisi konfigurasi bawaan (default) virtual machine.

    `hashicorp/bionic64` : nama virtual machine yang tersedia di https://app.vagrantup.com/hashicorp/boxes/bionic64.
 
4. Start the Vagrant VM 
    ```
    vagrant up
    ```
    Perintah ini digunakan untuk memulai dan menyediakan virtual machine sesuai yang tertera di `Vagrantfile`.

5. SSH the Vagrant VM
    ```
    vagrant ssh
    ```
    Perintah ini digunakan untuk masuk ke dalam virtual machine. Ketik control-d untuk keluar dari SSH.

    Ketik perintah dibawah untuk menghapus VM.
    ```
    vagrant destroy
    ```

6. Enable provisioning with a shell script  
    - Buka `Vagrantfile`. 
    - Cari bagian `config.vm.provision`
    - Hilangkan komen dan tambahkan perintah berikut:
        ```ruby
        # Enable provisioning with a shell script. Additional provisioners such as
        # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
        # documentation for more information about their specific syntax and use.
        config.vm.provision "shell", inline: <<-SHELL
            apt-get update
            apt-get install -y apache2
            service apache2 start
            service apache2 status
        SHELL
        ```
        Konfigurasi ini dipakai untuk menginstall paket dan menjalankan perintah yang tersedia di sistem operasi.

7. Test apache webserver
    - Jalankan: `vagrant up`
    - Masuk ke SSH: `vagrant ssh`
    - Test apache webserver: `curl localhost`
    - Keluar dari ssh dengan control-d.
    - Hapus VM: `vagrant destroy`

8. Networking  
    Cari dan hilangkan komentar bagian-bagian ini:
    ```ruby
    # Create a forwarded port mapping which allows access to a specific port
    # within the machine from a port on the host machine. In the example below,
    # accessing "localhost:8080" will access port 80 on the guest machine.
    # NOTE: This will enable public access to the opened port
    config.vm.network "forwarded_port", guest: 80, host: 8080
    # Create a private network, which allows host-only access to the machine
    # using a specific IP.
    config.vm.network "private_network", ip: "192.168.33.10"
    # Create a public network, which generally matched to bridged network.
    # Bridged networks make the machine appear as another physical device on
    # your network.
    config.vm.network "public_network"
    ```

    Dengan konfigurasi di atas, kamu dapat mengakses apache webserver dengan membuka http://192.168.33.10 dari browser.

9. Mengganti Memori dan CPU  
    Cari `config.vm.provider` dan update memori dan CPU:
    ```ruby
    # Provider-specific configuration so you can fine-tune various
    # backing providers for Vagrant. These expose provider-specific options.
    # Example for VirtualBox:
    config.vm.provider "virtualbox" do |vb|
        vb.memory = 1024
        vb.cpus = 1
    end
    ```

10. Recreate VM after update
    - Jangan lupa untuk menjalankan `vagrant destroy` setelah melakukan perubahan di `Vagrantfile`.
    - Setelah itu jalankan `vagrant up` lagi untuk provisioning.
    - Bisa juga dengan menjalankan `vagrant reload`.
