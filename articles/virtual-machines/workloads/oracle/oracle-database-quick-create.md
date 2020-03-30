---
title: Een Oracle-database maken in een Azure VM | Microsoft Documenten
description: Zorg dat een Oracle Database 12c-database snel in uw Azure-omgeving wordt uitgevoerd.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: 9f4b9d53aaa1cac17fbaae4b638e144654fad4e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535626"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Een Oracle-database maken in een Azure-vm

Met deze handleiding wordt met behulp van de Azure CLI een Azure-virtuele machine geïmplementeerd vanuit de [Oracle Marketplace-galerieafbeelding](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) om een Oracle 12c-database te maken. Zodra de server is geïmplementeerd, maakt u verbinding via SSH om de Oracle-database te configureren. 

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze Quickstart gebruikmaken van Azure CLI versie 2.0.4 of hoger. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. 

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Virtuele machine maken

Als u een virtuele machine (VM) wilt maken, gebruikt u de opdracht [AZ VM Create.](/cli/azure/vm) 

In het volgende voorbeeld wordt een virtuele machine met de naam `myVM` gemaakt. Het maakt ook SSH-toetsen, als ze nog niet bestaan op een standaardsleutellocatie. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

Nadat u de VM hebt gemaakt, geeft Azure CLI informatie weer die vergelijkbaar is met het volgende voorbeeld. Let op `publicIpAddress`de waarde voor . U gebruikt dit adres om toegang te krijgen tot de VM.

```output
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-the-vm"></a>Verbinding maken met de virtuele machine

Als u een SSH-sessie met de VM wilt maken, gebruikt u de volgende opdracht. Vervang het IP-adres door de `publicIpAddress` waarde voor uw virtuele machine.

```bash
ssh azureuser@<publicIpAddress>
```

## <a name="create-the-database"></a>De database maken

De Oracle-software is al geïnstalleerd op de Marketplace-afbeelding. Maak als volgt een voorbeelddatabase. 

1.  Schakel over *oracle* naar de oracle-superuser en initialiseer vervolgens de listener voor logboekregistratie:

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    ```

    De uitvoer lijkt op het volgende:

    ```output
    Copyright (c) 1991, 2014, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Log messages written to /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))

    Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Start Date                23-MAR-2017 15:32:08
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Log File         /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening Endpoints Summary...
    (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))
    The listener supports no services
    The command completed successfully
    ```

2.  Maak de database:

    ```bash
    dbca -silent \
           -createDatabase \
           -templateName General_Purpose.dbc \
           -gdbname cdb1 \
           -sid cdb1 \
           -responseFile NO_VALUE \
           -characterSet AL32UTF8 \
           -sysPassword OraPasswd1 \
           -systemPassword OraPasswd1 \
           -createAsContainerDatabase true \
           -numberOfPDBs 1 \
           -pdbName pdb1 \
           -pdbAdminPassword OraPasswd1 \
           -databaseType MULTIPURPOSE \
           -automaticMemoryManagement false \
           -storageType FS \
           -ignorePreReqs
    ```

    Het duurt een paar minuten om de database te maken.

3. Oracle-variabelen instellen

