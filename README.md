# Palworld Linux Server Setup Guide

**Overview**

This is a step-by-step guide on how to set up and run a Ubuntu Palworld server.

**Prerequisites**

- Ubuntu server (20.04 or higher recommended)
- Basic knowledge of terminal commands
- A user with sudo privileges

**Disclaimer**

Directory structures may differ based on your specific setup.

# Step 1: Update and Upgrade Your System

    sudo apt update && sudo apt full-upgrade -y && sudo apt autoremove -y

# Step 2: Install Required Dependencies

    sudo add-apt-repository multiverse
    sudo dpkg --add-architecture i386; sudo apt update

**Install screen (Session Manager)**

    sudo apt install screen -y

**Update**

    sudo apt update

**Install Steamcmd**

    sudo apt install steamcmd -y

**Install UFW (Firewall)**

    sudo apt install ufw -y

# Step 3: Configure UFW (Uncomplicated Firewall)

    sudo ufw allow from any proto udp to any port 8211 comment "Palworld Server Port"

**Note:** To make this more secure you can change the "any" in "from any" to an IP address or to a range of address.

    sudo ufw allow from any proto udp to any port 27015 comment "Palworld Query Port"

**Note:** To make this more secure you can change the "any" in "from any" to an IP address or to a range of address.

**Allow SSH connections through the UFW** (Optional)

    sudo ufw allow from any to any port 22 comment "SSH"

**Note:** To make this more secure you can change the "any" in "from any" to an IP address to a range of address.

**Enable UFW** (UFW will enable on reboot)

    sudo ufw enable
    
--------------------------------------------------------------------------------
# Step 4: Create a Steam User

    sudo adduser steam 

**Note:** This will prompt you through the setup

-------------------------------------------------------------------------------
# Step 5: Install Palworld Server

**Switch to the steam user**

    su steam

**Move to the steam home dir**

    cd /home/steam

**Install Palworld Server Files**

    steamcmd +login anonymous +app_update 2394010 validate +quit

**Navigate to the Server Directory**

    cd .steam/steam/steamapps/common/PalServer

**Start the server**

    ./PalServer.sh -useperfthreads -NoAsyncLoadingThread -UseMultithreadForDS

Stop the server with Ctrl + C.

# Step 6: Configure the Server

**Edit PalWorldSettings.ini**

    nano Pal/Saved/Config/LinuxServer/PalWorldSettings.ini

**Add the following to PalworldSettings.ini**

    [/Script/Pal.PalGameWorldSettings]
    OptionSettings=(Difficulty=None,DayTimeSpeedRate=1.000000,NightTimeSpeedRate=1.000000,ExpRate=1.000000,PalCaptureRate=1.000000,PalSpawnNumRate=1.000000,PalDamageRateAttack=1.000000,PalDamageRateDefense=1.000000,PlayerDamageRateAttack=1.000000,PlayerDamageRateDefense=1.000000,PlayerStomachDecreaceRate=1.000000,PlayerStaminaDecreaceRate=1.000000,PlayerAutoHPRegeneRate=1.000000,PlayerAutoHpRegeneRateInSleep=1.000000,PalStomachDecreaceRate=1.000000,PalStaminaDecreaceRate=1.000000,PalAutoHPRegeneRate=1.000000,PalAutoHpRegeneRateInSleep=1.000000,BuildObjectDamageRate=1.000000,BuildObjectDeteriorationDamageRate=1.000000,CollectionDropRate=1.000000,CollectionObjectHpRate=1.000000,CollectionObjectRespawnSpeedRate=1.000000,EnemyDropItemRate=1.000000,DeathPenalty=All,bEnablePlayerToPlayerDamage=False,bEnableFriendlyFire=False,bEnableInvaderEnemy=True,bActiveUNKO=False,bEnableAimAssistPad=True,bEnableAimAssistKeyboard=False,DropItemMaxNum=3000,DropItemMaxNum_UNKO=100,BaseCampMaxNum=128,BaseCampWorkerMaxNum=15,DropItemAliveMaxHours=1.000000,bAutoResetGuildNoOnlinePlayers=False,AutoResetGuildTimeNoOnlinePlayers=72.000000,GuildPlayerMaxNum=20,BaseCampMaxNumInGuild=4,PalEggDefaultHatchingTime=72.000000,WorkSpeedRate=1.000000,AutoSaveSpan=30.000000,bIsMultiplay=False,bIsPvP=False,bCanPickupOtherGuildDeathPenaltyDrop=False,bEnableNonLoginPenalty=True,bEnableFastTravel=True,bIsStartLocationSelectByMap=True,bExistPlayerAfterLogout=False,bEnableDefenseOtherGuildPlayer=False,bInvisibleOtherGuildBaseCampAreaFX=False,CoopPlayerMaxNum=4,ServerPlayerMaxNum=32,ServerName="Default Palworld Server",ServerDescription="",AdminPassword="",ServerPassword="",PublicPort=8211,PublicIP="",RCONEnabled=False,RCONPort=25575,Region="",bUseAuth=True,BanListURL="https://api.palworldgame.com/api/banlist.txt",RESTAPIEnabled=False,RESTAPIPort=8212,bShowPlayerList=False,AllowConnectPlatform=Steam,bIsUseBackupSaveData=True,LogFormatType=Text,SupplyDropSpan=180)

**Notice:** Edit the following settings as needed:

ServerName=""

ServerDescription="" (optional)

AdminPassword="" (optional)

ServerPassword="" (optional)

PublicIP=""

**Notice:** The file should have two lines and if you use nano it should look something like this.

![image](https://github.com/user-attachments/assets/3efb9777-25d3-49ec-8846-e56372c564f0)

**Note:** You can also find the PalWorldSettings.ini settings at the following location.

    nano /home/steam/.steam/steamapps/common/PalServer/DefaultPalWorldSettings.ini


# Step 7: Create a Systemd Service (Optional)

**Create the service file**

    sudo nano /etc/systemd/system/PalWorld.service

Add the following configuration

    [Unit]
    Description=PalWorld Server
    After=network.target

    [Service]
    Type=forking
    WorkingDirectory=/home/steam/.steam/steamapps/common/PalServer
    ExecStart=/usr/bin/screen -dmS PalWord /home/steam/.steam/steamapps/common/PalServer/./PalServer.sh -useperfthreads -NoAsyncLoadingThread -UseMultithreadForDS -PublicLobby
    RemainAfterExit=yes
    Restart=on-failure
    RestartSec=5
    User=steam
    StandardOutput=journal+console
    StandardError=journal+console

    [Install]
    WantedBy=multi-user.target

**Enable and Start the Service**

    sudo systemctl daemon-reload
    sudo systemctl enable PalWorld.service
    sudo systemctl start PalWorld.service

**Conclusion**

You have successfully set up your Palworld server! For further customization, refer to the game’s official documentation.


**References**
- https://developer.valvesoftware.com/wiki/SteamCMD#Linux
- https://tech.palworldgame.com/getting-started/deploy-dedicated-server/
- https://www.digitalocean.com/community/tutorials/ufw-essentials-common-firewall-rules-and-commands
- https://tech.palworldgame.com/settings-and-operation/configuration/
