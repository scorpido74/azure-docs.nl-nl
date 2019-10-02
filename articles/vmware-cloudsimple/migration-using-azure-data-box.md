---
title: 'Azure VMware-oplossing op CloudSimple: migratie met behulp van Azure Data Box'
description: Bulksgewijs gegevens migratie naar Azure VMware-oplossing door CloudSimple met behulp van Azure Data Box
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5f19b98fbbbad2f43227bfa2f73eab47bf7b1699
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817460"
---
# <a name="migrating-data-to-azure-vmware-solution-by-cloudsimple-using-azure-data-box"></a>Gegevens migreren naar Azure VMware-oplossing door CloudSimple met Azure Data Box

Met de cloudoplossing Microsoft Azure Data Box kunt u terabytes aan on-premises gegevens op een snelle, goedkope en betrouwbare manier naar Azure verzenden. De veilige gegevensoverdracht wordt versneld door u een systeemeigen Data Box-opslagapparaat toe te sturen. Elk opslagapparaat heeft een maximaal bruikbare opslagcapaciteit van 80 TB en wordt via een regionale koerier naar uw datacenter vervoerd. Dit apparaat heeft een stevige behuizing om uw gegevens tijdens het verzenden te beschermen.

Met Azure Data Box kunt u bulksgewijs uw VMware-gegevens migreren naar uw Privécloud.  Gegevens uit uw on-premises vSphere-omgeving worden gekopieerd naar de Data Box met NFS-protocol.  Bij de migratie van bulk gegevens moet u een tijdgebonden kopie van virtuele machines, configuratie en gekoppelde gegevens naar de Data Box kopiëren en naar Azure worden verzonden.

In dit artikel vindt u meer informatie over:

* Azure Data Box instellen.
* Het kopiëren van gegevens uit een on-premises VMware-omgeving naar de Data Box met behulp van NFS.
* Het retour neren van Azure Data Box wordt voor bereid.
* Prepare BLOB-gegevens voor kopiëren naar Azure VMware-oplossing door CloudSimple.
* De gegevens worden gekopieerd van Azure naar uw Privécloud.

## <a name="scenarios"></a>Scenario's

Azure Data Box moet worden gebruikt in de volgende scenario's voor bulk gegevens migratie.

* Migreer grote hoeveelheid gegevens van on-premises naar de Azure VMware-oplossing door CloudSimple om te worden gebruikt als basis voor het maken en synchroniseren van verschillen via het netwerk.
* Migreer een grote hoeveelheid uitgeschakelde virtuele machines (koude virtuele machines).
* Migreer de gegevens van de virtuele machine voor het instellen van ontwikkel-en test omgevingen.
* Migreer een groot aantal virtuele-machine sjablonen, Iso's, schijven van virtuele machines.

## <a name="before-you-begin"></a>Voordat u begint

* Controleer de vereisten en [volg orde data Box](../databox/data-box-deploy-ordered.md) van uw Azure Portal.  Tijdens het order proces moet u een opslag account selecteren, waarmee BLOBOPSLAG kan worden opgeslagen.  Zodra u de Data Box hebt ontvangen, verbindt u deze met uw on-premises netwerk en [stelt u de data Box](../databox/data-box-deploy-set-up.md) in met een IP-adres dat bereikbaar is vanuit uw vSphere-beheer netwerk.

* Maak een virtueel netwerk en een opslag account in dezelfde regio van Azure waar uw Azure VMware-oplossing door CloudSimple is ingericht.

* Maak een [virtuele Azure-netwerk verbinding](cloudsimple-azure-network-connection.md) vanuit uw privécloud naar het virtuele netwerk waar het opslag account wordt gemaakt met behulp van de stappen die worden beschreven in het artikel [verbinding maken tussen Azure Virtual Network en CloudSimple met behulp van ExpressRoute](virtual-network-connection.md)

## <a name="set-up-azure-data-box-for-nfs"></a>Azure Data Box instellen voor NFS

