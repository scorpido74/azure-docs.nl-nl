---
title: Azure VMware-oplossing - migratie met Azure-gegevensvak
description: Azure Data Box gebruiken om gegevens in bulk te migreren naar Azure VMware Solution.
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65167169248d83ebfec2c49c308673ec9315934e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019754"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>Gegevens migreren naar Azure VMware-oplossing met Azure Data Box

Met de Microsoft Azure Data Box-cloudoplossing u terabytes (TBS's) aan gegevens op een snelle, goedkope en betrouwbare manier naar Azure verzenden. De veilige gegevensoverdracht wordt versneld door u een systeemeigen Data Box-opslagapparaat toe te sturen. Elk opslagapparaat heeft een maximale bruikbare opslagcapaciteit van 80 TB en wordt door een regionale luchtvaartmaatschappij naar uw datacenter getransporteerd. Het apparaat heeft een robuuste behuizing om uw gegevens tijdens het transport te beschermen en te beveiligen.

Door Data Box te gebruiken, u uw VMware-gegevens bulkmigreren naar uw private cloud. Gegevens uit uw on-premises VMware vSphere-omgeving worden via het NFS-protocol (Network File System) gekopieerd naar Data Box. Bulkgegevensmigratie omvat het opslaan van een point-in-time-kopie van virtuele machines, configuratie en gekoppelde gegevens naar Gegevensvak en vervolgens handmatig naar Azure verzenden.

In dit artikel leert u over:

* Gegevensvak instellen.
* Gegevens uit de on-premises VMware-omgeving kopiëren naar de Gegevensbox via NFS.
* Voorbereiden op de terugkeer van Data Box.
* Blob-gegevens voorbereiden voor kopiëren naar Azure VMware-oplossing.
* Het kopiëren van de gegevens van Azure naar uw privécloud.

## <a name="scenarios"></a>Scenario's

Gegevensvak gebruiken in de volgende scenario's voor bulkgegevensmigratie:

* Een grote hoeveelheid gegevens migreren van on-premises naar Azure VMware-oplossing. Met deze methode wordt een basislijn vastgesteld en worden verschillen over het netwerk gesynchroniseerd.
* Een groot aantal virtuele machines migreren die zijn uitgeschakeld (koude virtuele machines).
* Virtuele machinegegevens migreren voor het instellen van ontwikkel- en testomgevingen.
* Als u een groot aantal virtuele machinesjablonen, ISO-bestanden en virtuele machineschijven wilt migreren.

## <a name="before-you-begin"></a>Voordat u begint

* Schakel de vereisten in en [bestel gegevensvak](../databox/data-box-deploy-ordered.md) via uw Azure-portal. Tijdens het bestelproces moet u een opslagaccount selecteren waarmee Blob-opslag wordt opgeslagen. Nadat u het Data Box-apparaat hebt ontvangen, sluit u het aan op uw on-premises netwerk en [stelt u het apparaat in](../databox/data-box-deploy-set-up.md) met een IP-adres dat bereikbaar is vanuit uw vSphere-beheernetwerk.

* Maak een virtueel netwerk en een opslagaccount in dezelfde regio waar uw Azure VMware-oplossing is ingericht.

* Maak een [virtuele Azure-netwerkverbinding](cloudsimple-azure-network-connection.md) vanuit uw privécloud naar het virtuele netwerk waar het opslagaccount wordt gemaakt door de stappen in het virtuele Netwerk van Connect Azure naar CloudSimple te volgen [met ExpressRoute.](virtual-network-connection.md)

## <a name="set-up-data-box-for-nfs"></a>Gegevensvak instellen voor NFS

Maak verbinding met de lokale webgebruikersinterface van uw gegevensbox door de stappen te volgen in het gedeelte 'Verbinding maken met uw apparaat' van [Zelfstudie: Kabel en verbinding maken met uw Azure-gegevensvak.](../databox/data-box-deploy-set-up.md)  Gegevensvak configureren om toegang te verlenen tot NFS-clients:

1. Ga in de lokale webgebruikersinterface naar de pagina **Verbinden en kopiëren.** Selecteer **NFS-clienttoegang**onder **NFS-instellingen**. 

    ![NFS-clienttoegang configureren 1](media/nfs-client-access.png)

2. Voer het IP-adres van de VMware ESXi-hosts in en selecteer **Toevoegen**. U de toegang voor alle hosts in uw vSphere-cluster configureren door deze stap te herhalen. Selecteer **OK**.

    ![NFS-clienttoegang configureren 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **Maak altijd een map voor de bestanden die u van plan bent te kopiëren in de bestandsshare en kopieer de bestanden vervolgens naar die map**. De map gemaakt onder shares met blok-blobs en pagina-blobs vertegenwoordigt een container waarnaar gegevens als blobs worden geüpload. U bestanden niet rechtstreeks naar de *hoofdmap* in het opslagaccount kopiëren.

Onder blok-blob- en pagina-blob-shares zijn entiteiten op het eerste niveau containers en entiteiten op het tweede niveau blobs. Onder aandelen voor Azure Files zijn entiteiten op het eerste niveau aandelen en entiteiten op het tweede niveau bestanden.

In de volgende tabel ziet u het UNC-pad naar de shares op uw Data Box en de URL van het Azure Storage-pad waarnaar de gegevens worden geüpload. De uiteindelijke URL van het Azure Storage-pad kan worden afgeleid van het UNC-pad naar de shares.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure-blok-blobs | <li>UNC-pad naar shares: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-pagina-blobs  | <li>UNC-pad naar shares: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>UNC-pad naar shares: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> Gebruik Azure Block-blobs voor het kopiëren van VMware-gegevens.

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>Monteer het NFS-aandeel als een gegevensarchief op uw on-premises vCenter-cluster en kopieer de gegevens

Het NFS-aandeel uit uw gegevensvak moet worden gemonteerd als een gegevensarchief op uw on-premises vCenter-cluster of VMware ESXi-host om de gegevens naar het NFS-gegevensarchief te kopiëren:

1. Meld u aan bij uw on-premises vCenter-server.

2. Klik met de rechtermuisknop op **Datacenter,** selecteer **Opslag,** selecteer **Nieuw gegevensarchief**en selecteer **Volgende**.

   ![Nieuwe datastore toevoegen](media/databox-migration-add-datastore.png)

3. Selecteer **NFS** in stap 1 van de wizard Gegevensarchief toevoegen onder **Type**.

   ![Nieuwe datastore toevoegen - type](media/databox-migration-add-datastore-type.png)

4. Selecteer **NFS 3** in stap 2 van de wizard als NFS-versie en selecteer **Volgende**.

   ![Nieuwe datastore toevoegen - NFS-versie](media/databox-migration-add-datastore-nfs-version.png)

5. Geef in stap 3 van de wizard de naam op voor het gegevensarchief, het pad en de server. U het IP-adres van uw gegevensbox voor de server gebruiken. Het mappad is `/<StorageAccountName_BlockBlob>/<ContainerName>/` in de indeling.

   ![Nieuwe datastore toevoegen - NFS-configuratie](media/databox-migration-add-datastore-nfs-configuration.png)

6. Selecteer in stap 4 van de wizard de ESXi-hosts waar u het gegevensarchief wilt monteren en selecteer **Volgende**.  Selecteer in een cluster alle hosts om de migratie van de virtuele machines te garanderen.

   ![Nieuwe datastore toevoegen - Hosts selecteren](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. Bekijk in stap 5 van de wizard het overzicht en selecteer **Voltooien**.

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>Gegevens kopiëren naar het GEGEVENSvak NFS-gegevensarchief

Virtuele machines kunnen worden gemigreerd of gekloond naar het nieuwe datastore.  Ongebruikte virtuele machines die u wilt migreren, kunnen worden gemigreerd naar het Data Box NFS-gegevensarchief met behulp van de optie **opslagvMotion.** Actieve virtuele machines kunnen worden gekloond naar het Data Box NFS-gegevensarchief.

* Identificeer en vermeld de virtuele machines die kunnen worden **verplaatst.**
* Identificeer en vermeld de virtuele machines die moeten worden **gekloond.**

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>Een virtuele machine verplaatsen naar een gegevensarchief van een gegevensarchief

1. Klik met de rechtermuisknop op de virtuele machine die u naar het gegevensarchief Van het gegevensvak wilt verplaatsen en selecteer **Vervolgens Migreren**.

    ![Virtuele machine migreren](media/databox-migration-vm-migrate.png)

2. Selecteer **Alleen opslag wijzigen** voor het migratietype en selecteer **Volgende**.

    ![Virtuele machine migreren - alleen opslag](media/databox-migration-vm-migrate-change-storage.png)

3. Selecteer **Databox-Datastore** als bestemming en selecteer **Volgende**.

    ![Virtuele machine migreren - datastore selecteren](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. Bekijk de informatie en selecteer **Voltooien**.

5. Herhaal stap 1 tot en met 4 voor extra virtuele machines.

> [!TIP]
> U meerdere virtuele machines selecteren die zich in dezelfde energiestatus bevinden (ingeschakeld of uitgeschakeld) en deze in bulk migreren.

De virtuele machine wordt gemigreerd naar het NFS-gegevensarchief vanuit Data Box. Nadat alle virtuele machines zijn gemigreerd, u de actieve virtuele machines uitschakelen (afsluiten) ter voorbereiding op de migratie van gegevens naar Azure VMware Solution.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>Een virtuele machine of een sjabloon voor virtuele machines klonen naar het datastore van de gegevensarchief

1. Klik met de rechtermuisknop op een virtuele machine of een sjabloon voor virtuele machines die u wilt klonen. Selecteer **Kloonkloon** > **naar virtuele machine**.

    ![Virtuele machine kloon](media/databox-migration-vm-clone.png)

2. Selecteer een naam voor de gekloonde virtuele machine of de sjabloon voor virtuele machines.

3. Selecteer de map waar u het gekloonde object wilt plaatsen en selecteer **Volgende**.

4. Selecteer het cluster of de resourcegroep waar u het gekloonde object wilt plaatsen en selecteer **Volgende**.

5. Selecteer **Databox-Datastore** als opslaglocatie en selecteer **Volgende**.

    ![Virtuele machine kloon - selecteer datastore](media/databox-migration-vm-clone-select-datastore.png)

6. Als u opties voor het gekloonde object wilt aanpassen, selecteert u de aanpassingsopties en selecteert u **Volgende**.

7. Bekijk de configuraties en selecteer **Voltooien**.

8. Herhaal stap 1 tot en met 7 voor extra virtuele machines of virtuele machinesjablonen.

Virtuele machines worden gekloond en opgeslagen in het NFS-gegevensarchief van Data Box. Nadat de virtuele machines zijn gekloond, moet u ervoor zorgen dat ze zijn uitgeschakeld ter voorbereiding op de migratie van gegevens naar Azure VMware-oplossing.

### <a name="copy-iso-files-to-the-data-box-datastore"></a>ISO-bestanden kopiëren naar het gegevensarchief van de gegevensbox

1. Ga vanuit uw on-premises vCenter-webgebruikersinterface naar **Storage.**  Selecteer **Databox-Datastore** en selecteer **Vervolgens Bestanden**. Maak een nieuwe map voor het opslaan van ISO-bestanden.

    ![ISO kopiëren - nieuwe map maken](media/databox-migration-create-folder.png)

2. Geef een naam op voor de map waar ISO-bestanden worden opgeslagen.

3. Dubbelklik op de nieuw gemaakte map om deze te openen.

4. Selecteer **Bestanden uploaden** en selecteer vervolgens de ISO-bestanden die u wilt uploaden.
    
    ![ISO kopiëren - bestanden uploaden](media/databox-migration-upload-iso.png)

> [!TIP]
> Als u al ISO-bestanden in uw on-premises gegevensarchief hebt, u de bestanden selecteren en **kopiëren om** de bestanden naar het DATA Box NFS-gegevensarchief te kopiëren.


## <a name="prepare-data-box-for-return"></a>Gegevensvak voorbereiden voor retourneren

Nadat alle virtuele machinegegevens, sjabloongegevens voor virtuele machines en ISO-bestanden zijn gekopieerd naar het DATA Box NFS-gegevensarchief, u het gegevensarchief loskoppelen van uw vCenter. Alle virtuele machines en sjablonen voor virtuele machines moeten uit de voorraad worden verwijderd voordat u de gegevenswinkel loskoppelt.

### <a name="remove-objects-from-inventory"></a>Objecten uit voorraad verwijderen

1. Ga vanuit uw on-premises vCenter-webgebruikersinterface naar **Storage.** Selecteer **Databox-Datastore** en selecteer vervolgens **VM's**.

    ![Virtuele machines uit voorraad verwijderen - uitgeschakeld](media/databox-migration-select-databox-vm.png)

2. Zorg ervoor dat alle virtuele machines zijn uitgeschakeld.

3. Selecteer alle virtuele machines, klik met de rechtermuisknop en selecteer **Verwijderen uit voorraad**.

    ![Virtuele machines uit voorraad verwijderen](media/databox-migration-remove-vm-from-inventory.png)

4. Selecteer **VM-sjablonen in mappen** en herhaal stap 3.

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>Het GEGEVENSvak NFS-gegevensarchief verwijderen uit vCenter

Het Data Box NFS-gegevensarchief moet worden losgekoppeld van VMware ESXi-hosts voordat u zich voorbereidt op terugkeer.

1. Ga vanuit uw on-premises vCenter-webgebruikersinterface naar **Storage.**

2. Klik met de rechtermuisknop op **Databox-Datastore** en selecteer **Datastore ontkoppelen**.

    ![Gegevensarchief gegevensarchief uit de ontkoppeling ontkoppelen](media/databox-migration-unmount-datastore.png)

3. Selecteer alle ESXi-hosts waar het gegevensarchief is gemonteerd en selecteer **OK**.

    ![Gegevensarchief gegevensarchief uitdemonteren - hosts selecteren](media/databox-migration-unmount-datastore-select-hosts.png)

4. Eventuele waarschuwingen controleren en accepteren en selecteer **OK**.

### <a name="prepare-data-box-for-return-and-then-return-it"></a>Gegevensvak voorbereiden voor retour en het retourneren

Volg de stappen die zijn beschreven in het artikel [Retourazure-gegevensvak en controleer het uploaden van gegevens naar Azure om](../databox/data-box-deploy-picked-up.md) het gegevensvak terug te sturen. Controleer de status van de gegevenskopie naar uw Azure-opslagaccount. Nadat de status is weergegeven als voltooid, u de gegevens in uw Azure-opslagaccount verifiëren.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>Gegevens uit Azure-opslag kopiëren naar Azure VMware-oplossing

Gegevens die naar uw Data Box-apparaat worden gekopieerd, zijn beschikbaar op uw Azure-opslagaccount nadat de orderstatus van uw gegevensvak is weergegeven als voltooid. De gegevens kunnen nu worden gekopieerd naar uw Azure VMware-oplossing. Gegevens in het opslagaccount moeten worden gekopieerd naar het vSAN-gegevensarchief van uw privécloud met behulp van het NFS-protocol. 

Kopieer eerst Blob-opslaggegevens naar een beheerde schijf op een virtuele Linux-machine in Azure met **AzCopy.** Maak de beheerde schijf beschikbaar via NFS, monteer het NFS-aandeel als gegevensarchief in uw privécloud en kopieer de gegevens. Met deze methode u de gegevens sneller kopiëren naar uw private cloud.

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>Kopieer gegevens naar uw privécloud met behulp van een Virtuele Linux-machine en beheerde schijven en exporteer vervolgens naar nfs-share

1. Maak een [Virtuele Linux-machine](../virtual-machines/linux/quick-create-portal.md) in Azure in dezelfde regio waar uw opslagaccount is gemaakt en een virtuele Azure-netwerkverbinding met uw privécloud heeft.

2. Maak een beheerde schijf waarvan de opslagcapaciteit groter is dan de hoeveelheid blobgegevens en [bevestig deze aan uw virtuele Linux-machine.](../virtual-machines/linux/attach-disk-portal.md)  Als de hoeveelheid blobgegevens groter is dan de capaciteit van de grootste beheerde schijf die beschikbaar is, moeten de gegevens in meerdere stappen worden gekopieerd of met meerdere beheerde schijven.

3. Maak verbinding met de virtuele Linux-machine en monteer de beheerde schijf.

4. Installeer [AzCopy op uw Linux virtuele machine.](../storage/common/storage-use-azcopy-v10.md)

5. Download de gegevens van uw Azure Blob-opslag op de beheerde schijf met AzCopy.  Opdrachtsyntaxis: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"`.  Vervang `<storage-account-name>` door de naam `<container-name>` van uw Azure-opslagaccount en door de container die de gegevens bevat die via gegevensvak worden gekopieerd.

6. Installeer de NFS-server op uw Virtuele Linux-machine:

    - Op een Ubuntu/Debian `sudo apt install nfs-kernel-server`distributie: .
    - Op een Enterprise `sudo yum install nfs-utils`Linux distributie: .

7. Wijzig de machtiging van de map op uw beheerde schijf waar gegevens uit Azure Blob-opslag zijn gekopieerd.  Wijzig de machtigingen voor alle mappen die u wilt exporteren als een NFS-share.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. Machtigingen toewijzen voor IP-adressen van client om toegang te `/etc/exports` krijgen tot het NFS-aandeel door het bestand te bewerken.

    ```bash
    sudo vi /etc/exports
    ```
    
    Voer de volgende regels in het bestand in voor elk IP-host-adres van uw privécloud.  Als u shares maakt voor meerdere mappen, voegt u alle mappen toe.

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. Exporteer de NFS-shares `sudo exportfs -a` met behulp van de opdracht.

10. Start de NFS-kernelserver opnieuw op met de `sudo systemctl restart nfs-kernel-server` opdracht.


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-a-private-cloud-vcenter-cluster-and-then-copy-data"></a>Monteer het NFS-aandeel van de Virtuele Linux-machine als een gegevensarchief op een vCenter-cluster in de private cloud en kopieer vervolgens gegevens

Het NFS-aandeel van uw Virtuele Linux-machine moet worden gemonteerd als een datastore op uw private cloud vCenter-cluster. Nadat deze is gemonteerd, kunnen gegevens worden gekopieerd van het NFS-gegevensarchief naar het cloudvSAN-gegevensarchief.

1. Meld u aan bij uw private cloud vCenter-server.

2. Klik met de rechtermuisknop op **Datacenter,** selecteer **Opslag,** selecteer **Nieuw gegevensarchief**en selecteer **Volgende**.

   ![Nieuwe datastore toevoegen](media/databox-migration-add-datastore.png)

3. Selecteer in stap 1 van de wizard Datastore toevoegen het **NFS-type.**

   ![Nieuwe datastore toevoegen - type](media/databox-migration-add-datastore-type.png)

4. Selecteer **NFS 3** in stap 2 van de wizard als NFS-versie en selecteer **Volgende**.

   ![Nieuwe datastore toevoegen - NFS-versie](media/databox-migration-add-datastore-nfs-version.png)

5. Geef in stap 3 van de wizard de naam op voor het gegevensarchief, het pad en de server.  U het IP-adres van uw Linux virtuele machine voor de server gebruiken.  Het mappad is `/<folder>/<subfolder>/` in de indeling.

   ![Nieuwe datastore toevoegen - NFS-configuratie](media/databox-migration-add-datastore-nfs-configuration.png)

6. Selecteer in stap 4 van de wizard de ESXi-hosts waar u het gegevensarchief wilt monteren en selecteer **Volgende**.  Selecteer in een cluster alle hosts om de migratie van de virtuele machines te garanderen.

   ![Nieuwe datastore toevoegen - Hosts selecteren](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. Bekijk in stap 5 van de wizard het overzicht en selecteer **Voltooien**.

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>Virtuele machines en sjablonen voor virtuele machines toevoegen van een NFS-gegevensarchief aan de voorraad

1. Ga vanuit uw vCenter-webgebruikersvoor de private cloud naar **Storage**.  Selecteer een NFS-gegevensarchief van een Virtuele Linux-machine en selecteer **Vervolgens Bestanden**.

    ![Bestanden selecteren in NFS-gegevensarchief](media/databox-migration-datastore-select-files.png)

2. Selecteer een map met een virtuele machine of een sjabloon voor virtuele machines.  Selecteer in het detailvenster een .vmx-bestand voor een virtuele machine of een .vmtx-bestand voor een sjabloon voor virtuele machines.

3. Selecteer **VM registreren** om de virtuele machine te registreren op uw private cloud vCenter.

    ![Virtuele machine registreren](media/databox-migration-datastore-register-vm.png)

4. Selecteer het datacenter, de map en de cluster/resourcegroep waar u de virtuele machine wilt registreren.

4. Herhaal stap 3 en 4 voor alle virtuele machines en virtuele machinesjablonen.

5. Ga naar de map met de ISO-bestanden.  Selecteer de ISO-bestanden en selecteer **Kopiëren om** de bestanden naar een map in uw vSAN-gegevensarchief te kopiëren.

De virtuele machines en virtuele machinesjablonen zijn nu beschikbaar op uw private cloud vCenter. Deze virtuele machines moeten worden verplaatst van het NFS-gegevensarchief naar het vSAN-gegevensarchief voordat u ze inschakelt. U de **optie opslagvMotion** gebruiken en het vSAN-gegevensarchief selecteren als doel voor de virtuele machines.

De virtuele machinesjablonen moeten worden gekloond van uw Linux virtuele machine NFS datastore naar uw vSAN datastore.

### <a name="clean-up-your-linux-virtual-machine"></a>Ruim je Virtuele Linux-machine op

Nadat alle gegevens naar uw privécloud zijn gekopieerd, u het NFS-gegevensarchief uit uw privécloud verwijderen:

1. Zorg ervoor dat alle virtuele machines en sjablonen worden verplaatst en gekloond naar uw vSAN-gegevensarchief.

2. Verwijder alle sjablonen voor virtuele machines uit het NFS-gegevensarchief uit de voorraad.

3. Demonteer de Linux virtuele machine datastore uit uw private cloud vCenter.

4. Verwijder de virtuele machine en beheerde schijf uit Azure.

5. Als u de gegevens die door Data Box zijn overgedragen niet wilt bewaren in uw opslagaccount, verwijdert u het Azure-opslagaccount.  
    


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [gegevensvak](../databox/data-box-overview.md).
* Meer informatie over verschillende opties voor [het migreren van workloads naar uw privécloud.](migrate-workloads.md)
