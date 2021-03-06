# VirtualBox

In order to use VirtualBox to run Jupyter Lab you will need to:

- Download and install [VirtualBox](https://www.virtualbox.org/)
- Download the `.ova` file containing the Virtual Machine (this is a large file, in excess of 6GB) *Link to follow...*

From here on we will refer to the OVA file as your VM (short for: Virtual Machine) since it is a fully-functioning operating system in a single file!

### Known Issues

#### Windows Issues

Windows 10 Home presents particular challenges for *all* kinds of 'virtualisation' (running an operating system on 'top' of Windows). There are two *potential* issues:

1. The 'subsystem' has not been installed to enable you to run Virtual Machines.
2. The BIOS of the computer itself has not been configured to allow you to run Virtual Machines.

Both of these are resolveable, but both present risks to your computer and you should be aware of these before proceeding. Alternatively, you may wish to [install Anaconda Python directly](../conda/README.md) though this presents other challenges... 

#### Mac Issues

If you are going over this process on a modern Mac, you will need to allow VirtualBox access rights when it requests them.

Additionally, you might encounter the following error when importing the appliance:

```
Nonexistent host networking interface, name '' (VERR_INTERNAL_ERROR).

Result Code:
NS_ERROR_FAILURE (0x80004005)

Component:
ConsoleWrap

Interface:
IConsole {872da645-4a9b-1727-bee2-5585105b9eed}
```

This has to do with security settings of macOS. To work around the issue, follow these steps:

1. From the main menu, select "File > Host Network Manager". You should see an empty white box with "Host-only Networks".
2. Click the "Create" button. A new Host-only network will be created and added to the list automatically.

### Installation

There are three main steps to follow:

1. [Import the appliance (`.ova` file)](#Appliance-import)
2. [Forward the required port to the host](#Port-forwarding)
3. [Set up a shared folder so the VM can see files in your host
   machine](#Folder-sharing)

Mac users, please check the section on [known issues](#Mac-known-issues).

It's important to note that you only need to run these three steps *once* when setting the VM up in a machine for the *first time*. After this, [launching and running the VM](#Running-the-VM) is a one-click job.

### Appliance import

1. Go to "File --> Import Appliance..."
1. Select the `.ova` file that you have downloaded. The import process might take a couple of minutes, but you do not have to do anything

### Port forwarding

The VM will be accessed through your browser. To be able to connect from the host, the port 8888 needs to be able to reach the VM. Here is how you can do it:

1. Right click on the `{name}` image and then left-click on "Settings"
2. Go to the "Network" tab and click on "Advanced"
3. Click on "Port Forwarding", this will open up a new dialog window
4. On the right side, click on "Add new port" button, the one with a + sign.
   If you hover your mouse over, it will read "Adds new port fordwarding rule". This will add a new row in the table
5. In the new row, type  "jupyter" under "Name", leave "Protocol", "Host IP" and "Guest IP" as they are, and enter 8888 under both "Host Port" and "Guest Port".
6. You may see two network cards listed, in which case the one that is *not* set to NAT should be deactivated. 
7. Then click OK on the bottom right part of the dialog window

### Folder sharing

The following steps allow you to select a folder on the host that will be accessible from the VM.

1. Right click on the Virtual Machine in VirtualBox and left-click on "Settings"
2. Go to the "Shared Folders" tab
3. Click on the button that has a folder with a + sign icon. If you hover your mouse it will read "Add new shared folder" in the top right
4. Click on "Folder Path" and select "Other" from the folder path dropdown
5. Point to the folder you want to share with the VM
6. Use "notebooks" (or similar) under the "Folder Name" box
7. Leave "Mount point" blank and make sure "Read-only" is *not* checked
8. Click OK.

### Display

Finally, it's useful to enable the Display Adapter fully for contemporary machines:

1. Right click on the Virtual Machine in VirtualBox and left-click on "Displays"
2. Select the "Screen" tab.
3. Increase the Video Memory to, say, 16MB
4. Change the Graphics Controller to "VMSVGA"
5. Click OK.

## Running the VM

Once the steps above are followed, you are good to go. To start a session,
follow these steps:

1. Select `{name}` on the VirtualBox window
2. On the top row, click on "Start"
3. A new window will launch with a black console that will start printing output out. This should continue for about 30 seconds, depending on your laptop
4. When the startup is complete, you should see a [Texas longhorn](https://en.wikipedia.org/wiki/Texas_Longhorn) drawn on the console. Everything is ready.
5. Open a browser (Firefox or Chrome preferably) and point it at: `localhost:8888`
6. A page will load, asking you to enter a password. Use `casa2021`.
7. The main JupyterLab interface should load, happy hacking!

### Shared files

The file menu on the left side of JupyterLab should display a single folder named `work`. This is the bridge to the host. If you double click on it, it will display the files in the folder you have decided to share with VirtualBox.

## Building a New VM

There are two stages:

1. [Provisioning](#Provisioning)
2. [Deployment](#Deployment)

#### Sources

This approach builds on a [Towards Data Science blog post](https://towardsdatascience.com/sharing-data-visualisations-in-virtualbox-to-keep-it-departments-happy-f978854ea44d) adapted by [Dani Arribas-Bel](https://github.com/darribas/gds_env/blob/master/virtualbox/README_docker-machine.md), with some further tweaks for our use for which the key references are:

- [Base Documentation](https://rancher.com/docs/os/v1.x/en/)
  - [Quick-Start Guide](https://rancher.com/docs/os/v1.x/en/quick-start-guide/)
- [Configuration](https://rancher.com/docs/os/v1.x/en/configuration/)
  - [Writing Files](https://rancher.com/docs/os/v1.x/en/configuration/write-files/)
  - [Running Commands](https://rancher.com/docs/os/v1.x/en/configuration/running-commands/)
  - [Sysctl](https://rancher.com/docs/os/v1.x/en/configuration/sysctl/) and [System Services](https://rancher.com/docs/os/v1.x/en/system-services/)

### Requirements

You will need the following:

- [VirtualBox](https://www.virtualbox.org/)
- [Docker](https://www.docker.com/)
- [Docker-Machine](https://docs.docker.com/machine/)
- The [Cloud Config YAML file](cloud-config-additions.yml)

**NOTE**: Where you see `{name}` replace this with the desired machine name that you would want to 'expose' to VirtualBox (e.g. `SDS` or `gdsbox`).

### Create a VM

Set up a VM using [RancherOS](https://rancher.com/rancher-os/) using the instructions for [docker-machine](https://rancher.com/docs/os/v1.x/en/installation/workstation/docker-machine/):

```shell
export BOX_NM='sds2020'
docker-machine create -d virtualbox \
      --virtualbox-boot2docker-url https://releases.rancher.com/os/latest/rancheros.iso \
      --virtualbox-memory 2048 \
      $BOX_NM
```

If you can safely assume higher-performance machines then increasing the memorty to 4096 would improve performance.

You can check this has happened using:

```shell
VBoxManage list vms
```

And should see the machine named `{name}` listed.

### Provision the VM

Now you need to make a choice about how to 'pull' the docker image that you want to distribute; there are two choices:

1. Pre-install the docker image so that the VM is 'ready to run' once it has been downloaded and imported into VirtualBox (this entails a larger download but fewer opportunities for things to go _wrong_); 
2. Use a start-up script in the VM to pull the docker image if it is not found on the VM when it starts up (this shifts more of the bandwidth burden on to the end-user via a smaller initial download with the rest downloaded at user's convenience).

Either approach should work, however, it's hard to get output to the user when starting up RancherOS so it's **probably best to go with Option #1** so that the user isn't left wondering what's going one while you pull 6.5GB of data down and launch the OS. With that in mind, the only line that you would need to skip from the below configuration process is:

```bash
docker pull $IMAGE_NM
```

In principle, the way that `cloud-config-additions.yml` is written means that if it can't find the named Docker image it will pull in anyway. So you're really just skipping a step that is user-unfriendly when you pre-load the image.

To provision the VM you need to run the commands below to populate an `rc.local` script. Note that `rc.local` cannot be run from the Terminal successfully. It can *only* run during start-up. Below I have tried to parameterise the setup process such that `IMAGE_NM` refers to the version of the docker image that you want (e.g. `jreades/sds:1.0`) while `BOX_NM` refers to the name of the VM as it will appear in the VirtualBox app and `JUPYTER_PWD` is the SHA1-encrypted password to use with JupyterLab on launch.

You can generate a JupyterLab-compatible password as follows (**Note** that you will need to do this on a computer with the notebook library installed!):

```bash
export PWD='casa2021'
python3 -c "from notebook.auth import passwd; print(passwd('$PWD'))"
```

Anyway, here's how to provision:

```shell
# ----------------------
# This part runs on *your* machine (a.k.a. the host)
# ----------------------
export BOX_NM='sds2020'
docker-machine scp ./cloud-config-additions.yml $BOX_NM:
docker-machine ssh $BOX_NM -t

# ----------------------
# This part runs on the Virtual Machine (a.k.a. the guest)
# The ssh command above logged you on to the running VM.
# ----------------------
export IMAGE_NM='jreades/sds:0.6.6'
export JUPYTER_PWD='sha1:288f84f833b0:7645388b889d84efbb2716d646e5eadd78b67d10'

# Install and configure virtualbox-tools
sudo ros service enable virtualbox-tools
sudo ros service up virtualbox-tools
# Make backup of cloud-config.yml in case something goes wrong.
# Then append our docker config/launch service to the YAML file
# Note: if you're trying to set up your own Docker image then y
# you will need to adjust the IMAGE_NM and JUPYTER_PWD vars above!!!
# cat cloud-config-additions.yml | sed 's,{IMAGE_NM},'"$IMAGE_NM"','
sudo sh -c 'cp "/var/lib/rancher/conf/cloud-config.yml" ./cloud-config.yml'
sudo sh -c "cat \"/home/docker/cloud-config.yml\" \"/home/docker/cloud-config-additions.yml\" | sed 's,{IMAGE_NM},'"$IMAGE_NM"',' | sed 's,{JUPYTER_PWD},'"$JUPYTER_PWD"',' > /var/lib/rancher/conf/cloud-config.yml"
# And pull the Docker image that we want.
docker pull $IMAGE_NM
```

Running `docker pull` may take some time (and should not be done over a mobile connection) but you can then shut down the machine:

```shell
exit
docker-machine stop $BOX_NM
```

### Export the VM

This virtual machine is still only accessible on the host machine, so we need to export it into a format that allows us to distribute it more widely:

```shell
VBoxManage export $BOX_NM --iso -o $BOX_NM.ova
```

