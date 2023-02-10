<div align=center>
<img src="images/zano_icon.png" width="400">
<h1>Solo Mining with AMD (Ubuntu)<h1>
</div>

## Step 1: AMD Driver Installation

Since Ubuntu doesn't come with AMD's OpenCL driver, which is necessary for mining, you'll need to install that. But it also requires ****very**** specific AMD driver software (22.40): we've found no other that successfully installs. 

1. First, we must get the driver installer itself. Go to your Downloads directory and give the following commands:
   
   ```
   sudo apt update
   sudo apt upgrade
   ```
   
   a) If running Ubuntu 20.04 LTS:
   
   ```
   wget https://repo.radeon.com/amdgpu-install/22.40/ubuntu/focal/amdgpu-install_5.4.50401-1_all.deb
   ```
   
   b) If running Ubuntu 22.04 LTS or 22.10:
   
   ```
   wget https://repo.radeon.com/amdgpu-install/22.40/ubuntu/jammy/amdgpu-install_5.4.50401-1_all.deb
   ```
   
   Now, if you already have any AMD drivers installed ****other than 22.40****, you'll need a few extra steps, provided in the following footenote [^1]. 
   
   If there are no other AMD drivers on your rig, proceed to install the driver installer and enable the proprietary repository: 
   
   ```
   sudo apt install ./amdgpu-install_5.4.50401-1_all.deb
   sudo sed -i 's/#deb/deb/g' /etc/apt/sources.list.d/amdgpu-proprietary.list  
   ```

2. If everything went smoothly, the driver can now be installed:
   
   ```
   amdgpu-install --opencl=legacy,rocr --usecase=workstation,graphics --no-32
   ```
   
   If successful, reboot:
   
   ```
   sudo reboot
   ```

3. To determine whether the driver was properly installed, we need the application ****clinfo****. Install that, and then check for your GPU: [^2]
   
   ```
   sudo apt install clinfo
   sudo clinfo
   ```
   
   You should see something like the following, with OpenCL under both "Platform version" and "Device version."
   
   <div>
   <img src="images/amd-clinfo.png" width="800">
   </div>

## Step 2: Install & Run Wildrig Miner

Currently, Wildrig is the only miner compatible with AMD GPUs. With Wildrig, however, direct solo mining isn't possible. We'll thus be mining through Newpool, which has a solo mining option. [^3]

Create a directory for Wildrig within your main Zano directory, and enter it:

```
mkdir wildrig
cd wildrig
```

Within it, download Wildrig:

```
wget https://github.com/andru-kun/wildrig-multi/releases/download/0.36.6b/wildrig-multi-linux-0.36.6b.tar.xz
```

Extract and then remove original .tar file:

```
tar -xf wildrig-multi-linux-0.36.6b.tar.xz
rm wildrig-multi-linux-0.36.6b.tar.xz
```

You're ready to begin mining. Give the following command in your `wildrig` directory, substituting your own address for "wallet_address":[^4]

```
sudo ./wildrig-multi --print-full --algo progpowz -o stratum+tcp://minenice.newpool.pw:1287 -u solo:wallet_address.ZanoSolo -p x
```

The miner should start, displaying something like this:

<div>
<img src="images/amd-wildrig_running.png" width="800">
</div>

Now go to [Newpool](https://newpool.pw/zano/#worker_stats) and enter your wallet address; you should see your miner stats:

<div>
<img src="images/amd-newpool_solo_mining.png" width="800">
</div>

<div>
<h1>Congratulations, you're solo mining Zano!<h1>
</div>

[^1]: If you already have any AMD driver installed other than 22.40, you'll need to here take the following steps. Uninstall driver: `amdgpu-install --uninstall`. Install the debian package (i.e., what would have been your next step above): `sudo apt install ./amdgpu-install_5.4.50401-1_all.deb`. Give: `sudo apt update` & `sudo apt autoremove`. You can now proceed to enabling the AMD proprietary repository. 

[^2]: If you don't include `sudo` in this command, it's likely that your GPU will not be displayed under devices.

[^3]: Be aware that Newpool has a 1.0% pool fee.

[^4]: Note again the need to give `sudo` here. Also, the port 1287 is used here, which is for high end graphics cards such as RX 470, 480, 570, 580, VEGA 56/64 and better. Port 1157 should be used for mid- and low-grade GPUs such as RX 460, 550, and 560.