Verbinding maken met uw Azure Data Box lokale web-gebruikers interface met behulp van de stappen in de sectie **verbinding maken met uw apparaat** in het artikel [Tutorial: Kabel en maak verbinding met uw Azure Data Box @ no__t-0.  Configureer Data Box om toegang tot NFS-clients toe te staan.

1. Ga in de lokale gebruikersinterface naar de pagina **Verbinding maken en kopiëren**. Klik onder **NFS-instellingen** op **NFS-clienttoegang**. 

    ![NFS-clienttoegang configureren 1](media/nfs-client-access.png)

2. Voer het IP-adres van de VMware ESXi hosts in en klik op **toevoegen**. U kunt de toegang voor alle hosts in uw vSphere-cluster configureren door deze stap te herhalen. Klik op **OK**.

    ![NFS-clienttoegang configureren 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **Maak altijd een map voor de bestanden die u van plan bent te kopiëren in de bestandsshare en kopieer de bestanden vervolgens naar die map**. De map gemaakt onder shares met blok-blobs en pagina-blobs vertegenwoordigt een container waarnaar gegevens als blobs worden geüpload. Het is niet mogelijk om bestanden rechtstreeks te kopiëren naar de *root*-map in het opslagaccount.

Onder blok-blob- en pagina-blob-shares zijn entiteiten op het eerste niveau containers en entiteiten op het tweede niveau blobs. Onder shares voor Azure Files zijn entiteiten op het eerste niveau shares en entiteiten op het tweede niveau bestanden.

In de volgende tabel ziet u het UNC-pad naar de shares op uw Data Box en de URL van het Azure Storage-pad waarnaar de gegevens worden geüpload. De uiteindelijke URL van het Azure Storage-pad kan worden afgeleid van het UNC-pad naar de shares.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure-blok-blobs | <li>UNC-pad naar shares: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-pagina-blobs  | <li>UNC-pad naar shares: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>UNC-pad naar shares: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> Gebruik Azure Block blobs voor het kopiëren van VMware-gegevens.

## <a name="mount-nfs-share-as-a-datastore-on-on-premises-vcenter-cluster-and-copy-data"></a>NFS-share als een gegevens opslag koppelen aan een on-premises vCenter-cluster en gegevens kopiëren

De NFS-share van uw Azure Data Box moet worden gekoppeld als een gegevens opslag in uw on-premises vCenter-cluster of VMware ESXi-host om de gegevens te kopiëren.  Zodra de gegevens zijn gekoppeld, kunnen ze worden gekopieerd naar de NFS-Data Store.

1. Meld u aan bij uw on-premises vCenter-Server.

2. Klik met de rechter muisknop op het **Data Center**, selecteer **opslag** toevoegen klik op **nieuwe gegevens opslag** en klik op **volgende**

   ![Nieuwe gegevens opslag toevoegen](media/databox-migration-add-datastore.png)

3. Selecteer in stap 1 van de wizard Data Store toevoegen het type **NFS**.

   ![Nieuwe gegevens opslag toevoegen-type](media/databox-migration-add-datastore-type.png)

4. Selecteer in stap 2 **NFS 3** als NFS-versie en klik op **volgende**.

   ![Nieuwe Data Store-NFS-versie toevoegen](media/databox-migration-add-datastore-nfs-version.png)

5. Geef in stap 3 de naam voor de gegevens opslag, het pad en de server op.  U kunt het IP-adres van uw Data Box voor Server gebruiken.  Het mappad heeft de notatie `/<StorageAccountName_BlockBlob>/<ContainerName>/`.

   ![Nieuwe Data Store-NFS-configuratie toevoegen](media/databox-migration-add-datastore-nfs-configuration.png)

6. Selecteer in stap 4 de ESXi-hosts waar u de gegevens opslag wilt koppelen en klik op **volgende**.  Selecteer alle hosts in een cluster om de migratie van virtuele machines te controleren.

   ![Nieuwe gegevens opslag toevoegen-hosts selecteren](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. Bekijk in stap 5 de samen vatting en klik op **volt ooien**

## <a name="copy-data-to-data-box-nfs-datastore"></a>Gegevens kopiëren naar Data Box NFS-gegevens opslag

Virtuele machines kunnen worden gemigreerd of gekloond naar de nieuwe gegevens opslag.  Alle ongebruikte virtuele machines die moeten worden gemigreerd, kunnen worden gemigreerd naar Data Box NFS-gegevens opslag met behulp van **Storage vMotion**.  Actieve virtuele machines kunnen worden **gekloond** naar het data Box NFS-gegevens archief.

* De lijst met virtuele machines identificeren die kunnen worden **verplaatst**.
* De lijst met virtuele machines identificeren die moeten worden **gekloond**.

### <a name="move-virtual-machine-to-data-box-datastore"></a>Verplaats de virtuele machine naar Data Box gegevens opslag

1. Klik met de rechter muisknop op de virtuele machine die u wilt verplaatsen naar Data Box gegevens opslag en selecteer **migreren**.

    ![Virtuele machine migreren](media/databox-migration-vm-migrate.png)

2. Selecteer **alleen opslag** voor migratie type wijzigen en klik op **volgende**.

    ![Virtuele machine migreren-alleen opslag](media/databox-migration-vm-migrate-change-storage.png)

3. Selecteer Data Box gegevens opslag als doel en klik op **volgende**.

    ![Virtuele machine migreren: Selecteer gegevens opslag](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. Lees de informatie en klik op **volt ooien**.

5. Herhaal stap 1 tot en met 4 voor andere virtuele machines.

> [!TIP]
> U kunt meerdere virtuele machines in dezelfde energie status selecteren (ingeschakeld of uitgeschakeld) en deze bulksgewijs migreren.

De opslag voor de virtuele machine wordt gemigreerd naar het NFS-gegevens archief van Azure Data Box.  Zodra alle virtuele machines zijn gemigreerd, kunt u de ingeschakelde virtuele machines afsluiten in de voor bereiding op de migratie van gegevens naar de Azure VMware-oplossing.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-data-box-datastore"></a>Een virtuele machine of een virtuele-machine sjabloon klonen voor het Data Box gegevens opslag

1. Klik met de rechter muisknop op een virtuele machine of een virtuele-machine sjabloon die u wilt klonen.  Selecteer **klonen**, **klonen naar de virtuele machine**

    ![Virtuele machine klonen](media/databox-migration-vm-clone.png)

2. Selecteer een naam voor de gekloonde virtuele machine of de virtuele-machine sjabloon.

3. Selecteer de map waarin u het gekloonde object wilt plaatsen en klik op **volgende**.

4. Selecteer het cluster of de resource groep waar u het gekloonde object wilt plaatsen en klik op **volgende**.

5. Selecteer de Azure Data Box NFS-gegevens archief als opslag locatie en klik op **volgende**.

    ![Virtuele machine klonen: Selecteer gegevens opslag](media/databox-migration-vm-clone-select-datastore.png)

6. Selecteer de opties aanpassen als u de opties voor het gekloonde object wilt aanpassen en klik op **volgende**.

7. Controleer de configuraties en klik op **volt ooien**.

8. Herhaal stap 1 tot en met 7 voor aanvullende virtuele machines of virtuele-machine sjablonen.

Virtuele machines worden gekloond en opgeslagen in de NFS-gegevens opslag van Azure Data Box.  Wanneer de virtuele machines zijn gekloond, moet u ervoor zorgen dat de gekloonde virtuele machines zijn uitgeschakeld ter voor bereiding op de migratie van gegevens naar de Azure VMware-oplossing.

### <a name="copy-iso-files-to-data-box-datastore"></a>ISO-bestanden kopiëren naar Data Box gegevens opslag

1. Ga in uw on-premises vCenter-webinterface naar **opslag**.  Selecteer Data Box NFS-gegevens opslag en klik op **bestanden**.  Een **nieuwe map** maken voor het opslaan van ISO-bestanden.

    ![ISO kopiëren: nieuwe map maken](media/databox-migration-create-folder.png)

2. Geef een naam op voor de map waarin de ISO-bestanden worden opgeslagen.

3. Dubbel klik op de zojuist gemaakte map voor toegang tot de inhoud.

4. Klik op **bestanden uploaden** en selecteer de iso's die u wilt uploaden.
    
    ![ISO-Upload bestanden kopiëren](media/databox-migration-upload-iso.png)

> [!TIP]
> Als u al ISO-bestanden hebt in uw on-premises gegevens opslag, kunt u de bestanden selecteren en **kopiëren naar** de data Box NFS-gegevens opslag.


## <a name="prepare-azure-data-box-for-return"></a>Azure Data Box voorbereiden voor retour neren

Zodra alle gegevens van de virtuele machine, de gegevens van de virtuele-machine sjabloon en ISO-bestanden worden gekopieerd naar de Data Box NFS-Data Store, kan de Data Store worden losgekoppeld van uw vCenter.  Alle virtuele machines en virtuele-machine sjablonen moeten uit de inventaris worden verwijderd voordat de gegevens opslag kan worden losgekoppeld.

### <a name="remove-objects-from-inventory"></a>Objecten uit de inventaris verwijderen

1. Ga in uw on-premises vCenter-webinterface naar **opslag**.  Selecteer Data Box NFS-gegevens opslag en klik op **vm's**.

2. Zorg ervoor dat alle virtuele machines zijn uitgeschakeld.

    ![Virtuele machines uit de voor Raad verwijderen-uitgeschakeld](media/databox-migration-select-databox-vm.png)

3. Selecteer alle virtuele machines, klik er met de rechter muisknop op en selecteer **verwijderen uit inventarisatie**.

    ![Virtuele machines uit de inventaris verwijderen](media/databox-migration-remove-vm-from-inventory.png)

4. Selecteer **VM-sjablonen in mappen** van de bovenste knoppen en herhaal stap 3. 

### <a name="remove-azure-data-box-nfs-datastore-from-vcenter"></a>Azure Data Box NFS-gegevens opslag uit vCenter verwijderen

Data Box NFS-gegevens opslag moet worden losgekoppeld van VMware ESXi hosts voordat de retour wordt voor bereid.

1. Ga in uw on-premises vCenter-webinterface naar **opslag**.

2. Klik met de rechter muisknop op de Data Box NFS-gegevens opslag en selecteer **Data Store ontkoppelen**.

    ![Data Box gegevens opslag ontkoppelen](media/databox-migration-unmount-datastore.png)

3. Selecteer alle ESXi-hosts waar het gegevens archief is gekoppeld en klik op **OK**.

    ![Data Box Data Store ontkoppelen-hosts selecteren](media/databox-migration-unmount-datastore-select-hosts.png)

4. Controleer en accepteer eventuele waarschuwingen en klik op **OK**.

### <a name="prepare-data-box-for-return-and-return-the-data-box"></a>Data Box voorbereiden voor retour neren en de Data Box retour neren

Volg de stappen die worden beschreven in het artikel [retour Azure data box en controleer het uploaden van gegevens naar Azure](../databox/data-box-deploy-picked-up.md) om de data box te retour neren.  Controleer de status van het kopiëren van gegevens naar uw Azure Storage-account en zodra de status is voltooid, kunt u de gegevens in uw Azure Storage-account verifiëren.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution-by-cloudsimple"></a>Gegevens kopiëren van Azure Storage naar de Azure VMware-oplossing via CloudSimple

Gegevens die naar uw Azure Data Box zijn gekopieerd, zijn beschikbaar in uw Azure Storage-account wanneer de status van de bestelling van uw Data Box wordt weer gegeven als voltooid.  De gegevens kunnen nu worden gekopieerd naar uw Azure VMware-oplossing door CloudSimple.  Gegevens in het opslag account moeten worden gekopieerd met het NFS-protocol naar het vSAN-gegevens archief van uw Privécloud.  Kopieer eerst BLOB-gegevens naar een beheerde schijf op een virtuele Linux-machine in azure met behulp van **AzCopy**.  Zorg ervoor dat de beheerde schijf beschikbaar is via NFS-protocol en koppel de NFS-share als een gegevens opslag in uw Privécloud en kopieer de gegevens.  Met deze methode kunt u een snellere kopie van de gegevens naar uw Privécloud. 

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-export-as-nfs-share"></a>Gegevens naar uw Privécloud kopiëren met behulp van een virtuele Linux-machine en beheerde schijven en exporteren als NFS-share

1. Maak een [virtuele Linux-machine](../virtual-machines/linux/quick-create-portal.md) in Azure in dezelfde regio waarin uw opslag account is gemaakt en een Azure Virtual Network-verbinding heeft met uw privécloud.

2. Maak een beheerde schijf, die groter is dan de hoeveelheid BLOB-gegevens en [koppel deze aan uw virtuele Linux-machine](../virtual-machines/linux/attach-disk-portal.md).  Als de hoeveelheid BLOB-gegevens groter is dan de grootste beschik bare schijf, moeten de gegevens in meerdere stappen worden gekopieerd of meerdere beheerde schijven gebruiken.

3. Maak verbinding met de virtuele Linux-machine en koppel de beheerde schijf.

4. Installeer [AzCopy op uw virtuele Linux-machine](../storage/common/storage-use-azcopy-v10.md).

5. Down load de gegevens van uw Azure Blob-opslag op de beheerde schijf met behulp van AzCopy.  Opdracht syntaxis: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"`.  Vervang `<storage-account-name>` door de naam van uw Azure Storage-account en `<container-name>` met de container, die de gegevens bevat die zijn gekopieerd met Azure Data Box.

6. Installeer de NFS-server op uw virtuele Linux-machine.

    1. Op een Ubuntu/Debian-distributie: `sudo apt install nfs-kernel-server`.
    2. In een Enter prise Linux-distributie: `sudo yum install nfs-utils`.

7. Wijzig de machtiging van de map op uw beheerde schijf waar gegevens uit het Azure Blob Store zijn gekopieerd.  Wijzig de machtigingen voor alle mappen die u wilt exporteren als NFS-share.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. Wijs machtigingen voor IP-adressen van clients toe om toegang te krijgen tot de NFS-share door het `/etc/exports`-bestand te bewerken.

    ```bash
    sudo vi /etc/exports
    ```
    
    Typ de volgende regels in het bestand voor elk ESXi van uw Privécloud.  Als u shares voor meerdere mappen maakt, moet u alle mappen toevoegen.

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. Exporteer de NFS-shares met de opdracht `sudo exportfs -a`.

10. Start de NFS-kernelserver opnieuw met behulp van de opdracht `sudo systemctl restart nfs-kernel-server`.


### <a name="mount-linux-virtual-machine-nfs-share-as-a-datastore-on-private-cloud-vcenter-cluster-and-copy-data"></a>De NFS-share van de virtuele machine voor Linux koppelen als een gegevens opslag op een Privécloud-cluster en gegevens kopiëren

De NFS-share van uw virtuele Linux-machine moet worden gekoppeld als een gegevens opslag in uw particuliere cloud-vCenter-cluster voor het kopiëren van de gegevens.  Nadat de gegevens zijn gekoppeld, kunnen ze vanuit de NFS-Data Store naar de Privécloud vSAN Data Store worden gekopieerd.

1. Meld u aan bij de vCenter-Server van de Privécloud.

2. Klik met de rechter muisknop op het **Data Center**, selecteer **opslag** toevoegen klik op **nieuwe gegevens opslag** en klik op **volgende**

   ![Nieuwe gegevens opslag toevoegen](media/databox-migration-add-datastore.png)

3. Selecteer in stap 1 van de wizard Data Store toevoegen het type **NFS**.

   ![Nieuwe gegevens opslag toevoegen-type](media/databox-migration-add-datastore-type.png)

4. Selecteer in stap 2 **NFS 3** als NFS-versie en klik op **volgende**.

   ![Nieuwe Data Store-NFS-versie toevoegen](media/databox-migration-add-datastore-nfs-version.png)

5. Geef in stap 3 de naam voor de gegevens opslag, het pad en de server op.  U kunt het IP-adres van uw virtuele Linux-machine voor Server gebruiken.  Het mappad heeft de notatie `/<folder>/<subfolder>/`.

   ![Nieuwe Data Store-NFS-configuratie toevoegen](media/databox-migration-add-datastore-nfs-configuration.png)

6. Selecteer in stap 4 de ESXi-hosts waar u de gegevens opslag wilt koppelen en klik op **volgende**.  Selecteer alle hosts in een cluster om de migratie van virtuele machines te controleren.

   ![Nieuwe gegevens opslag toevoegen-hosts selecteren](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. Bekijk in stap 5 de samen vatting en klik op **volt ooien**

### <a name="add-virtual-machines-and-virtual-machine-templates-from-nfs-datastore-to-the-inventory"></a>Virtuele machines en virtuele-machine sjablonen van NFS-gegevens opslag toevoegen aan de inventaris

1. Navigeer vanuit uw persoonlijke Cloud vCenter-webinterface naar **opslag**.  Selecteer virtuele Linux-machine NFS-gegevens opslag en klik op **bestanden**.

    ![Bestanden selecteren in de NFS-gegevens opslag](media/databox-migration-datastore-select-files.png)

2. Selecteer een map die een virtuele machine of een virtuele-machine sjabloon bevat.  Selecteer in het detail venster `.vmx`-bestand voor een virtuele machine of `.vmtx`-bestand voor een virtuele-machine sjabloon.

3. Klik op **VM registreren** om de virtuele machine in uw privécloud te registreren.

    ![Virtuele machine registreren](media/databox-migration-datastore-register-vm.png)

4. Selecteer het Data Center, de map en het cluster of de resource groep waar u de virtuele machine wilt registreren.

4. Herhaal stap 3 en 4 voor alle virtuele machines en virtuele-machine sjablonen.

5. Ga naar de map met de ISO-bestanden.  Selecteer ISO-bestanden en **Kopieer deze naar** een map in uw vSAN-gegevens opslag.

De virtuele machines en virtuele-machine sjablonen zijn nu beschikbaar in uw Privécloud.  Deze virtuele machines moeten worden verplaatst van de NFS-gegevens opslag naar de vSAN-gegevens opslag voordat ze kunnen worden ingewisseld.  U kunt een Storage vMotion van de virtuele machines uitvoeren en vSAN-gegevens opslag selecteren als doel voor de virtuele machine.

De virtuele-machine sjablonen moeten worden gekloond van de NFS-gegevens opslag van uw virtuele Linux-machine naar uw vSAN-gegevens opslag.

### <a name="clean-up-of-your-linux-virtual-machine"></a>Uw virtuele Linux-machine opruimen

Zodra alle gegevens naar uw Privécloud zijn gekopieerd, kunt u de NFS-gegevens opslag uit de privécloud verwijderen.

1. Zorg ervoor dat alle virtuele machines en sjablonen zijn verplaatst en gekloond naar uw vSAN-gegevens opslag.

2. Verwijder alle virtuele-machine sjablonen uit de NFS-gegevens opslag uit de inventaris.

3. De gegevens opslag van de virtuele Linux-machine ontkoppelen van uw Privécloud.

4. Verwijder de virtuele machine en de beheerde schijf uit Azure.

5. Als u niet wilt dat de gegevens worden overgedragen met behulp van Azure Data Box in uw opslag account, verwijdert u het Azure-opslag account.  
    


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure data Box](../databox/data-box-overview.md)
* Meer informatie over de verschillende opties voor [het migreren van workloads naar een privécloud](migrate-workloads.md)
