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

# Prepare Java Environment
sudo apt-get update
sudo apt-get install openjdk-8-jdk

# Install the Necessary Tools to Make an Android Build
sudo apt-get install bison g++-multilib git gperf libxml2-utils make zlib1g-dev zip liblz4-tool libncurses5 libssl-dev bc flex

# Download Repo Tool and Set PATH
mkdir ~/.bin
curl http://commondatastorage.googleapis.com/git-repo-downloads/repo > ~/.bin/repo

    ## For Chinese Users, You Can Use The Repo Tool Provide By Mokee
    curl https://raw.githubusercontent.com/MoKee/git-repo/stable/repo > ~/.bin/repo

chmod a+x ~/.bin/repo
sudo nano ~/.bashrc ## Open .bashrc with Nano
export PATH=~/bin:$PATH ## Add to the last line of .bashrc And save the file
source ~/.bashrc

````

#### Sync the codes

```bash

# Initialize Local Repository
repo init -u https://github.com/HentaiOS/platform_manifest -b queenslave
cd .repo
git clone https://github.com/HentaiOS-Devices-Sony/local_manifests
cd local_manifests
git checkout queenslave
cd ../..

# Solve Some confilct in the Xmls
<project path="vendor/qcom/opensource/dataservices" name="platform_vendor_qcom_opensource_dataservices" groups="vendor" remote="github/hentaiOS" /> ## Delete This Line in .repo/manifests/snippets/hentai.xml
<project path="vendor/qcom/opensource/interfaces" name="platform_vendor_qcom_opensource_interfaces" groups="vendor" remote="github/hentaiOS" /> ## Delete This Line in .repo/manifests/snippets/hentai.xml

# Sync
repo sync -f --force-sync --no-tags --no-clone-bundle -j$(nproc --all)

# Cherry-pick the Upstream Patches and Delete Some Repos That Would Cause Build Conflict
. update.sh

```

#### Build

````bash

# Setup Environment
. build/envsetup.sh


# Choose a Target
lunch hentai_$device-userdebug

# Build the Code
make -j&(nproc)

````

#### Flash the Images

````bash

# Flash the OEM Image
https://developer.sony.com/develop/open-devices/downloads/software-binaries ## Download the OEM Image for Your Device Here
unzip SW_binaries_for_Xperia_ANDROID_10.0.7.1_R1_<release version>_<platform>.zip ## Unzip the OEM Image
fastboot flash oem SW_binaries_for_Xperia_ANDROID_10.0.7.1_R1_<release version>_<platform>.img ## Flash the Image

#  Flash the Hentai OS
fastboot flash boot out/target/product/<device>/boot.img ## Flash Boot
fastboot flash vbmeta out/target/product/<device>/vbmeta.img ## Flash Vbmeta
fastboot flash dtbo out/target/product/<device>/dtbo.img ## Flash Dtbo
fastboot flash recovery out/target/product/<device>/recovery.img ## Flash Recovery
fastboot flash system out/target/product/<device>/system.img ## Flash System
fastboot flash vendor out/target/product/<device>/vendor.img ## Flash Vendor
fastboot flash userdata out/target/product/<device>/userdata.img ## Flash Userdata

````


Reference: <a href="https://developer.sony.com/develop/open-devices/guides/aosp-build-instructions/build-aosp-android-android-10-0-0">Sony Open Device Project Android 10 Build Guide</a>

