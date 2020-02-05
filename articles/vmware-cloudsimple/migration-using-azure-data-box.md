---
title: 'Azure VMware-oplossing: migratie met behulp van Azure Data Box'
description: Azure Data Box gebruiken om gegevens bulksgewijs te migreren naar de Azure VMware-oplossing.
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f368ad7cf9b83195e35a2283de7a3644cc9fc317
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019754"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>Gegevens migreren naar Azure VMware-oplossing met behulp van Azure Data Box

Met de Microsoft Azure Data Box Cloud oplossing kunt u op een snelle, goedkope en betrouw bare manier terabytes (TBs) van gegevens naar Azure verzenden. De veilige gegevensoverdracht wordt versneld door u een systeemeigen Data Box-opslagapparaat toe te sturen. Elk opslag apparaat heeft een maximale bruikbare opslag capaciteit van 80 TB en wordt naar uw Data Center getransporteerd door een regionale luchtvaart maatschappij. Het apparaat heeft een robuuste behuizing om uw gegevens tijdens de overdracht te beschermen en te beveiligen.

Met Data Box kunt u uw VMware-gegevens bulksgewijs migreren naar de Privécloud van uw AVS. Gegevens uit uw on-premises VMware vSphere omgeving worden naar Data Box gekopieerd via het NFS-protocol (Network File System). Bij de migratie van bulk gegevens wordt een tijdgebonden kopie van virtuele machines, configuratie en gekoppelde gegevens opgeslagen in Data Box en vervolgens hand matig naar Azure verzonden.

In dit artikel vindt u meer informatie over:

* Data Box instellen.
* Gegevens kopiëren van de on-premises VMware-omgeving naar het Data Box door middel van NFS.
* Voorbereiden op het retour neren van Data Box.
* BLOB-gegevens voorbereiden om te kopiëren naar de Azure VMware-oplossing.
* De gegevens van Azure naar de Privécloud van uw AVS kopiëren.

## <a name="scenarios"></a>Scenario's

Gebruik Data Box in de volgende scenario's voor bulk gegevens migratie:

* Om een grote hoeveelheid gegevens te migreren van on-premises naar Azure VMware-oplossing. Met deze methode maakt u een basis lijn en synchroniseert u verschillen via het netwerk.
* Voor het migreren van een groot aantal virtuele machines dat is uitgeschakeld (koude virtuele machines).
* Om de gegevens van de virtuele machine voor het instellen van ontwikkel-en test omgevingen te migreren.
* Om een groot aantal virtuele-machine sjablonen, ISO-bestanden en virtuele-machine schijven te migreren.

## <a name="before-you-begin"></a>Voordat u begint

* Controleer de vereisten en [bestel data Box](../databox/data-box-deploy-ordered.md) via uw Azure Portal. Tijdens het order proces moet u een opslag account selecteren dat Blob Storage mogelijk maakt. Nadat u het Data Box apparaat hebt ontvangen, verbindt u het met uw on-premises netwerk en [stelt u het apparaat](../databox/data-box-deploy-set-up.md) in met een IP-adres dat bereikbaar is vanuit uw vSphere-beheer netwerk.

* Maak een virtueel netwerk en een opslag account in dezelfde regio waarin uw Azure VMware-oplossing is ingericht.

* Maak een [verbinding met een virtueel Azure-netwerk](cloudsimple-azure-network-connection.md) van uw AVS-privécloud naar het virtuele netwerk waar het opslag account wordt gemaakt door de stappen in verbinding maken met het [virtuele netwerk van Azure met ExpressRoute te](virtual-network-connection.md)volgen.

## <a name="set-up-data-box-for-nfs"></a>Data Box instellen voor NFS

Maak verbinding met uw Data Box lokale web-UI door de stappen in de sectie ' verbinding maken met uw apparaat ' in de [zelf studie te volgen: kabel en verbinding maken met uw Azure data Box](../databox/data-box-deploy-set-up.md). Data Box configureren om toegang tot NFS-clients toe te staan:

