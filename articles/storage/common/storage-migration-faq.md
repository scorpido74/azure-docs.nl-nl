---
title: Veelgestelde vragen over Azure Storage migratie | Microsoft Docs
description: Antwoorden op veelgestelde vragen over het migreren van Azure Storage
services: storage
author: genlin
manager: dcscontentpm
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.subservice: common
ms.openlocfilehash: 1d8275d11b845df43238dce82beabe89d6464799
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84944692"
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Veelgestelde vragen over de Azure Storage-migratie

In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Storage migratie.

## <a name="copy-upload-or-download"></a>Kopiëren, uploaden of downloaden

**Hoe kan ik een script maken om bestanden van de ene container naar de andere te kopiëren?**

U kunt AzCopy gebruiken om bestanden tussen containers te kopiëren. Zie het volgende voorbeeld:

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

AzCopy maakt gebruik van de [copy BLOB API](https://docs.microsoft.com/rest/api/storageservices/copy-blob) om elk bestand in de container te kopiëren.  

U kunt elke virtuele machine of lokale computer met Internet toegang gebruiken om AzCopy uit te voeren. U kunt dit ook een Azure Batch schema gebruiken om dit automatisch te doen, maar het is gecompliceerder.  

Het Automation-script is ontworpen voor de implementatie van Azure Resource Manager in plaats van het bewerken van opslag inhoud. Zie [resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)voor meer informatie.

**Worden er kosten in rekening gebracht voor het kopiëren van gegevens tussen twee bestands shares op hetzelfde opslag account in dezelfde regio?**

Nee. Er worden geen kosten in rekening gebracht voor dit proces.

**Hoe kan ik 1-2 TB aan gegevens downloaden van de Azure Portal?**

Gebruik AzCopy om de gegevens te downloaden. Zie [gegevens overdragen met AzCopy in Windows](storage-use-azcopy.md) en [gegevens overdragen met AzCopy in Linux](storage-use-azcopy-linux.md)voor meer informatie.

**Hoe kan ik een VHD downloaden naar een lokale computer, met uitzonde ring van de download optie in de portal?**

U kunt [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gebruiken om een VHD te downloaden.

**Hoe kan ik gegevens downloaden naar een Linux-computer vanuit een Azure-opslag account of gegevens uploaden vanaf een Linux-computer?**

U kunt de Azure CLI gebruiken.

- Een enkele BLOB downloaden:

      azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

- Eén BLOB uploaden:

      azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**Hoe kan ik migreert u blobs van het ene opslag account naar het andere?**

 U kunt dit doen met behulp van het [BLOB-migratie script](../scripts/storage-common-transfer-between-storage-accounts.md).
 
## <a name="migration-or-backup"></a>Migratie of back-up

**Hoe kan ik gegevens verplaatsen van de ene opslag container naar de andere?**

Volg deze stappen:

1.  Maak de container (map) in de doel-blob.

2.  Gebruik [AzCopy](https://azure.microsoft.com/blog/azcopy-5-1-release/) om de inhoud van de oorspronkelijke BLOB-container naar een andere blob-container te kopiëren.

**Hoe kan ik een Power shell-script maken om gegevens van een Azure-bestands share te verplaatsen naar een andere in Azure Storage?**

Gebruik AzCopy om de gegevens van een Azure-bestands share te verplaatsen naar een andere in Azure Storage. Zie [gegevens overdragen met AzCopy in Windows](storage-use-azcopy.md) en [gegevens overdragen met AzCopy in Linux](storage-use-azcopy-linux.md)voor meer informatie.

**Hoe kan ik u grote CSV-bestanden uploaden naar Azure Storage?**

Gebruik AzCopy om grote CSV-bestanden te uploaden naar Azure Storage. Zie [gegevens overdragen met AzCopy in Windows](storage-use-azcopy.md) en [gegevens overdragen met AzCopy in Linux](storage-use-azcopy-linux.md)voor meer informatie.

**Ik moet elke dag de logboeken van station D naar mijn Azure-opslag account verplaatsen. Hoe kan ik dit automatisch?**

U kunt AzCopy gebruiken en een taak maken in taak planner. Bestanden uploaden naar een Azure-opslag account met behulp van een AzCopy-batch script. Zie [opstart taken voor een Cloud service configureren en uitvoeren](../../cloud-services/cloud-services-startup-tasks.md)voor meer informatie.

**Hoe kan ik mijn opslag account verplaatsen tussen abonnementen?**

Gebruik AzCopy om uw opslag account te verplaatsen tussen abonnementen. Zie [gegevens overdragen met AzCopy in Windows](storage-use-azcopy.md) en [gegevens overdragen met AzCopy in Linux](storage-use-azcopy-linux.md)voor meer informatie.

**Hoe kan ik ongeveer 10 TB aan gegevens verplaatsen naar de opslag in een andere regio?**

Gebruik AzCopy om de gegevens te verplaatsen. Zie [gegevens overdragen met AzCopy in Windows](storage-use-azcopy.md) en [gegevens overdragen met AzCopy in Linux](storage-use-azcopy-linux.md)voor meer informatie.

**Hoe kan ik gegevens van on-premises naar Azure Storage kopiëren?**

Gebruik AzCopy om de gegevens te kopiëren. Zie [gegevens overdragen met AzCopy in Windows](storage-use-azcopy.md) en [gegevens overdragen met AzCopy in Linux](storage-use-azcopy-linux.md)voor meer informatie.

**Hoe kan ik gegevens van on-premises verplaatsen naar Azure Files?**

Gebruik AzCopy om gegevens te verplaatsen. Zie [gegevens overdragen met AzCopy in Windows](storage-use-azcopy.md) en [gegevens overdragen met AzCopy in Linux](storage-use-azcopy-linux.md)voor meer informatie.

**Hoe kan ik beheerde schijven naar een ander opslag account verplaatsen?**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Volg deze stappen:

1.  Stop de virtuele machine waaraan de beheerde schijf is gekoppeld.

2.  Kopieer de VHD van de beheerde schijf van het ene naar het andere gebied door het volgende Azure PowerShell script uit te voeren:

    ```
    Connect-AzAccount

    Select-AzSubscription -SubscriptionId <ID>

    $sas = Grant-AzDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  Maak een beheerde schijf met behulp van het VHD-bestand in een andere regio waarnaar u de VHD hebt gekopieerd. U doet dit door het volgende Azure PowerShell script uit te voeren:  

    ```
    $resourceGroupName = 'MDDemo'

    $diskName = 'contoso\_os\_disk1'

    $vhdUri = 'https://contoso.storageaccou.com.vhd

    $storageId = '/subscriptions/<ID>/resourceGroups/<RG name>/providers/Microsoft.Storage/storageAccounts/contosostorageaccount1'

    $location = 'westus'

    $storageType = 'StandardLRS'

    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Import -SourceUri $vhdUri -StorageAccountId $storageId -DiskSizeGB 128

    $osDisk = New-AzDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
    ```

Zie [CreateVmFromManagedOsDisk.ps1](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1)voor meer informatie over het implementeren van een virtuele machine vanaf een beheerde schijf.

**Hoe kan ik gegevens van een opslag account verplaatsen of downloaden?**

Gebruik AzCopy om de gegevens te downloaden. Zie [gegevens overdragen met AzCopy in Windows](storage-use-azcopy.md) en [gegevens overdragen met AzCopy in Linux](storage-use-azcopy-linux.md)voor meer informatie.

**Hoe kan ik van een Premium Storage-account naar een Standard-opslag account gaan?**

Volg deze stappen:

1.  Maak een standaard-opslag account. (Of gebruik een bestaand standaard opslag account in uw abonnement.)

2.  Down load AzCopy. Voer een van de volgende AzCopy-opdrachten uit.

    De hele schijven in het opslag account kopiëren:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S

    Als u slechts één schijf wilt kopiëren, geeft u de naam van de schijf op in **patroon**:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

Het kan enkele uren duren voordat de bewerking is voltooid.

Als u ervoor wilt zorgen dat de overdracht is voltooid, controleert u de container van het bestemmings opslag account in de Azure Portal. Nadat de schijven zijn gekopieerd naar het standaard opslag account, kunt u ze als een bestaande schijf aan de virtuele machine koppelen. Zie [een beheerde gegevens schijf koppelen aan een virtuele Windows-machine in de Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md)voor meer informatie.  

**Hoe kan ik van een klassiek opslag account naar een Azure Resource Manager Storage-account gaan?**

U kunt de cmdlet **Move-AzureStorageAccount** gebruiken. Deze cmdlet heeft meerdere stappen (valideren, voorbereiden, door voeren). U kunt de verplaatsing valideren voordat u deze maakt.

Als u virtuele machines hebt, moet u extra stappen uitvoeren voordat u de gegevens van het opslag account migreert. Zie [IaaS-resources van klassiek naar Azure Resource Manager migreren met behulp van Azure PowerShell](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md)voor meer informatie.

**Hoe kan ik back-ups maken van mijn hele opslag account naar een ander opslag account?**

Er is geen optie om rechtstreeks een back-up te maken van een volledig opslag account. Maar u kunt de container in dat opslag account hand matig naar een ander account verplaatsen met behulp van AzCopy of Storage Explorer. De volgende stappen laten zien hoe u AzCopy kunt gebruiken om de container te verplaatsen:  

1.  Installeer het [AzCopy](storage-use-azcopy.md) -opdracht regel programma. Met dit hulp programma kunt u het VHD-bestand verplaatsen tussen opslag accounts.

2.  Nadat u AzCopy op Windows hebt geïnstalleerd met behulp van het installatie programma, opent u een opdracht prompt venster en bladert u naar de installatiemap van AzCopy op de computer. AzCopy wordt standaard geïnstalleerd op **% Program Files (x86)% \ micro soft SDKs\Azure\AzCopy** of **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**.

3.  Voer de volgende opdracht uit om de container te verplaatsen. U moet de tekst vervangen door de werkelijke waarden.   

            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

    - `/Source`: Geef de URI op voor het bron-of-opslag account (tot aan de container).  
    - `/Dest`: Geef de URI voor het doel-opslag account op (Maxi maal de container).  
    - `/SourceKey`: Geef de primaire sleutel voor het bron-opslag account op. U kunt deze sleutel uit de Azure Portal kopiëren door het opslag account te selecteren.  
    - `/DestKey`: Geef de primaire sleutel voor het doel-opslag account op. U kunt deze sleutel kopiëren vanuit de portal door het opslag account te selecteren.

Nadat u deze opdracht hebt uitgevoerd, worden de container bestanden verplaatst naar het doel-opslag account.

> [!NOTE]
> De AzCopy CLI werkt niet samen met de **patroon** schakelaar wanneer u van de ene Azure-Blob naar een andere kopieert.
>
> U kunt de AzCopy-opdracht rechtstreeks kopiëren en bewerken, en kruis controleren om ervoor te zorgen dat het **patroon** overeenkomt met de bron. Zorg er ook voor dat **/s** joker tekens van kracht zijn. Zie [AzCopy para meters](storage-use-azcopy.md)voor meer informatie.

**Hoe kan ik back-up maken van Azure File Storage?**

Er is geen back-upoplossing. Azure Files ondersteunt echter ook asynchrone kopieën. Zo kunt u bestanden kopiëren:

- Van een share naar een andere share in een opslag account of een ander opslag account.

- Van een share naar een BLOB-container in een opslag account of een ander opslag account.

Zie [gegevens overdragen met AzCopy in Windows](storage-use-azcopy.md)voor meer informatie.

## <a name="configuration"></a>Configuratie

**Hoe kan ik de secundaire locatie wijzigen in de regio Europa van een opslag account?**

Wanneer u een opslag account maakt, selecteert u de primaire regio voor het account. De selectie van de secundaire regio is gebaseerd op de primaire regio en kan niet worden gewijzigd. Zie [geo-redundante opslag (GRS): cross-Regional Replication for Azure Storage (Engelstalig)](storage-redundancy.md)voor meer informatie.

**Waar kan ik meer informatie krijgen over Azure Storage versleuteling?**  

Zie de volgende artikelen:

- [Azure Storage-versleuteling voor inactieve gegevens](storage-service-encryption.md)
- [Door de klant beheerde sleutels gebruiken met Azure Key Vault voor het beheren van Azure Storage versleuteling](encryption-customer-managed-keys.md)
- [Geef een versleutelings sleutel op voor een aanvraag voor Blob-opslag (preview-versie)](encryption-customer-provided-keys.md)

**Gegevens in een opslag account Hoe kan ik versleutelen?**

Nadat u versleuteling in een opslag account hebt ingeschakeld, worden de bestaande gegevens niet versleuteld. Als u de bestaande gegevens wilt versleutelen, moet u deze opnieuw uploaden naar het opslag account.

Gebruik AzCopy om de gegevens te kopiëren naar een ander opslag account en vervolgens de gegevens terug te verplaatsen. U kunt ook [versleuteling gebruiken bij rest](storage-service-encryption.md).

**Zijn er vereisten voor het wijzigen van de replicatie van een opslag account van geografisch redundante opslag naar lokaal redundante opslag?**

Nee.

**Hoe kan ik converteren naar Azure Premium Storage voor een bestands share?**

Premium Storage is niet toegestaan op een Azure-bestands share.

**Hoe kan ik upgrade van een standaard-opslag account naar een Premium Storage-account? Hoe kan ik downgrade van een Premium Storage-account naar een Standard-opslag account?**

U moet het doel-opslag account maken, gegevens van het bron account naar het doel account kopiëren en het bron account vervolgens verwijderen. U kunt een hulp programma zoals AzCopy gebruiken om de gegevens te kopiëren.

Als u virtuele machines hebt, moet u extra stappen uitvoeren voordat u de gegevens van het opslag account migreert. Zie [migreren naar Azure Premium Storage (niet-beheerde schijven)](storage-migration-to-premium-storage.md)voor meer informatie.

**Hoe kan ik anderen toegang geven tot mijn opslag resources?**

Andere personen toegang geven tot de opslag resources:

- Gebruik een SAS-token (Shared Access Signature) om toegang te bieden tot een resource.

- Geef een gebruiker op met de primaire of secundaire sleutel voor het opslag account. Zie [Toegangssleutels voor een opslagaccount beheren](storage-account-keys-manage.md) voor meer informatie.

- Wijzig het toegangs beleid om anonieme toegang toe te staan. Zie [anonieme gebruikers machtigingen verlenen aan containers en blobs](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs)voor meer informatie.

**Waar wordt AzCopy geïnstalleerd?**

- Als u toegang hebt tot AzCopy vanaf de opdracht regel van Microsoft Azure Storage, typt u **AzCopy**. De opdracht regel wordt naast AzCopy geïnstalleerd.

- Als u de 32-bits versie hebt geïnstalleerd, bevindt deze zich hier: **% Program Files (x86)% \\ micro soft sdk's \\ Azure \\ AzCopy**.

- Als u de 64-bits versie hebt geïnstalleerd, bevindt deze zich hier: **% Program Files% \\ micro soft sdk's \\ Azure \\ AzCopy**.

**Hoe kan ik een aangepast HTTPS-domein met mijn opslag account gebruiken? Hoe kan ik bijvoorbeeld ' https: \/ /mystorageaccountname.blob.core.windows.net/images/image.gif ' weer geven als ' https: \/ /www.contoso.com/images/image.gif '?**

TLS/SSL wordt momenteel niet ondersteund voor opslag accounts met aangepaste domeinen.
U kunt echter geen aangepaste niet-HTTPS-domeinen gebruiken. Zie [een aangepaste domein naam configureren voor het eind punt van de Blob-opslag](../blobs/storage-custom-domain-name.md)voor meer informatie.

## <a name="access-to-storage"></a>Toegang tot opslag

**Hoe kan ik een container-map op een virtuele machine toewijzen?**

Een Azure-bestands share gebruiken.

**Hoe kan ik toegang Azure Files redundante opslag?**

Geografisch redundante opslag met lees toegang is vereist voor toegang tot redundante opslag. Azure Files ondersteunt echter alleen lokaal redundante opslag en standaard geografisch redundante opslag waarvoor alleen-lezen toegang is toegestaan.

**Hoe kan ik toegang krijgen tot gegevens die zijn opgeslagen in de secundaire regio voor een gerepliceerd opslag account (zoals zone-redundante opslag, geografisch redundante opslag of geografisch redundante opslag met lees toegang)?**

- Als u gebruikmaakt van zone-redundante opslag of geografisch redundante opslag, hebt u geen toegang tot gegevens uit de secundaire regio, tenzij u een failover naar die regio initieert. Zie [nood herstel en failover van het opslag account](storage-disaster-recovery-guidance.md)voor meer informatie over het failoverproces.

- Als u geografisch redundante opslag met lees toegang gebruikt, kunt u op elk gewenst moment toegang krijgen tot gegevens uit de secundaire regio. Hanteer één van de volgende methoden:  

  - **AzCopy**: toevoeg **-secundair** aan de naam van het opslag account in de URL voor toegang tot het secundaire eind punt. Bijvoorbeeld:  

      `https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd`

  - **SAS-token**: gebruik een SAS-token om toegang te krijgen tot gegevens van het eind punt. Zie [using Shared Access signatures](storage-sas-overview.md)(Engelstalig) voor meer informatie.

**Hoe kan ik FTP gebruiken om toegang te krijgen tot gegevens in een opslag account?**

Er is geen manier om rechtstreeks toegang te krijgen tot een opslag account via FTP. U kunt echter een virtuele machine van Azure instellen en vervolgens een FTP-server op de virtuele machine installeren. U kunt de FTP-server bestanden opslaan op een Azure Files share of op een gegevens schijf die beschikbaar is voor de virtuele machine.

Als u alleen gegevens wilt downloaden zonder gebruik te hoeven maken van Storage Explorer of een soort gelijke toepassing, kunt u mogelijk een SAS-token gebruiken. Zie [using Shared Access signatures](storage-sas-overview.md)(Engelstalig) voor meer informatie.

## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Contact opnemen met ondersteuning

Als u nog steeds hulp nodig hebt, neemt u [contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.
