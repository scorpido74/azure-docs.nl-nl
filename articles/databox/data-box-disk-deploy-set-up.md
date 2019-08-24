---
title: Zelf studie voor het uitpakken, verbinding maken met en ontgrendelen Azure Data Box Disk | Microsoft Docs
description: In deze zelfstudie leest u hoe u Azure Data Box Disk instelt
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.localizationpriority: high
ms.date: 07/23/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 483288869e0eda20010108b8293c5964ff9571c2
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012909"
---
::: zone target="docs"

# <a name="tutorial-unpack-connect-and-unlock-azure-data-box-disk"></a>Zelfstudie: Azure Data Box Disk uitpakken, verbinding maken en ontgrendelen

In deze zelfstudie wordt beschreven hoe u de Azure Data Box-schijf uitpakt, aansluit en ontgrendelt.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Uw Data Box-schijf uitpakken
> * Schijven aansluiten en de wachtwoordcode opzoeken
> * Schijven ontgrendelen op Windows-client
> * Schijven ontgrendelen op Linux-client

## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

1. U hebt de zelfstudie [ Azure Data Box Disk bestellen](data-box-disk-deploy-ordered.md).
2. U hebt uw schijven ontvangen en de taakstatus in de portal is bijgewerkt naar **Geleverd**.
3. U beschikt over een clientcomputer waarop u het ontgrendelingsprogramma voor Data Box-schijven kunt installeren. De clientcomputer moet voldoen aan deze vereisten:
    - Een [ondersteund besturingssysteem](data-box-disk-system-requirements.md#supported-operating-systems-for-clients) worden uitgevoerd.
    - Andere [vereiste software](data-box-disk-system-requirements.md#other-required-software-for-windows-clients) is geïnstalleerd in het geval van een Windows-client.  

## <a name="unpack-your-disks"></a>Uw schijven uitpakken

 Voer de volgende stappen uit om uw schijven uit te pakken.

1. De Data Box-schijven worden verzonden in een kleine verzenddoos. Open de doos en haal de inhoud eruit. Controleer of de doos één tot vijf SSD-schijven (Solid-State Disks) en een USB-kabel per schijf bevat. Bekijk of er aan de doos is gerommeld en of deze is beschadigd. 

    ![Verzenddoos voor de Data Box-schijf](media/data-box-disk-deploy-set-up/data-box-disk-ship-package1.png)

2. Als er aan de verzenddoos is gerommeld of als deze ernstig is beschadigd, moet u de doos niet openen. Neem in dat geval contact op met Microsoft Ondersteuning om te bepalen of de schijven in een goede staat zijn of dat er vervangende schijven moeten worden verzonden.
3. Controleer of er op de doos een doorzichtige plastic hoes zit met daarin een verzendlabel (onder het huidige label) dat voor een retourzending kan worden gebruikt. Als dit label is zoekgeraakt of is beschadigd, kunt u in Azure Portal altijd een nieuw label downloaden en afdrukken. 

    ![Verzendlabel voor de Data Box-schijf](media/data-box-disk-deploy-set-up/data-box-disk-package-ship-label.png)

4. Bewaar de doos en het verpakkingsschuim voor de retourzending van de schijven.

## <a name="connect-to-disks-and-get-the-passkey"></a>Schijven aansluiten en de wachtwoordcode opzoeken 

1. Gebruik de meegeleverde kabel om de schijf aan te sluiten op de clientcomputer met een ondersteund besturingssysteem, zoals wordt aangegeven in de vereisten. 

    ![De Data Box-schijf aansluiten](media/data-box-disk-deploy-set-up/data-box-disk-connect-unlock.png)    
    
2. Ga in Azure Portal naar **Algemeen > Apparaatdetails**. Gebruik het kopieerpictogram om de wachtwoordcode te kopiëren. Deze code wordt gebruikt voor het ontgrendelen van de schijven.

    ![Wachtwoordcode voor ontgrendelen van Data Box-schijf](media/data-box-disk-deploy-set-up/data-box-disk-get-passkey.png) 

De stappen voor het ontgrendelen van de schijven verschillen voor clients met Windows of met Linux.

## <a name="unlock-disks-on-windows-client"></a>Schijven ontgrendelen op Windows-client

Voer de volgende stappen uit om uw schijven aan te sluiten en te ontgrendelen.
     
1. Ga in Azure Portal naar **Algemeen > Apparaatdetails**. 
2. Download de toolset van Data Box Data Box Disk voor Windows-clients. Deze toolset bevat drie hulpprogram ma's: Data Box Disk hulp programma voor ontgrendelen, Data Box Disk validatie hulpprogramma en Data Box Disk Splits kopiëren. 

    In deze procedure gebruikt u alleen het hulpprogramma Data Box Disk Unlock. De andere twee hulpprogramma's worden later gebruikt.

    > [!div class="nextstepaction"]
    > [Data Box Disk-toolset voor Windows downloaden](https://aka.ms/databoxdisktoolswin)         

3. Pak de toolset uit op de computer die u gaat gebruiken om de gegevens te kopiëren. 
4. Open op dezelfde computer een opdrachtpromptvenster of voer hierop Windows PowerShell uit als beheerder.
5. (Optioneel) Voer de opdracht voor systeemcontrole uit om na te gaan of de computer die u gebruikt voor het ontgrendelen van de schijf voldoet aan de besturingssysteemvereisten. Hieronder ziet u een voorbeeld van de uitvoer. 

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /SystemCheck
    Successfully verified that the system can run the tool.
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ``` 

6. Voer `DataBoxDiskUnlock.exe` uit en geef de wachtwoordcode op die u hebt vastgesteld in [Schijven aansluiten en de wachtwoordcode opzoeken](#connect-to-disks-and-get-the-passkey). De stationsletter die is toegewezen aan de schijf wordt weergegeven. Hieronder ziet u een voorbeeld van de uitvoer.

    ```powershell
    PS C:\WINDOWS\system32> cd C:\DataBoxDiskUnlockTool\DiskUnlock
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe
    Enter the passkey :
    testpasskey1
    
    Following volumes are unlocked and verified.
    Volume drive letters: D:
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```

7. Herhaal de ontgrendelingsstappen voor alle schijven die in de toekomst worden geplaatst. Gebruik de opdracht `help` als u hulp nodig hebt met het ontgrendelingsprogramma van Data Box Disk.   

    ```powershell
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /help
    USAGE:
    DataBoxUnlock /PassKey:<passkey_from_Azure_portal>
    
    Example: DataBoxUnlock /PassKey:<your passkey>
    Example: DataBoxUnlock /SystemCheck
    Example: DataBoxUnlock /Help
    
    /PassKey:        Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.
    /SystemCheck:    This option checks if your system meets the requirements to run the tool.
    /Help:           This option provides help on cmdlet usage and examples.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```  
8. Wanneer de schijf is ontgrendeld, kunt u de inhoud van de schijf bekijken.    

    ![Inhoud van de Data Box-schijf](media/data-box-disk-deploy-set-up/data-box-disk-content.png)

Zie problemen [met ontgrendelen oplossen](data-box-disk-troubleshoot-unlock.md)als u problemen ondervindt tijdens het ontgrendelen van de schijven. 

## <a name="unlock-disks-on-linux-client"></a>Schijven ontgrendelen op Linux-client

1. Ga in Azure Portal naar **Algemeen > Apparaatdetails**. 
2. Download de toolset van Data Box Data Box Disk voor Linux-clients.  

    > [!div class="nextstepaction"]
    > [Data Box Disk-toolset voor Linux downloaden](https://aka.ms/databoxdisktoolslinux) 

3. Open een terminal op uw Linux-client. Ga naar de map waar u de software hebt gedownload. Wijzig de bestandsmachtigingen zodat u deze bestanden kunt uitvoeren. Typ de volgende opdracht: 

    `chmod +x DataBoxDiskUnlock_x86_64` 
    
    `chmod +x DataBoxDiskUnlock_Prep.sh` 
 
    Hieronder ziet u een voorbeeld van de uitvoer. Zodra de opdracht chmod is voltooid, kunt u controleren of de bestandsmachtigingen zijn gewijzigd door de opdracht `ls` uit te voeren. 
 
    ```
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_x86_64  
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_Prep.sh   
        [user@localhost Downloads]$ ls -l  
        -rwxrwxr-x. 1 user user 1152664 Aug 10 17:26 DataBoxDiskUnlock_x86_64  
        -rwxrwxr-x. 1 user user 795 Aug 5 23:26 DataBoxDiskUnlock_Prep.sh
    ```
4. Voer het script uit om alle binaire bestanden te installeren die nodig zijn voor de ontgrendelingssoftware van Data Box Disk. Gebruik `sudo` om de opdracht als root uit te voeren. Als de binaire bestanden zijn geïnstalleerd, wordt dit gemeld in de terminal.

    `sudo ./DataBoxDiskUnlock_Prep.sh`

    Het script controleert eerst of er een ondersteund besturingssysteem wordt uitgevoerd op de clientcomputer. Hieronder ziet u een voorbeeld van de uitvoer. 
 
    ```
    [user@localhost Documents]$ sudo ./DataBoxDiskUnlock_Prep.sh 
        OS = CentOS Version = 6.9 
        Release = CentOS release 6.9 (Final) 
        Architecture = x64 
    
        The script will install the following packages and dependencies. 
        epel-release 
        dislocker 
        ntfs-3g 
        fuse-dislocker 
        Do you wish to continue? y|n :|
    ```
    
 
5. Typ `y` om verder te gaan met de installatie. Het script installeert de volgende pakketten: 
   - **epel-release**: opslagplaats met de volgende drie pakketten. 
   - **dislocker en zekering-** dislocker: deze hulpprogram Ma's helpen BitLocker-versleutelde schijven te ontsleutelen. 
   - **ntfs-3g**: pakket voor het koppelen van NTFS-volumes. 
 
     Als de pakketten zijn geïnstalleerd, ziet u hierover een melding in de terminal.     
     ```
     Dependency Installed: compat-readline5.x86 64 0:5.2-17.I.el6 dislocker-libs.x86 64 0:0.7.1-8.el6 mbedtls.x86 64 0:2.7.4-l.el6        ruby.x86 64 0:1.8.7.374-5.el6 
     ruby-libs.x86 64 0:1.8.7.374-5.el6 
     Complete! 
     Loaded plugins: fastestmirror, refresh-packagekit, security 
     Setting up Remove Process 
     Resolving Dependencies 
     --> Running transaction check 
     ---> Package epel-release.noarch 0:6-8 will be erased --> Finished Dependency Resolution 
     Dependencies Resolved 
     Package        Architecture        Version        Repository        Size 
     Removing:  epel-release        noarch         6-8        @extras        22 k 
     Transaction Summary                                 
     Remove        1 Package(s) 
     Installed size: 22 k 
     Downloading Packages: 
     Running rpmcheckdebug 
     Running Transaction Test 
     Transaction Test Succeeded 
     Running Transaction 
     Erasing : epel-release-6-8.noarch 
     Verifying : epel-release-6-8.noarch 
     Removed: 
     epel-release.noarch 0:6-8 
     Complete! 
     Dislocker is installed by the script. 
     OpenSSL is already installed.
     ```

6. Voer het ontgrendelingsprogramma van Data Box Disk uit. Geef de wachtwoordcode op die u hebt vastgesteld in [Schijven aansluiten en de wachtwoordcode opzoeken](#connect-to-disks-and-get-the-passkey). Geef eventueel een lijst van met BitLocker versleutelde volumes op die u wilt ontgrendelen. De wachtwoordcode en de lijst moeten tussen enkele aanhalingstekens staan. 

    Typ de volgende opdracht.
 
    `sudo ./DataBoxDiskUnlock_x86_64 /PassKey:’<Your passkey from Azure portal>’          

    Hieronder ziet u een voorbeeld van de uitvoer. 
 
    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock_x86_64 /Passkey:’qwerqwerqwer’  
    
    START: Mon Aug 13 14:25:49 2018 
    Volumes: /dev/sdbl 
    Passkey: qwerqwerqwer 
    
    Volumes for data copy : 
    /dev/sdbl: /mnt/DataBoxDisk/mountVoll/ 
    END: Mon Aug 13 14:26:02 2018
    ```
    U ziet het koppelpunt voor het volume waarnaar u uw gegevens kunt kopiëren.

7. Herhaal de ontgrendelingsstappen voor alle schijven die in de toekomst worden geplaatst. Gebruik de opdracht `help` als u hulp nodig hebt met het ontgrendelingsprogramma van Data Box Disk. 
    
    `sudo ./DataBoxDiskUnlock_x86_64 /Help` 

    Hieronder ziet u een voorbeeld van de uitvoer. 
 
    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock_x86_64 /Help  
    START: Mon Aug 13 14:29:20 2018 
    USAGE: 
    sudo DataBoxDiskUnlock /PassKey:’<passkey from Azure_portal>’ 
    
    Example: sudo DataBoxDiskUnlock /PassKey:’passkey’ 
    Example: sudo DataBoxDiskUnlock /PassKey:’passkey’ /Volumes:’/dev/sdbl’ 
    Example: sudo DataBoxDiskUnlock /Help Example: sudo DataBoxDiskUnlock /Clean 
    
    /PassKey: This option takes a passkey as input and unlocks all of your disks. 
    Get the passkey from your Data Box Disk order in Azure portal. 
    /Volumes: This option is used to input a list of BitLocker encrypted volumes. 
    /Help: This option provides help on the tool usage and examples. 
    /Unmount: This option unmounts all the volumes mounted by this tool. 
   
    END: Mon Aug 13 14:29:20 2018 [user@localhost Downloads]$
    ```
    
8. Als de schijf is ontgrendeld, kunt u naar het koppelpunt gaan en de inhoud van de schijf bekijken. U kunt de gegevens nu kopiëren naar de map *BlockBlob* of *PageBlob*. 

    ![Inhoud van de Data Box-schijf](media/data-box-disk-deploy-set-up/data-box-disk-content-linux.png)


Zie problemen [met ontgrendelen oplossen](data-box-disk-troubleshoot-unlock.md)als u problemen ondervindt tijdens het ontgrendelen van de schijven. 

::: zone-end

::: zone target="chromeless"

1. Schijven uitpakken en de inbegrepen kabel gebruiken om de schijf te verbinden met de client computer.
2. Down load en pak de Data Box Disk-toolset op dezelfde computer die u gaat gebruiken om de gegevens te kopiëren.

    > [!div class="nextstepaction"]
    > [Data Box Disk-toolset voor Windows downloaden](https://aka.ms/databoxdisktoolswin)

    of
    > [!div class="nextstepaction"]
    > [Data Box Disk-toolset voor Linux downloaden](https://aka.ms/databoxdisktoolslinux) 

3. Als u de schijven op een Windows-client wilt ontgrendelen, opent u een opdracht prompt venster of voert u Windows Power shell uit als Administrator op dezelfde computer:

    - Typ de volgende opdracht in dezelfde map waarin Data Box Disk hulp programma voor ontgrendelen is geïnstalleerd.

        ``` 
        .\DataBoxDiskUnlock.exe
        ```
    -  Geef de sleutel op die u hebt verkregen van **algemene > apparaatgegevens** in de Azure Portal. De stationsletter die is toegewezen aan de schijf wordt weergegeven. 
4. Als u de schijven op een Linux-client wilt ontgrendelen, opent u een Terminal. Ga naar de map waar u de software hebt gedownload. Typ de volgende opdrachten om de bestands machtigingen te wijzigen, zodat u deze bestanden kunt uitvoeren: 

    ```
    chmod +x DataBoxDiskUnlock_x86_64
    chmod +x DataBoxDiskUnlock_Prep.sh
    ``` 
    Voer het script uit om alle vereiste binaire bestanden te installeren.

    ```
    sudo ./DataBoxDiskUnlock_Prep.sh
    ```
    Voer het ontgrendelingsprogramma van Data Box Disk uit. Geef de sleutel van de Azure Portal op door naar **algemene > apparaatgegevens**te gaan. Geef eventueel een lijst met BitLocker-versleutelde volumes binnen enkele aanhalings tekens op om te ontgrendelen.

    ```
    sudo ./DataBoxDiskUnlock_x86_64 /PassKey:’<Your passkey from Azure portal>’
    ```      
5. Herhaal de ontgrendelingsstappen voor alle schijven die in de toekomst worden geplaatst. Gebruik de Help-opdracht als u hulp nodig hebt met het ontgrendelingsprogramma voor Data Box-schijven.

Nadat de schijf is ontgrendeld, kunt u de inhoud van de schijf weer geven.

Ga naar de [zelf studie voor meer informatie over het instellen en ontgrendelen van de schijven: Azure Data Box Disk](data-box-disk-deploy-set-up.md)uitpakken, verbinding maken en ontgrendelen.

::: zone-end

::: zone target="docs"

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie zijn verschillende onderwerpen besproken over de Azure Data Box-schijf, zoals:

> [!div class="checklist"]
> * Uw Data Box-schijf uitpakken
> * Schijven aansluiten en de wachtwoordcode opzoeken
> * Schijven ontgrendelen op Windows-client
> * Schijven ontgrendelen op Linux-client


Ga naar de volgende zelfstudie om te lezen hoe u gegevens kopieert naar uw Data Box-schijf.

> [!div class="nextstepaction"]
> [Gegevens kopiëren naar uw Data Box-schijf](./data-box-disk-deploy-copy-data.md)

::: zone-end