1. Ga in de lokale web-UI naar de pagina **verbinding maken en kopiëren** . Onder **NFS-instellingen**selecteert u **NFS-client toegang**. 

    ![NFS-clienttoegang configureren 1](media/nfs-client-access.png)

2. Voer het IP-adres van de VMware ESXi hosts in en selecteer **toevoegen**. U kunt de toegang voor alle hosts in uw vSphere-cluster configureren door deze stap te herhalen. Selecteer **OK**.

    ![NFS-clienttoegang configureren 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **Maak altijd een map voor de bestanden die u van plan bent te kopiëren in de bestandsshare en kopieer de bestanden vervolgens naar die map**. De map gemaakt onder shares met blok-blobs en pagina-blobs vertegenwoordigt een container waarnaar gegevens als blobs worden geüpload. U kunt bestanden niet rechtstreeks kopiëren naar de *hoofdmap* van het opslag account.

Onder blok-blob- en pagina-blob-shares zijn entiteiten op het eerste niveau containers en entiteiten op het tweede niveau blobs. Onder shares voor Azure Files zijn de entiteiten van het eerste niveau shares en entiteiten van het tweede niveau bestanden.

In de volgende tabel ziet u het UNC-pad naar de shares op uw Data Box en de URL van het Azure Storage-pad waarnaar de gegevens worden geüpload. De uiteindelijke URL van het Azure Storage-pad kan worden afgeleid van het UNC-pad naar de shares.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure-blok-blobs | <li>UNC-pad naar shares: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-pagina-blobs  | <li>UNC-pad naar shares: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>UNC-pad naar shares: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> Gebruik Azure Block blobs voor het kopiëren van VMware-gegevens.

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>Koppel de NFS-share als een gegevens opslag in uw on-premises vCenter-cluster en kopieer de gegevens

De NFS-share van uw Data Box moet worden gekoppeld als een gegevens opslag in uw on-premises vCenter-cluster of VMware ESXi-host om de gegevens naar de NFS-gegevens opslag te kopiëren:

1. Meld u aan bij uw on-premises vCenter-Server.

2. Klik met de rechter muisknop op **Data Center**, selecteer **opslag**, selecteer **nieuwe gegevens opslag**en selecteer vervolgens **volgende**.

   ![Nieuwe gegevens opslag toevoegen](media/databox-migration-add-datastore.png)

3. Selecteer in stap 1 van de wizard Data Store toevoegen onder **type**de optie **NFS** .

   ![Nieuwe gegevens opslag toevoegen-type](media/databox-migration-add-datastore-type.png)

4. Selecteer in stap 2 van de wizard de optie **NFS 3** als NFS-versie en selecteer vervolgens **volgende**.

   ![Nieuwe Data Store-NFS-versie toevoegen](media/databox-migration-add-datastore-nfs-version.png)

5. Geef in stap 3 van de wizard de naam op voor de gegevens opslag, het pad en de server. U kunt het IP-adres van uw Data Box voor de-server gebruiken. Het mappad bevindt zich in de `/<StorageAccountName_BlockBlob>/<ContainerName>/`-indeling.

   ![Nieuwe Data Store-NFS-configuratie toevoegen](media/databox-migration-add-datastore-nfs-configuration.png)

6. Selecteer in stap 4 van de wizard de ESXi-hosts waar u de gegevens opslag wilt koppelen en selecteer vervolgens **volgende**. Selecteer in een cluster alle hosts om te controleren of de virtuele machines zijn gemigreerd.

   ![Nieuwe gegevens opslag toevoegen-hosts selecteren](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. Bekijk in stap 5 van de wizard de samen vatting en selecteer **volt ooien**.

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>Gegevens kopiëren naar de Data Box NFS-gegevens opslag

Virtuele machines kunnen worden gemigreerd of gekloond naar de nieuwe gegevens opslag. Alle ongebruikte virtuele machines die u wilt migreren, kunnen worden gemigreerd naar het Data Box NFS-gegevens archief met behulp van de optie **opslag vMotion** . Actieve virtuele machines kunnen worden gekloond naar het Data Box NFS-gegevens archief.

* De virtuele machines identificeren en weer geven die kunnen worden **verplaatst**.
* Identificeer en vermeld de virtuele machines die moeten worden **gekloond**.

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>Een virtuele machine verplaatsen naar een Data Box gegevens opslag

1. Klik met de rechter muisknop op de virtuele machine die u wilt verplaatsen naar de Data Box gegevens opslag en selecteer vervolgens **migreren**.

    ![Virtuele machine migreren](media/databox-migration-vm-migrate.png)

2. Selecteer **alleen opslag wijzigen** voor het migratie type en selecteer vervolgens **volgende**.

    ![Virtuele machine migreren-alleen opslag](media/databox-migration-vm-migrate-change-storage.png)

3. Selecteer **Databox-Data Store** als doel en selecteer vervolgens **volgende**.

    ![Virtuele machine migreren: Selecteer gegevens opslag](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. Controleer de informatie en selecteer **volt ooien**.

5. Herhaal stap 1 tot en met 4 voor aanvullende virtuele machines.

> [!TIP]
> U kunt meerdere virtuele machines selecteren die zich in dezelfde energie status bevinden (ingeschakeld of uitgeschakeld) en deze bulksgewijs migreren.

De virtuele machine wordt gemigreerd naar de NFS-gegevens opslag van Data Box. Nadat alle virtuele machines zijn gemigreerd, kunt u de actieve virtuele machines in de voor bereiding voor de migratie van gegevens naar de Azure VMware-oplossing uitschakelen (afsluiten).

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>Een virtuele machine of een virtuele-machine sjabloon klonen naar de Data Box gegevens opslag

1. Klik met de rechter muisknop op een virtuele machine of een virtuele-machine sjabloon die u wilt klonen. Selecteer **klonen** > **klonen naar de virtuele machine**.

    ![Virtuele machine klonen](media/databox-migration-vm-clone.png)

2. Selecteer een naam voor de gekloonde virtuele machine of de virtuele-machine sjabloon.

3. Selecteer de map waarin u het gekloonde object wilt plaatsen en selecteer vervolgens **volgende**.

4. Selecteer het cluster of de resource groep waar u het gekloonde object wilt plaatsen en selecteer vervolgens **volgende**.

5. Selecteer **Databox-Data Store** als de opslag locatie en selecteer vervolgens **volgende**.

    ![Virtuele machine klonen: Selecteer gegevens opslag](media/databox-migration-vm-clone-select-datastore.png)

6. Als u opties voor het gekloonde object wilt aanpassen, selecteert u de aanpassings opties en selecteert u **volgende**.

7. Controleer de configuraties en selecteer **volt ooien**.

8. Herhaal stap 1 tot en met 7 voor aanvullende virtuele machines of virtuele-machine sjablonen.

Virtuele machines worden gekloond en opgeslagen in de NFS-gegevens opslag van Data Box. Nadat de virtuele machines zijn gekloond, moet u ervoor zorgen dat ze worden afgesloten voor de voor bereiding op de migratie van gegevens naar de Azure VMware-oplossing.

### <a name="copy-iso-files-to-the-data-box-datastore"></a>ISO-bestanden naar de Data Box gegevens opslag kopiëren

1. Ga in uw on-premises vCenter-webinterface naar **opslag**. Selecteer **Databox-Data Store** en selecteer vervolgens **bestanden**. Een nieuwe map maken voor het opslaan van ISO-bestanden.

    ![ISO kopiëren: nieuwe map maken](media/databox-migration-create-folder.png)

2. Geef een naam op voor de map waarin de ISO-bestanden worden opgeslagen.

3. Dubbel klik op de map die u zojuist hebt gemaakt om deze te openen.

4. Selecteer **bestanden uploaden** en selecteer vervolgens de ISO-bestanden die u wilt uploaden.
    
    ![ISO-Upload bestanden kopiëren](media/databox-migration-upload-iso.png)

> [!TIP]
> Als u al ISO-bestanden hebt in uw on-premises gegevens opslag, kunt u de bestanden selecteren en **kopiëren naar** om de bestanden te kopiëren naar de data Box NFS-gegevens opslag.


## <a name="prepare-data-box-for-return"></a>Data Box voorbereiden voor retour neren

Nadat alle gegevens van de virtuele machine, de gegevens van de virtuele-machine sjabloon en ISO-bestanden zijn gekopieerd naar de Data Box NFS-Data Store, kunt u de gegevens opslag loskoppelen van uw vCenter. Alle virtuele machines en virtuele-machine sjablonen moeten uit de inventarisatie worden verwijderd voordat u de gegevens opslag verbreekt.

### <a name="remove-objects-from-inventory"></a>Objecten uit de inventaris verwijderen

1. Ga in uw on-premises vCenter-webinterface naar **opslag**. Selecteer **Databox-Data Store** en selecteer vervolgens **vm's**.

    ![Virtuele machines uit de voor Raad verwijderen-uitgeschakeld](media/databox-migration-select-databox-vm.png)

2. Zorg ervoor dat alle virtuele machines zijn afgesloten.

3. Selecteer alle virtuele machines, klik met de rechter muisknop en selecteer vervolgens **verwijderen uit inventarisatie**.

    ![Virtuele machines uit de inventaris verwijderen](media/databox-migration-remove-vm-from-inventory.png)

4. Selecteer **VM-sjablonen in mappen** en herhaal stap 3.

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>De Data Box NFS-gegevens opslag uit de vCenter verwijderen

De verbinding tussen de Data Box NFS-gegevens opslag en VMware ESXi hosts moet worden verbroken voordat het resultaat wordt voor bereid.

1. Ga in uw on-premises vCenter-webinterface naar **opslag**.

2. Klik met de rechter muisknop op **Databox-Data Store** en selecteer **Data Store ontkoppelen**.

    ![Data Box gegevens opslag ontkoppelen](media/databox-migration-unmount-datastore.png)

3. Selecteer alle ESXi-hosts waar het gegevens archief is gekoppeld en selecteer **OK**.

    ![Data Box Data Store ontkoppelen-hosts selecteren](media/databox-migration-unmount-datastore-select-hosts.png)

4. Controleer en accepteer eventuele waarschuwingen en selecteer **OK**.

### <a name="prepare-data-box-for-return-and-then-return-it"></a>Data Box voorbereiden voor retour neren en retour neren

Volg de stappen die worden beschreven in het artikel [retour Azure data box en controleer het uploaden van gegevens naar Azure](../databox/data-box-deploy-picked-up.md) om de data box te retour neren. Controleer de status van de gegevens kopie naar uw Azure Storage-account. Nadat de status is weer gegeven als voltooid, kunt u de gegevens in uw Azure Storage-account verifiëren.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>Gegevens kopiëren van Azure Storage naar de Azure VMware-oplossing

Gegevens die naar uw Data Box-apparaat zijn gekopieerd, zijn beschikbaar in uw Azure Storage-account nadat de order status van uw Data Box als voltooid wordt weer gegeven. De gegevens kunnen nu naar uw Azure VMware-oplossing worden gekopieerd. Gegevens in het opslag account moeten worden gekopieerd naar de vSAN-gegevens opslag van uw Privécloud in de Cloud met behulp van het NFS-protocol. 

Kopieer eerst Blob Storage-gegevens naar een beheerde schijf op een virtuele Linux-machine in azure met behulp van **AzCopy**. De beheerde schijf beschikbaar maken via NFS, de NFS-share koppelen als een gegevens opslag in de Privécloud van uw AVS en de gegevens vervolgens kopiëren. Met deze methode kunt u sneller een kopie van de gegevens naar de Privécloud van uw AVS maken.

### <a name="copy-data-to-your-avs-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>Gegevens kopiëren naar de Privécloud van de Cloud met behulp van een virtuele Linux-machine en beheerde schijven, en vervolgens exporteren als NFS-share

1. Maak een [virtuele Linux-machine](../virtual-machines/linux/quick-create-portal.md) in Azure in dezelfde regio waarin uw opslag account is gemaakt en een Azure Virtual Network-verbinding heeft met uw AVS-privécloud.

2. Maak een beheerde schijf waarvan de opslag capaciteit groter is dan de hoeveelheid BLOB-gegevens en [koppel deze aan uw virtuele Linux-machine](../virtual-machines/linux/attach-disk-portal.md). Als de hoeveelheid BLOB-gegevens groter is dan de capaciteit van de grootste beschik bare schijf, moeten de gegevens in meerdere stappen worden gekopieerd of meerdere beheerde schijven gebruiken.

3. Maak verbinding met de virtuele Linux-machine en koppel de beheerde schijf.

4. Installeer [AzCopy op uw virtuele Linux-machine](../storage/common/storage-use-azcopy-v10.md).

5. Down load de gegevens van uw Azure Blob-opslag naar de beheerde schijf met behulp van AzCopy. Opdracht syntaxis: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"`. Vervang `<storage-account-name>` door de naam van uw Azure Storage-account en `<container-name>` met de container die de gegevens bevat die via Data Box zijn gekopieerd.

6. Installeer de NFS-server op uw virtuele Linux-machine:

    - Op een Ubuntu/Debian-distributie: `sudo apt install nfs-kernel-server`.
    - In een Enter prise Linux-distributie: `sudo yum install nfs-utils`.

7. Wijzig de machtiging van de map op uw beheerde schijf waar gegevens uit Azure Blob Storage zijn gekopieerd. Wijzig de machtigingen voor alle mappen die u wilt exporteren als een NFS-share.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. Wijs machtigingen voor IP-adressen van clients toe om toegang te krijgen tot de NFS-share door het `/etc/exports`-bestand te bewerken.

    ```bash
    sudo vi /etc/exports
    ```
    
    Voer de volgende regels in het bestand in voor elke ESXi-host-IP van uw AVS-privécloud. Als u shares voor meerdere mappen maakt, moet u alle mappen toevoegen.

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. Exporteer de NFS-shares met behulp van de opdracht `sudo exportfs -a`.

10. Start de NFS-kernel-server opnieuw met behulp van de `sudo systemctl restart nfs-kernel-server` opdracht.


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-an-avs-private-cloud-vcenter-cluster-and-then-copy-data"></a>Koppel de virtuele Linux-machine met de NFS-share als een gegevens opslag op een AVS-cluster met een persoonlijke Cloud-vCenter en kopieer vervolgens gegevens

De NFS-share van uw virtuele Linux-machine moet worden gekoppeld als een gegevens opslag op uw AVS-cluster met de Privécloud-Cloud. Nadat deze is gekoppeld, kunnen gegevens uit de NFS-Data Store worden gekopieerd naar de vSAN gegevens opslag van de AVS.

1. Meld u aan bij uw AVS Privécloud-vCenter-Server.

2. Klik met de rechter muisknop op **Data Center**, selecteer **opslag**, selecteer **nieuwe gegevens opslag**en selecteer vervolgens **volgende**.

   ![Nieuwe gegevens opslag toevoegen](media/databox-migration-add-datastore.png)

3. Selecteer in stap 1 van de wizard Data Store toevoegen het **NFS** -type.

   ![Nieuwe gegevens opslag toevoegen-type](media/databox-migration-add-datastore-type.png)

4. Selecteer in stap 2 van de wizard de optie **NFS 3** als NFS-versie en selecteer vervolgens **volgende**.

   ![Nieuwe Data Store-NFS-versie toevoegen](media/databox-migration-add-datastore-nfs-version.png)

5. Geef in stap 3 van de wizard de naam op voor de gegevens opslag, het pad en de server. U kunt het IP-adres van uw virtuele Linux-machine gebruiken voor de-server. Het mappad bevindt zich in de `/<folder>/<subfolder>/`-indeling.

   ![Nieuwe Data Store-NFS-configuratie toevoegen](media/databox-migration-add-datastore-nfs-configuration.png)

6. Selecteer in stap 4 van de wizard de ESXi-hosts waar u de gegevens opslag wilt koppelen en selecteer vervolgens **volgende**. Selecteer in een cluster alle hosts om te controleren of de virtuele machines zijn gemigreerd.

   ![Nieuwe gegevens opslag toevoegen-hosts selecteren](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. Bekijk in stap 5 van de wizard de samen vatting en selecteer vervolgens **volt ooien**.

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>Virtuele machines en virtuele-machine sjablonen van een NFS-gegevens opslag toevoegen aan de inventaris

1. Ga vanuit uw AVS-webinterface voor persoonlijke Cloud vCenter naar **opslag**. Selecteer een virtuele Linux-machine met de NFS-gegevens opslag en selecteer vervolgens **bestanden**.

    ![Bestanden selecteren in de NFS-gegevens opslag](media/databox-migration-datastore-select-files.png)

2. Selecteer een map die een virtuele machine of een virtuele-machine sjabloon bevat. Selecteer in het detail venster een. vmx-bestand voor een virtuele machine of een. vmtx-bestand voor een virtuele-machine sjabloon.

3. Selecteer **VM registreren** om de virtuele machine te registreren op uw AVS Privécloud-vCenter.

    ![Virtuele machine registreren](media/databox-migration-datastore-register-vm.png)

4. Selecteer het Data Center, de map en het cluster of de resource groep waar u de virtuele machine wilt registreren.

4. Herhaal stap 3 en 4 voor alle virtuele machines en virtuele-machine sjablonen.

5. Ga naar de map met de ISO-bestanden. Selecteer de ISO-bestanden en selecteer **kopiëren naar** om de bestanden te kopiëren naar een map in uw vSAN-gegevens opslag.

De virtuele machines en virtuele-machine sjablonen zijn nu beschikbaar op uw AVS-Privécloud. Deze virtuele machines moeten worden verplaatst van de NFS-gegevens opslag naar de vSAN-gegevens opslag voordat u ze inschakelt. U kunt de optie **opslag vMotion** gebruiken en de vSAN-gegevens opslag als doel voor de virtuele machines selecteren.

De virtuele-machine sjablonen moeten worden gekloond van de NFS-gegevens opslag van uw virtuele Linux-machine naar uw vSAN-gegevens opslag.

### <a name="clean-up-your-linux-virtual-machine"></a>Uw virtuele Linux-machine opruimen

Nadat alle gegevens naar de Privécloud van uw AVS zijn gekopieerd, kunt u de NFS-gegevens opslag uit de Privécloud van uw AVS verwijderen:

1. Zorg ervoor dat alle virtuele machines en sjablonen zijn verplaatst en gekloond naar uw vSAN-gegevens opslag.

2. Verwijder uit de inventarisatie alle virtuele-machine sjablonen uit de NFS-gegevens opslag.

3. Ontkoppel de gegevens opslag van de virtuele Linux-machine uit uw AVS-Privécloud.

4. Verwijder de virtuele machine en de beheerde schijf uit Azure.

5. Als u de gegevens die zijn overgedragen door Data Box in uw opslag account niet wilt behouden, verwijdert u het Azure-opslag account. 
    


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Data Box](../databox/data-box-overview.md).
* Meer informatie over de verschillende opties voor [het migreren van werk belastingen naar de privécloud van uw AVS](migrate-workloads.md).