Voordat u verbinding maakt, moet u twee omgevingsvariabelen instellen: *ORACLE_HOME* en *ORACLE_SID*.

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
ORACLE_SID=cdb1; export ORACLE_SID
```

U ook ORACLE_HOME en ORACLE_SID variabelen toevoegen aan het .bashrc-bestand. Dit zou de omgevingsvariabelen opslaan voor toekomstige aanmeldingen. Bevestig dat `~/.bashrc` de volgende instructies aan het bestand zijn toegevoegd met behulp van de editor van uw keuze.

```bash
# Add ORACLE_HOME. 
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1 
# Add ORACLE_SID. 
export ORACLE_SID=cdb1 
```

## <a name="oracle-em-express-connectivity"></a>Oracle EM Express-connectiviteit

Voor een GUI-beheertool die u gebruiken om de database te verkennen, stelt u Oracle EM Express in. Als u verbinding wilt maken met Oracle EM Express, moet u eerst de poort in Oracle instellen. 

1. Maak verbinding met uw database met sqlplus:

    ```bash
    sqlplus / as sysdba
    ```

2. Eenmaal aangesloten, stelt u de poort 5502 in voor EM Express

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3. Open de container PDB1 als deze nog niet is geopend, maar controleer eerst de status:

    ```bash
    select con_id, name, open_mode from v$pdbs;
    ```

    De uitvoer lijkt op het volgende:

    ```output
      CON_ID NAME                           OPEN_MODE 
      ----------- ------------------------- ---------- 
      2           PDB$SEED                  READ ONLY 
      3           PDB1                      MOUNT
    ```

4. Als de `PDB1` OPEN_MODE voor niet LEZEN SCHRIJVEN is, voert u de volgende opdrachten uit om PDB1 te openen:

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

U moet `quit` typen om de sqlplus-sessie te beëindigen en te typen `exit` om uit te loggen van de oracle-gebruiker.

## <a name="automate-database-startup-and-shutdown"></a>Database opstarten en afsluiten automatiseren

De Oracle-database wordt standaard niet automatisch gestart wanneer u de virtuele machine opnieuw start. Als u de Oracle-database wilt instellen om automatisch te starten, meldt u zich eerst aan als root. Maak en werk vervolgens bepaalde systeembestanden samen.

1. Aanmelden als root

    ```bash
    sudo su -
    ```

2.  Bewerk het bestand `/etc/oratab` met behulp van `N` uw `Y`favoriete editor en wijzigt de standaardinstelling in:

    ```bash
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  Maak een `/etc/init.d/dbora` bestand met de naam en plak de volgende inhoud:

    ```bash
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle

    case "$1" in
    'start')
        # Start the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        # Remove "&" if you don't want startup as a background process.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
        touch /var/lock/subsys/dbora
        ;;

    'stop')
        # Stop the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
        rm -f /var/lock/subsys/dbora
        ;;
    esac
    ```

4.  Machtigingen voor bestanden met *chmod* als volgt wijzigen:

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  Maak als volgt symbolische koppelingen voor opstarten en afsluiten:

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  Als u uw wijzigingen wilt testen, start u de VM opnieuw:

    ```bash
    reboot
    ```

## <a name="open-ports-for-connectivity"></a>Poorten openen voor connectiviteit

De laatste taak is het configureren van een aantal externe eindpunten. Als u de Azure Network Security Group wilt instellen die de VM beschermt, sluit u eerst uw SSH-sessie af in de VM (moet u uit SSH zijn geschopt bij het opnieuw opstarten in vorige stap). 

1.  Als u het eindpunt wilt openen dat u op afstand gebruikt om toegang te krijgen tot de Oracle-database, maakt u als volgt een regel voor netwerkbeveiligingsgroep met [nsg-regel voor az-netwerk:](/cli/azure/network/nsg/rule) 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  Als u het eindpunt wilt openen dat u op afstand gebruikt om toegang te krijgen tot Oracle EM Express, maakt u als volgt een regel voor netwerkbeveiligingsgroep met [nsg-regel voor az-netwerk:](/cli/azure/network/nsg/rule)

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. Indien nodig, het verkrijgen van het openbare IP-adres van uw VM opnieuw met [az-netwerk public-ip show](/cli/azure/network/public-ip) als volgt:

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  Sluit EM Express aan vanuit uw browser. Controleer of uw browser compatibel is met EM Express (Flash-installatie is vereist): 

    ```https
    https://<VM ip address or hostname>:5502/em
    ```

U inloggen met het **SYS-account** en het **selectievakje sysdba** aanvinken. Gebruik het wachtwoord **OraPasswd1** dat u tijdens de installatie instelt. 

![Schermafbeelding van de aanmeldingspagina van Oracle OEM Express](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>Resources opschonen

Zodra u klaar bent met het verkennen van uw eerste Oracle-database op Azure en de VM niet meer nodig is, u de opdracht verwijderen van de [AZ-groep](/cli/azure/group) gebruiken om de brongroep, vm en alle gerelateerde bronnen te verwijderen.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over andere [Oracle-oplossingen op Azure](oracle-considerations.md). 

Probeer de [zelfstudie voor het installeren en configureren van Oracle Automated Storage Management.](configure-oracle-asm.md)
