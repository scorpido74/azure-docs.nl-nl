---
title: Een Oracle-data base maken in een Azure-VM | Microsoft Docs
description: Krijg snel een Oracle Database 12c-data base in uw Azure-omgeving.
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
ms.openlocfilehash: 6d43fa2621aa95bdcf18d5c033d1347e13dc3f67
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101475"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Een Oracle Database maken in een Azure VM

In deze hand leiding vindt u informatie over het gebruik van de Azure CLI om een virtuele Azure-machine te implementeren vanuit de [Galerie met Oracle Marketplace-afbeeldingen](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) om een Oracle 12c-data base te maken. Zodra de server is geïmplementeerd, kunt u verbinding maken via SSH om de Oracle-Data Base te configureren. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze snelstartgids de versie Azure CLI 2.0.4 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. 

In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
## <a name="create-virtual-machine"></a>Virtuele machine maken

Als u een virtuele machine (VM) wilt maken, gebruikt u de opdracht [AZ VM Create](/cli/azure/vm) . 

In het volgende voorbeeld wordt een virtuele machine met de naam `myVM` gemaakt. Ook worden er SSH-sleutels gemaakt, als deze nog niet bestaan op een standaard sleutel locatie. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

Nadat u de virtuele machine hebt gemaakt, toont Azure CLI informatie die lijkt op het volgende voor beeld. Noteer de waarde voor `publicIpAddress`. U gebruikt dit adres voor toegang tot de virtuele machine.

```azurecli
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

Gebruik de volgende opdracht om een SSH-sessie met de virtuele machine te maken. Vervang het IP-adres door `publicIpAddress` de waarde voor uw VM.

```bash 
ssh azureuser@<publicIpAddress>
```

## <a name="create-the-database"></a>De data base maken

De Oracle-software is al geïnstalleerd op de Marketplace-installatie kopie. Maak als volgt een voorbeeld database. 

1.  Schakel over naar de *Oracle* -super gebruiker en Initialiseer vervolgens de listener voor logboek registratie:

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    ```

    De uitvoer lijkt op het volgende:

    ```bash
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

2.  Maak de Data Base:

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

    Het duurt enkele minuten om de data base te maken.

3. Oracle-variabelen instellen

Voordat u verbinding maakt, moet u twee omgevings variabelen instellen: *ORACLE_HOME* en *ORACLE_SID*.

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
ORACLE_SID=cdb1; export ORACLE_SID
```
U kunt ook ORACLE_HOME-en ORACLE_SID-variabelen toevoegen aan het. bashrc-bestand. Hierdoor worden de omgevings variabelen opgeslagen voor toekomstige aanmeldingen. Bevestig dat u de volgende instructies aan het bestand `~/.bashrc` hebt toegevoegd met de editor van uw keuze.

```bash
# Add ORACLE_HOME. 
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1 
# Add ORACLE_SID. 
export ORACLE_SID=cdb1 
```

## <a name="oracle-em-express-connectivity"></a>Oracle EM Express-connectiviteit

Voor een GUI-beheer programma dat u kunt gebruiken om de data base te verkennen, stelt u Oracle EM Express in. Als u verbinding wilt maken met Oracle EM Express, moet u eerst de poort instellen in Oracle. 

1. Verbinding maken met uw data base met behulp van sqlplus:

    ```bash
    sqlplus / as sysdba
    ```

2. Nadat de verbinding is gemaakt, stelt u de poort 5502 voor EM Express in

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3. Open de container PDB1 als deze nog niet is geopend, maar eerst de status controleren:

    ```bash
    select con_id, name, open_mode from v$pdbs;
    ```

    De uitvoer lijkt op het volgende:

    ```bash
      CON_ID NAME                           OPEN_MODE 
      ----------- ------------------------- ---------- 
      2           PDB$SEED                  READ ONLY 
      3           PDB1                      MOUNT
    ```

4. Als de OPEN_MODE voor `PDB1` niet lezen schrijven is, voert u de volgende opdrachten uit om PDB1 te openen:

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

U moet typen `quit` om de sqlplus-sessie te beëindigen en `exit` te typen om de Oracle-gebruiker af te melden.

## <a name="automate-database-startup-and-shutdown"></a>Data bases automatisch opstarten en afsluiten

De Oracle-Data Base wordt standaard niet automatisch gestart wanneer u de virtuele machine opnieuw opstart. Als u de Oracle-Data Base wilt instellen om automatisch te starten, meldt u zich eerst aan als basis. Maak en werk vervolgens enkele systeem bestanden bij.

1. Aanmelden als basis
    ```bash
    sudo su -
    ```

2.  Bewerk het bestand `/etc/oratab` met uw favoriete editor en wijzig de standaard instelling `N` in `Y`:

    ```bash
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  Maak een bestand met `/etc/init.d/dbora` de naam en plak de volgende inhoud:

    ```
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

4.  Wijzig machtigingen voor bestanden met *chmod* als volgt:

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

6.  Als u uw wijzigingen wilt testen, start u de VM opnieuw op:

    ```bash
    reboot
    ```

## <a name="open-ports-for-connectivity"></a>Poorten openen voor connectiviteit

De laatste taak is het configureren van sommige externe eind punten. Als u de Azure-netwerk beveiligings groep wilt instellen die de virtuele machine beveiligt, moet u eerst uw SSH-sessie in de VM afsluiten (na het opnieuw opstarten in de vorige stap). 

1.  Als u het eind punt dat u gebruikt voor toegang tot de Oracle-Data Base op afstand wilt openen, maakt u een regel voor een netwerk beveiligings groep met [AZ Network NSG regel Create](/cli/azure/network/nsg/rule) als volgt: 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  Als u het eind punt wilt openen dat u gebruikt voor toegang tot Oracle EM Express op afstand, maakt u een regel voor een netwerk beveiligings groep met [AZ Network NSG regel Create](/cli/azure/network/nsg/rule) :

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. Als dat nodig is, kunt u het open bare IP-adres van uw VM opnieuw verkrijgen met [AZ Network public-ip show](/cli/azure/network/public-ip) als volgt:

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  Verbinding maken met EM Express vanuit uw browser. Zorg ervoor dat uw browser compatibel is met EM Express (Flash installeren is vereist): 

    ```
    https://<VM ip address or hostname>:5502/em
    ```

U kunt u aanmelden met het **sys** -account en het selectie vakje **als sysdba** inschakelen. Gebruik de wachtwoord **OraPasswd1** die u tijdens de installatie hebt ingesteld. 

![Scherm afbeelding van de Oracle OEM Express-aanmeldings pagina](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het verkennen van uw eerste Oracle-data base in Azure en de virtuele machine niet meer nodig is, kunt u de opdracht [AZ Group delete](/cli/azure/group) gebruiken om de resource groep, de virtuele machine en alle gerelateerde resources te verwijderen.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over andere [Oracle-oplossingen in azure](oracle-considerations.md). 

Probeer de zelf studie [Oracle Automated Storage Management installeren en configureren](configure-oracle-asm.md) .
