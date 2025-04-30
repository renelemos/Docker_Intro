> [!TIP]
> First steps with Docker? You found the right repository.

## Step 1 - How to start your operation with Docker
1 - Install Oracle VirtualBox at https://www.virtualbox.org/wiki/Downloads (remember to choose according to your OS (Windows, macOS, Linux)

2 - Install Vagrant at https://developer.hashicorp.com/vagrant/install (remember to choose according to your OS (Windows, macOS, Linux)

3 - Create a new folder called "Vagrant"

4 - Open Visual Studio Code, then Open your "Vagrant" Folder in the "Explorer" section.

5 - Create a new terminal
6 - Write in your terminal: 
```bash
vagrant init -m ubuntu/bionic64
```
and press enter - this will place a "Vagrantfile" in your "Vagrant" directory.

7 - Write in your terminal:
```bash
vagrant up
```
and press enter - this will bring the virtual machine up to the 'virtualbox' provider. (if you wish you can see it being created by opening your Oracle Virtual Box executable).

8 - In case in want to destroy this virtual machine, you can just write in your terminal:
```bash
vagrant destroy
```
and then it will force the shutdown of your virtual machine and destroy it and its associated drives.

Good. You already created and destroyed your first virtual machine.   
### Congratulations!

## Step 2 - Customizing your virtual machine
Now let's customize some of the configurations for this virtual machine.

If you created the vagrantfile in your directory, it should be something like this:

```python
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
end
```

Now you can enhance its configuration by adding some information regarding to it.
The first thing we will do is to create a name for the vm.provider. Let's call it "vb" (But you could call it anything, it doesn't need to be necessarily vb, okay?)

```python
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.provider = "virtualbox" do |vb|
end
```

Now let's config the memory and the CPUs in case the virtual machine provider is "virtualbox". We will consider the memory as being 1024 MB and the CPUs as having 2 processors.
We will also include another "end", since we used "do" to start the block.   
>[!NOTE]  
"Do" - "end" is just a Ruby syntax for a block, which is a chunk of code that you pass to a method.  
In Ruby, this block syntax is a core part of the language and is heavily used in DSLs like Vagrantfiles.

```python
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.provider = "virtualbox" do |vb|
    vb.memory = 1024
    vb.cpus = 2
  end
end
```

