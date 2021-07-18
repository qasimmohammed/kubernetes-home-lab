# kubernetes-home-lab
Playing about installing Kuberenetes on some Raspberry Pi's

Downloading and ammeding Ubuntu
	1. Download Ubuntu 20.04.2.0 LTS (Focal Fossa)
	''' wget "https://releases.ubuntu.com/20.04/ubuntu-20.04.2-live-server-amd64.iso" '''
	2. Get relevant information from ISO
	''' sudo ap-get update -y '''
	''' sudo apt-get install -y fdisk '''
	''' sudo fdisk -l ubuntu-20.04.2-live-server-amd64.iso '''
	3. Using the output of the Above, we need to calculate the start of the ISO using Offset calculation: start * sector size => 0 * 512 => 0
	''' mkdir ~/mnt '''
	''' sudo mount -o loop,offset=0,ubuntu-20.04.2-live-server-amd64.iso  ~/mnt '''
	''' ls ~/mnt '''
	4. Install some binaries to assist with chmod
	''' sudo apt-get install -y qemu-user-static '''
	''' systemctl restart systemd-binfmt.service '''
	5. Check everything is working
	''' sudo chroot ~/mnt/ /usr/bin/uname -a -r '''
	6. Making changes to image: Install and enable SSHD, Set up a local user,Allow the local user to use sudo (without a password, optional),Add authorized keys, Allow root to SSH with the authorized keys (optional) 
	''' sudo chroot ~/mnt /bin/bash '''

	    # Install openssh-server and enable it
	''' sudo apt-get install -y openssh-server '''
	''' sudo systemctl enable sshd.service '''

	   # Allow root to SSH with your authorized keys
	''' mkdir /root/.ssh '''

	   # Download, or otherwise add to the authorized_keys file, your public keys
	   # Replace the URL with the path to your own public keys
	''' curl https://github.com/qasimmohammed.keys -o /root/.ssh/authorized_keys '''
	''' sudo chmod 700 /root/.ssh '''
	''' sudo chmod 600 /root/.ssh/authorized_keys '''

	   # Add a local user, and put them in the wheel group
	   # Change the group and user to whatever you desire
	''' groupadd qasim '''
	''' useradd -g qasim -G wheel -m -u 1000 qasim '''

	   # Download or add your authorized keys
	   # Change the homedir and URL as you've done above
	''' mkdir /home/qasim/.ssh '''
	''' curl https://github.com/qasimmohammed.keys -o /home/qasim/.ssh/authorized_keys '''
	''' chmod 700 /home/qasim/.ssh '''
	''' chmod 600 /home/qasim/.ssh/authorized_keys '''
	''' chown -R qasim.qasim /home/qasim/.ssh/ '''

	   # Allow the wheel group (with your local user) to use suso without a password
	''' echo "%wheel ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/91-wheel-nopasswd '''

	7. Deactivate the initial set up process as it will over write our changes
	   # Disable the initial-setup.service for both the multi-user and graphical targets
	''' unlink /etc/systemd/system/multi-user.target.wants/initial-setup.service '''
	''' unlink /etc/systemd/system/graphical.target.wants/initial-setup.service '''

	8. Unmount and compress file
	''' sudo umount ~/mnt/ '''
	
	   # Compress the raw disk image to a .xz file, but keep the raw disk image
	''' xz --compress  ubuntu-20.04.2-live-server-amd64.iso --keep '''

	9. Install the image on the SD Card
	# Use arm-image-installer to copy the modified disk image to the SD card
	''' arm-image-installer --image=ubuntu-20.04.2-live-server-amd64.iso.xz --target=rpi3 --media=/dev/sdc --norootpass --resizefs -y '''



Installing and configuring Kubernetes
	1.
	2.
	3.


Sources
https://opensource.com/article/20/5/disk-image-raspberry-pi
https://opensource.com/article/20/6/kubernetes-raspberry-pi

