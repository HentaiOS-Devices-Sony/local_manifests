# Hentai OS for Sony Xperia devices

### this is for building Hentai OS for the following Sony Xperia Devices

```bash
Xperia XZ1
Xperia XZ1 Compact
Xperia XZ Premium
Xperia XZ2
Xperia XZ2 Compact
Xperia XZ3
Xperia XA2
Xperia XA2 Plus
Xperia XA2 Ultra
Xperia 10
Xperia 10 Plus
Xperia 1
Xperia 5
```

### How to Setup the Environment & Sync & Build

#### Setup the Environment

````bash
# Prepare your Java environment
sudo apt-get update
sudo apt-get install openjdk-8-jdk

# Install the necessary tools to make an Android build
sudo apt-get install bison g++-multilib git gperf libxml2-utils make zlib1g-dev zip liblz4-tool libncurses5 libssl-dev bc flex

# Download Repo tool and Set PATH
mkdir ~/.bin
curl http://commondatastorage.googleapis.com/git-repo-downloads/repo > ~/.bin/repo  
    ## For Chinese Users, You Can Use The Repo Tool Provide By Mokee
    curl https://raw.githubusercontent.com/MoKee/git-repo/stable/repo > ~/bin/repo

chmod a+x ~/.bin/repo
sudo nano ~/.bashrc ## Open .bashrc with Nano
export PATH=~/bin:$PATH ## Add to the last line of .bashrc And save the file
source ~/.bashrc
````

#### Sync the codes

```bash
# Initialize local repository
repo init -u https://github.com/HentaiOS/platform_manifest -b queenslave
cd .repo
git clone https://github.com/HentaiOS-Devices-Sony/local_manifests
cd local_manifests
git checkout queenslave
cd ../..

# Sync
repo sync -f --force-sync --no-tags --no-clone-bundle -j$(nproc --all)
# Cherry-pick the upstream patches and delete some repos that would cause build conflict
. update.sh
```

#### Build

````bash
# Set up environment
$ . build/envsetup.sh


# Choose a target
$ lunch hentai_$device-userdebug

# Build the code
$ mka bacon -jX ## Replace X With the Number of Thread of Your Processor
````
