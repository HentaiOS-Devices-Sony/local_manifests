# Hentai OS for Sony Xperia devices

### This is for Building Hentai OS for the Following Sony Xperia Devices

```bash

Xperia Touch
Xperia X
Xperia X Compact
Xperia X Performance
Xperia XZ
Xperia XZs
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
### Before You Begin

#### Unlock the Bootloder

unlock your device bootloader at <a href="https://developer.sony.com/develop/open-devices/get-started/unlock-bootloader/#unlock-code">Unlock Bootloader</a> following the  <a href="https://developer.sony.com/develop/open-devices/get-started/unlock-bootloader/how-to-unlock-bootloader/">How to Unlock Bootloader</a> guide

#### System requirement

Ubuntu 18.04 LTS Recommanded

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

# Creat the Folder for All the Source Code

mkdir ~/hentai
cd ~/hentai

# Initialize Local Repository

repo init -u https://github.com/HentaiOS/platform_manifest -b queenslave
cd .repo
git clone https://github.com/HentaiOS-Devices-Sony/local_manifests
cd local_manifests

git checkout queenslave

## If Building for Xperia X / X Compact / X performance / XZ / XZs / Touch
## Use
git checkout queenslave_4.9
## Instead

cd ../..

# Solve Some confilct in the Xmls
## Delete These Two Lines in .repo/manifests/snippets/hentai.xml

<project path="vendor/qcom/opensource/dataservices" name="platform_vendor_qcom_opensource_dataservices" groups="vendor" remote="github/hentaiOS" /> 
<project path="vendor/qcom/opensource/interfaces" name="platform_vendor_qcom_opensource_interfaces" groups="vendor" remote="github/hentaiOS" /> 

# Sync

repo sync -f --force-sync --no-tags --no-clone-bundle -j$(nproc --all)

# Cherry-pick the Upstream Patches and Delete Some Repos That Would Cause Build Conflict

. update.sh

# Extra Step if Building for Xperia X / X Compact / X performance / XZ / XZs / Touch

. q_4.9_repo_update.sh

# Add flex and bison to Allow List to Make the Kernel Buildable

nano build/soong/ui/build/paths/config.go ## Open with nano

## Add these two lines to the End of Allow List
## The Allow List Starts at Line 76, Ends at Line 112
    
    "flex":     Allowed,    ## Add this to line 113
    "bison":    Allowed,    ## Add this to line 114
    
## Save and Close build/soong/ui/build/paths/config.go
    
```

#### Build

````bash

# Setup Environment

. build/envsetup.sh


# Choose a Target

lunch hentai_$device-userdebug

# Build the images

make -j&(nproc)

# Build flashable zip

make otapackage -j&(nproc)

````

### Flash 

#### Flash the OEM Image with Fastboot

````bash

https://developer.sony.com/develop/open-devices/downloads/software-binaries                    ## Download the OEM Image for Your Device Here
unzip SW_binaries_for_Xperia_ANDROID_10.0.7.1_R1_<release version>_<platform>.zip              ## Unzip the OEM Image
fastboot flash oem SW_binaries_for_Xperia_ANDROID_10.0.7.1_R1_<release version>_<platform>.img ## Flash the Image

## This Step is Essential

````

### Flash Hentai OS

#### Flash Hentai OS with Fastboot
````bash

fastboot flash boot out/target/product/<device>/boot.img         ## Flash Boot
fastboot flash vbmeta out/target/product/<device>/vbmeta.img     ## Flash Vbmeta
fastboot flash dtbo out/target/product/<device>/dtbo.img         ## Flash Dtbo
fastboot flash recovery out/target/product/<device>/recovery.img ## Flash Recovery
fastboot flash system out/target/product/<device>/system.img     ## Flash System
fastboot flash vendor out/target/product/<device>/vendor.img     ## Flash Vendor
fastboot flash userdata out/target/product/<device>/userdata.img ## Flash Userdata

## There is No Need to Flash any Gapps Package, Gapps Already Included

````

#### Flash Hentai OS with Flashable Zip using Recovery

````bash

# Flash the Zip With TWRP or Any Recovey

fastboot flash recovery <the-name-of-the-recovery-image>.img ## Flash Recovery to the Device; Then Boot to the Recovery by Pressing Both the Power and Volume Down, When Feel a Viberate, Release Power and Continue to Press Volume Down
fastboot boot <the-name-of-the-recovery-image>.img ## Directly Boot to Recovery Without the Need to Flash Anything

## Then Flash the Zip in the Recovery

## There is No Need to Flash any Gapps Package, Gapps Already Included

````

### References

<a href="https://developer.sony.com/develop/open-devices/guides/aosp-build-instructions/build-aosp-android-android-10-0-0">Sony Open Device Project Android 10 Build Guide</a>

<a href="https://sx.ix5.org/info/building-android/">Sony Xperia Corner - Building Android</a>