Now if you create the virtual maching again by using **vagrant up** you will see that it has now 1024 MB and 2 processors.
![image](https://github.com/user-attachments/assets/580a1584-0123-4b08-bb90-4fbf10923097)

## Step 3 - Exploring the `.vagrant` Folder Created by `vagrant up`
![image](https://github.com/user-attachments/assets/03fbe794-c82b-4cc2-9439-485aeedbc483)

After you run `vagrant up` in your terminal, a `.vagrant` folder is created in your project directory.  
This folder contains internal Vagrant data, such as the current state of your virtual machines and provider-specific details (e.g., the VirtualBox VM ID).
Another important file inside this folder is the `private_key`, which allows you to connect to the virtual machine via SSH.

Now that you know where the `private_key` is located, let’s see what you can do with it.  
The easiest way to access your virtual machine is by running the following command in your terminal:

```bash
vagrant ssh
```

When you run `vagrant ssh`, Vagrant uses the `private_key` inside the `.vagrant` folder to automatically connect to your virtual machine via SSH.  
You’ll be logged directly into the VM's shell, usually as the `vagrant` user, without needing to type a username or password.

This lets you interact with the VM as if it were a regular remote Linux server — you can navigate the file system, install packages, run scripts, and more.

>[!Tip]
> If you wish to display the Linux distribution and version information of the system you're connected, you can do the just run the below command in your terminal:
>```bash
>cat /etc/*release

If you wish to logout from this server, you can just run the below command in your terminal:
```bash
exit
````

If you check how this ssh conection is configured, you can just run the below command in your terminal:
```bash
vagrant ssh-config
````
In other words, it displays the SSH configuration details that Vagrant is using to connect to the VM, including the private key location, user, and other connection details.

Ok. Now you have the commands to check the system details, log out, and examine the SSH configuration for your Vagrant-managed VM.
If you wish to **turn off** the virtual machine without destroying it (so you can restart it later), you can run the following command in your terminal:

```bash
vagrant halt
```

If you're unsure whether the machine is turned on or off, you can easily check by running the following command in your terminal:

```bash
vagrant status
```

Ok. That will be all for Step 3. Now let's move ahead to Step 4.

## Step 4 - Defining multiple instances in a single Vagrantfile (defining multiple Virtual Machines)

First of all, let's start from the beginning.  
**If** you already have any existing VMs running, you can destroy them to ensure a clean start. Run the following command in your terminal to destroy the current VMs:

```bash
vagrant destroy -f
```
This will remove all VMs defined in your Vagrantfile. Now you're ready to create two new virtual machines.

Let's now edit our Vagrantfile to define these two new VMs:

```ruby
Vagrant.configure("2") do |config|
  # First VM
  config.vm.define "vm1" do |vm1|
    vm1.vm.box = "ubuntu/bionic64"
    vm1.vm.provider "virtualbox" do |vb|
      vb.memory = 1024
      vb.cpus = 2
    end
  end

  # Second VM
  config.vm.define "vm2" do |vm2|
    vm2.vm.box = "ubuntu/bionic64"
    vm2.vm.provider "virtualbox" do |vb|
      vb.memory = 1024
      vb.cpus = 2
    end
  end
end
```

Great. Please check the tip below regarding to managing multiple instances.  
> [!Tip]
> Managing Multiple Instances:  
> In case you defined the VMs names as being "vm1" and "vm2":  
> **Starting all VMs**: Run vagrant up to start all VMs defined in the Vagrantfile.  
> **Starting a specific VM**: Run vagrant up vm1 to start only vm1.  
> **Stopping a specific VM**: Run vagrant halt vm1 to stop only vm1.  
> **Checking status**: Run vagrant status to see the status of all defined VMs.
> **Check the distribution and version information of the vm1**: vagrant ssh vm1 -c "cat /etc/*release"

If you want your Vagrant VMs (like vm1 and vm2) to communicate with each other, the best way is to configure private network interfaces.  
This gives each VM its own static IP on an isolated network, so they can "see" each other but aren't exposed to the wider internet.

For this to happen, you can just update your Vagrantfile as follows:

```ruby
Vagrant.configure("2") do |config|
  # First VM
  config.vm.define "vm1" do |vm1|
    vm1.vm.box = "ubuntu/bionic64"
    vm1.vm.network "private_network", ip: "192.168.56.11"
    vm1.vm.provider "virtualbox" do |vb|
      vb.memory = 1024
      vb.cpus = 2
    end
  end

  # Second VM
  config.vm.define "vm2" do |vm2|
    vm2.vm.box = "ubuntu/bionic64"
    vm2.vm.network "private_network", ip: "192.168.56.12"
    vm2.vm.provider "virtualbox" do |vb|
      vb.memory = 1024
      vb.cpus = 2
    end
  end
end
```
> [!TIP]
> **Make sure the IPs are on the same subnet and don’t conflict with other devices on your network.**
>
> | Term          | Meaning                                                                 |
> |---------------|-------------------------------------------------------------------------|
> | **Subnet**    | A block of IPs within a private network, like `192.168.56.0/24`         |
> | **IP Used**   | `192.168.56.12` was manually assigned within that subnet                |
> | **Communication** | VMs on the same subnet can communicate directly with each other |

## Where does the IP 192.168.56.12 come from?
This IP was not automatically assigned; we manually chose it when configuring the network with.  
This range (192.168.x.x) is a private range (reserved for internal networks), and 192.168.56.x is very common in VirtualBox, as it automatically creates an internal subnet called vboxnet0 within this range.  
The IP 192.168.56.12 that you configured in your Vagrantfile will only work on your local machine while you're using this configuration.  
In other words, only the VMs you create with this Vagrantfile and that are on the same network (with the subnet 192.168.56.x) will have this IP...

> [!Note]
> 192.168.x.x: Common private range, mostly used for small or home networks.  
> 10.x.x.x: Larger private range, ideal for larger networks or to provide more flexibility in a network environment.  
> If you have the freedom to choose, both IPs will work well in a private environment like VirtualBox or Vagrant.  
> You can use 10.x.x.x for consistency and flexibility. Example: 10.10.10.11

To check if the IP address was correctly assigned to the network interface of your virtual machine, run the following command:

```bash
vagrant ssh vm1 -c "ip -c a show enp0s8"
```
This command will return the IP address assigned to the enp0s8 interface on vm1.  
It's useful when you're trying to verify which IP the VM is using on the private network, especially if you want to make sure that both VMs are on the same subnet and can communicate with each other.  
(To check vm2, just change the command above from **vagrant ssh vm1 -c "ip -c a show enp0s8"** to **vagrant ssh vm1 -c "ip -c a show enp0s8"**.

The interface **enp0s8** is specific to Ubuntu (and some other Linux distros) due to the Predictable Network Interface Names.  
If you are using a different Linux distribution, the interface name might be something else, like **eth0** or **ens33**.

## Step 5 - Provisioning with Shell in Vagrant
Shell provisioning in Vagrant allows you to automatically configure your virtual machines with customized settings. By using the Shell provisioner, you can install packages, configure services, run commands, and do much more, all in an automated way. This ensures that every time you run vagrant up, your VM will be set up the way you want it, saving you time and effort!

## Why Use Inline Shell Provisioning?

**Simplicity**: You can include simple commands directly in the Vagrantfile without needing to create separate script files.
**Speed**: Ideal for quick setups and simple provisioning, where you only need to run a few basic commands.
**Automation**: With inline provisioning, you can automate the setup of your development environment efficiently, without manually configuring the VM each time.

Let's say we would like to install Nginx, Git, and Python on the VM every time it’s brought up with vagrant up.
We just need to open the Vagrantfile again and add the following provisioning block.

```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"

  # Provisioning with Shell
  config.vm.provision "shell", inline: <<-SHELL
    # Updating the package list
    sudo apt-get update

    # Installing Nginx, Git, and Python
    sudo apt-get install -y nginx git python3 python3-pip

    # Starting Nginx
    sudo systemctl start nginx
    sudo systemctl enable nginx

    # Checking the status of Nginx
    systemctl status nginx

    # Verifying Python installation
    python3 --version
    pip3 --version
  SHELL
end
```

## What’s Happening in the code above
1. Update the Package List:  
The first command, sudo apt-get update, ensures that your package list is up to date before installing any software. This is a common step to ensure you get the latest available versions.  
  
2. Installing Nginx, Git, and Python:  
Here, we are installing:  
**Nginx**: A popular web server.  
**Git**: A version control system for managing code.  
**Python**: A widely-used programming language (Python 3 and pip, the package manager for Python).  

3. Start and Enable Nginx:
After installing Nginx, we start the service with systemctl start nginx, which will allow Nginx to begin serving requests.
We also enable it with **systemctl enable nginx**, so Nginx will automatically start each time the VM boots.

4. Check Nginx Status:
The command **systemctl status nginx** shows the current status of the Nginx service, ensuring it's running properly.

5. Verify Python Installation:
Finally, we check the versions of Python and pip by running:
**python3 --version**: This confirms that Python 3 was installed successfully.
**pip3 --version**: This confirms that pip (the Python package manager) was installed.

After you save the Vagrantfile, run the following command in your terminal and Vagrant will automatically execute the provisioning script, and it will:  
1. Install Nginx, Git, and Python inside your VM.  
2. Start Nginx so you can use it right away.  
3. Check if Python and pip were successfully installed.  

## Using External Shell Scripts
If you have a more complex shell script, you can also use an external script instead of inline commands.
Here’s an example:
```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"

  # Provisioning using an external shell script
  config.vm.provision "shell", path: "scripts/setup.sh"
end
```
In this case, Vagrant will execute the setup.sh script located in the scripts folder in your project, setting up the VM according to what’s inside that script.

## Step 5 - The finish line

Here's the final wrap-up:

Congratulations on completing the tutorial! 
You've successfully learned how to set up, customize, and provision virtual machines with Vagrant. Here’s a quick recap of what we covered:

1. Installing VirtualBox and Vagrant:
2. Creating a Virtual Machine
3. Destroying a VM
4. Configuring VM Resources
5. Vagrantfile Customization
6. Understanding SSH
7. Connecting to the VM
8. Creating Multiple VMs
9. Private Network Setup
10. Using Inline Shell Provisioning
11. Why Use Shell Provisioning

## Next Steps

Testing Your Setup: 
Now that your VM is configured with the necessary tools, you can start developing applications or test configurations within the VM.  
You can use Nginx to serve web pages or use Git for version control.

Extending Provisioning:  
As your needs grow, you can easily extend the provisioning script to install additional software or configure more advanced settings.

Networking Between VMs:  
You can explore setting up communication between your VMs (like sharing files or creating a small distributed system) by using more advanced networking setups.

## Conclusion
You've gained essential skills in setting up and managing virtual machines with Vagrant and VirtualBox. 
With these tools in hand, you're now ready to work in isolated development environments that can be easily shared and replicated.  
Vagrant will save you time when setting up similar environments and make your workflows more efficient and consistent.
Good luck with your projects! Feel free to revisit this guide anytime you need to refine or extend your virtual machine setups.
