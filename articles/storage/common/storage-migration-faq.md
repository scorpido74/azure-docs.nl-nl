---
title: Veelgestelde vragen over azure-opslagmigratie | Microsoft Documenten
description: Antwoorden op veelgestelde vragen over het migreren van Azure Storage
services: storage
author: genlin
manager: dcscontentpm
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.subservice: common
ms.openlocfilehash: 1445d74e3050ffd6da7c45037df552f4bee9acf5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77116668"
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Veelgestelde vragen over de Azure Storage-migratie

In dit artikel worden veelgestelde vragen over Azure Storage-migratie beantwoord.

## <a name="copy-upload-or-download"></a>Kopiëren, uploaden of downloaden

**Hoe maak ik een script om bestanden van de ene container naar de andere te kopiëren?**

Als u bestanden tussen containers wilt kopiëren, u AzCopy gebruiken. Zie het volgende voorbeeld:

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

AzCopy gebruikt de [API Blob kopiëren](https://docs.microsoft.com/rest/api/storageservices/copy-blob) om elk bestand in de container te kopiëren.  

U elke virtuele machine of lokale machine gebruiken die toegang heeft tot internet om AzCopy uit te voeren. U ook een Azure Batch-schema gebruiken om dit automatisch te doen, maar het is ingewikkelder.  

Het automatiseringsscript is ontworpen voor azure resource manager-implementatie in plaats van opslaginhoudsmanipulatie. Zie [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)voor meer informatie.

**Zijn er kosten verbonden aan het kopiëren van gegevens tussen twee bestandsshares op hetzelfde opslagaccount binnen dezelfde regio?**

Nee. Er zijn geen kosten verbonden aan dit proces.

**Hoe kan ik 1-2 TB aan gegevens downloaden van de Azure-portal?**

Gebruik AzCopy om de gegevens te downloaden. Zie [Gegevens overbrengen met AzCopy op Windows](storage-use-azcopy.md) en Gegevens overbrengen met [AzCopy op Linux](storage-use-azcopy-linux.md)voor meer informatie.

**Hoe kan ik een VHD downloaden naar een lokale machine, anders dan met behulp van de downloadoptie in de portal?**

U [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gebruiken om een VHD te downloaden.

**Hoe download ik gegevens naar een Linux-gebaseerde computer vanaf een Azure-opslagaccount of upload ik gegevens van een Linux-machine?**

U de Azure CLI gebruiken.

- Download één blob:

      azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

- Upload één blob:

      azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**Hoe migreer ik Blobs van het ene opslagaccount naar het andere?**

 U dit doen met behulp van ons [Blob-migratiescript](../scripts/storage-common-transfer-between-storage-accounts.md).
 
## <a name="migration-or-backup"></a>Migratie of back-up

**Hoe verplaats ik gegevens van de ene opslagcontainer naar de andere?**

Volg deze stappen:

1.  Maak de container (map) in de doelblob.

2.  Gebruik [AzCopy](https://azure.microsoft.com/blog/azcopy-5-1-release/) om de inhoud van de oorspronkelijke blobcontainer naar een andere blobcontainer te kopiëren.

**Hoe maak ik een PowerShell-script om gegevens van het ene Azure-bestandsaandeel naar het andere te verplaatsen in Azure Storage?**

Gebruik AzCopy om de gegevens van het ene Azure-bestandsaandeel naar het andere te verplaatsen in Azure Storage. Zie [Gegevens overbrengen met AzCopy op Windows](storage-use-azcopy.md) en Gegevens overbrengen met [AzCopy op Linux](storage-use-azcopy-linux.md)voor meer informatie.

**Hoe upload ik grote CSV-bestanden naar Azure Storage?**

Gebruik AzCopy om grote CSV-bestanden te uploaden naar Azure Storage. Zie [Gegevens overbrengen met AzCopy op Windows](storage-use-azcopy.md) en Gegevens overbrengen met [AzCopy op Linux](storage-use-azcopy-linux.md)voor meer informatie.

**Ik moet de logboeken elke dag verplaatsen van station D naar mijn Azure-opslagaccount. Hoe automatiseer ik dit?**

U AzCopy gebruiken en een taak maken in Taakplanner. Upload bestanden naar een Azure-opslagaccount met behulp van een AzCopy-batchscript. Zie [Opstarttaken configureren en uitvoeren voor een cloudservice voor](../../cloud-services/cloud-services-startup-tasks.md)meer informatie.

**Hoe verplaats ik mijn opslagaccount tussen abonnementen?**

Gebruik AzCopy om uw opslagaccount tussen abonnementen te verplaatsen. Zie [Gegevens overbrengen met AzCopy op Windows](storage-use-azcopy.md) en Gegevens overbrengen met [AzCopy op Linux](storage-use-azcopy-linux.md)voor meer informatie.

**Hoe kan ik ongeveer 10 TB aan gegevens verplaatsen naar opslag in een andere regio?**

Gebruik AzCopy om de gegevens te verplaatsen. Zie [Gegevens overbrengen met AzCopy op Windows](storage-use-azcopy.md) en Gegevens overbrengen met [AzCopy op Linux](storage-use-azcopy-linux.md)voor meer informatie.

**Hoe kan ik gegevens van on-premises naar Azure Storage kopiëren?**

Gebruik AzCopy om de gegevens te kopiëren. Zie [Gegevens overbrengen met AzCopy op Windows](storage-use-azcopy.md) en Gegevens overbrengen met [AzCopy op Linux](storage-use-azcopy-linux.md)voor meer informatie.

**Hoe kan ik gegevens verplaatsen van on-premises naar Azure Files?**

Gebruik AzCopy om gegevens te verplaatsen. Zie [Gegevens overbrengen met AzCopy op Windows](storage-use-azcopy.md) en Gegevens overbrengen met [AzCopy op Linux](storage-use-azcopy-linux.md)voor meer informatie.

**Hoe verplaats ik beheerde schijven naar een ander opslagaccount?**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Volg deze stappen:

1.  Stop de virtuele machine waarop de beheerde schijf is aangesloten.

2.  Kopieer de vhd-beheerde schijf van het ene gebied naar het andere door het volgende Azure PowerShell-script uit te voeren:

    ```
    Connect-AzAccount

    Select-AzSubscription -SubscriptionId <ID>

    $sas = Grant-AzDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  Maak een beheerde schijf met behulp van het VHD-bestand in een andere regio waarnaar u de VHD hebt gekopieerd. Voer hiervoor het volgende Azure PowerShell-script uit:  

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

**Hoe verplaats of download ik gegevens van een opslagaccount?**

Gebruik AzCopy om de gegevens te downloaden. Zie [Gegevens overbrengen met AzCopy op Windows](storage-use-azcopy.md) en Gegevens overbrengen met [AzCopy op Linux](storage-use-azcopy-linux.md)voor meer informatie.

**Hoe ga ik van een premium opslagaccount naar een standaardopslagaccount?**

Volg deze stappen:

1.  Maak een standaard opslagaccount aan. (Of gebruik een bestaand standaardopslagaccount in uw abonnement.)

2.  Download AzCopy. Voer een van de volgende AzCopy-opdrachten uit.

    Ga als bedoeld als u hele schijven in het opslagaccount kopieert:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S

    Als u slechts één schijf wilt kopiëren, geeft u de naam van de schijf op in **Patroon:**

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

Het kan enkele uren duren voordat de bewerking is voltooid.

Als u ervoor wilt zorgen dat de overdracht is voltooid, onderzoekt u de opslagaccountcontainer voor bestemming in de Azure-portal. Nadat de schijven zijn gekopieerd naar het standaard opslagaccount, u ze als bestaande schijf aan de virtuele machine koppelen. Zie [Een beheerde gegevensschijf koppelen aan een virtuele Windows-machine in de Azure-portal](../../virtual-machines/windows/attach-managed-disk-portal.md)voor meer informatie.  

**Hoe ga ik van een klassiek opslagaccount naar een Azure Resource Manager-opslagaccount?**

U de cmdlet **Move-AzureStorageAccount** gebruiken. Deze cmdlet heeft meerdere stappen (valideren, voorbereiden, commit). U de verhuizing valideren voordat u deze maakt.

Als u virtuele machines hebt, moet u aanvullende stappen nemen voordat u de opslagaccountgegevens migreert. Zie [IaaS-resources migreren van klassiek naar Azure Resource Manager voor](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md)meer informatie met Azure PowerShell.

**Hoe maak ik een back-up van mijn volledige opslagaccount naar een ander opslagaccount?**

Er is geen optie om een back-up van een volledig opslagaccount rechtstreeks te maken. Maar u de container in dat opslagaccount handmatig verplaatsen naar een ander account met AzCopy of Storage Explorer. In de volgende stappen wordt uitgelegd hoe u AzCopy gebruikt om de container te verplaatsen:  

1.  Installeer [AzCopy](storage-use-azcopy.md) het azcopy-opdrachtregelgereedschap. Met deze tool u het VHD-bestand verplaatsen tussen opslagaccounts.

2.  Nadat u AzCopy op Windows hebt geïnstalleerd met behulp van het installatieprogramma, opent u een opdrachtpromptvenster en bladert u vervolgens naar de installatiemap AzCopy op uw computer. AzCopy is standaard geïnstalleerd op **%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy** of **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**.

3.  Voer de volgende opdracht uit om de container te verplaatsen. U moet de tekst vervangen door de werkelijke waarden.   

            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

    - `/Source`: Geef de URI voor het bronopslagaccount (tot aan de container).  
    - `/Dest`: Geef de URI voor het doelopslagaccount (tot aan de container).  
    - `/SourceKey`: Geef de primaire sleutel voor het bronopslagaccount op. U deze sleutel kopiëren vanuit de Azure-portal door het opslagaccount te selecteren.  
    - `/DestKey`: Geef de primaire sleutel voor het doelopslagaccount op. U deze sleutel van de portal kopiëren door het opslagaccount te selecteren.

Nadat u deze opdracht hebt uitgevoerd, worden de containerbestanden verplaatst naar het doelopslagaccount.

> [!NOTE]
> De AzCopy CLI werkt niet samen met de **patroonschakelaar** wanneer u van de ene Azure-blob naar de andere kopieert.
>
> U de opdracht AzCopy rechtstreeks kopiëren en bewerken en controleren of **patroon** overeenkomt met de bron. Zorg er ook voor dat **/S** wildcards van kracht zijn. Zie [AzCopy-parameters](storage-use-azcopy.md)voor meer informatie.

**Hoe maak ik een back-up van Azure-bestandsopslag?**

Er is geen back-upoplossing. Azure Files ondersteunt echter ook asynchrone kopie. U dus bestanden kopiëren:

- Van een aandeel naar een ander aandeel in een opslagaccount of naar een ander opslagaccount.

- Van een aandeel tot een blobcontainer in een opslagaccount of naar een ander opslagaccount.

Zie [Gegevens overbrengen met AzCopy op Windows](storage-use-azcopy.md)voor meer informatie.
## <a name="configuration"></a>Configuratie

**Hoe wijzig ik de secundaire locatie in de regio Europa voor een opslagaccount?**

Wanneer u een opslagaccount maakt, selecteert u het primaire gebied voor het account. De selectie van het secundaire gebied is gebaseerd op de primaire regio en kan niet worden gewijzigd. Zie [Georedundant storage (GRS): Cross-regional replicatie voor Azure Storage voor](storage-redundancy.md)meer informatie.

**Waar kan ik meer informatie krijgen over Azure Storage Service Encryption (SSE)?**  

Zie de volgende artikelen:

-  [Veiligheidsgids voor Azure Storage](../blobs/security-recommendations.md)

-  [Azure Storage Service-versleuteling voor gegevens in rust](storage-service-encryption.md)

**Hoe versleutel ik gegevens in een opslagaccount?**

Nadat u versleuteling in een opslagaccount hebt ingeschakeld, worden de bestaande gegevens niet versleuteld. Als u de bestaande gegevens wilt versleutelen, moet u deze opnieuw uploaden naar het opslagaccount.

Gebruik AzCopy om de gegevens naar een ander opslagaccount te kopiëren en de gegevens vervolgens terug te verplaatsen. U ook [versleuteling in rust](storage-service-encryption.md)gebruiken.

**Zijn er vereisten voor het wijzigen van de replicatie van een opslagaccount van georedundante opslag naar lokaal redundante opslag?**

Nee.

**Hoe kan ik converteren naar Azure Premium Storage voor een bestandsshare?**

Premium Storage is niet toegestaan op een Azure-bestandsshare.

**Hoe kan ik upgraden van een standaard opslagaccount naar een premium opslagaccount? Hoe kan ik downgraden van een premium-opslagaccount naar een standaardopslagaccount?**

U moet het doelopslagaccount maken, gegevens van het bronaccount kopiëren naar het doelaccount en vervolgens het bronaccount verwijderen. U een tool zoals AzCopy gebruiken om de gegevens te kopiëren.

Als u virtuele machines hebt, moet u aanvullende stappen nemen voordat u de opslagaccountgegevens migreert. Zie [Migreren naar Azure Premium Storage (onbeheerde schijven) voor](storage-migration-to-premium-storage.md)meer informatie.

**Hoe kan ik andere mensen toegang geven tot mijn opslagbronnen?**

Ga als u ervoor om andere mensen toegang te geven tot de opslagbronnen:

-   Gebruik een SAS-token (Shared Access Signature) om toegang te geven tot een bron.

-   Geef een gebruiker de primaire of secundaire sleutel voor het opslagaccount. Zie [Toegangssleutels voor opslagaccount beheren](storage-account-keys-manage.md)voor meer informatie .

-   Wijzig het toegangsbeleid om anonieme toegang mogelijk te maken. Zie [Anonieme gebruikersmachtigingen verlenen aan containers en blobs voor](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs)meer informatie.

**Waar is AzCopy geïnstalleerd?**

-   Als u AzCopy binnenkrijgt via de opdrachtregel Microsoft Azure Storage, typt u **AzCopy**. De opdrachtregel is naast AzCopy geïnstalleerd.

-   Als u de 32-bits versie hebt geïnstalleerd, bevindt deze zich hier: **%ProgramFiles(x86)%\\Microsoft SDKs\\Azure\\AzCopy**.

-   Als u de 64-bits versie hebt geïnstalleerd, bevindt deze zich hier: **%ProgramFiles%\\Microsoft\\SDKs Azure\\AzCopy**.

**Hoe gebruik ik een aangepast HTTPS-domein met mijn opslagaccount? Hoe laat ik bijvoorbeeld 'https:\//mystorageaccountname.blob.core.windows.net/images/image.gif' verschijnen\/als 'https: /www.contoso.com/images/image.gif'?**

SSL wordt momenteel niet ondersteund op opslagaccounts met aangepaste domeinen.
Maar u niet-HTTPS aangepaste domeinen gebruiken. Zie [Een aangepaste domeinnaam configureren voor het eindpunt van uw Blob-opslag voor](../blobs/storage-custom-domain-name.md)meer informatie.

## <a name="access-to-storage"></a>Toegang tot opslag

**Hoe breng ik een containermap in kaart op een virtuele machine?**

Gebruik een Azure-bestandsshare.

**Hoe krijg ik toegang tot redundante opslag van Azure Files?**

Georedundante opslag met toegang tot leestoegang is vereist om toegang te krijgen tot redundante opslag. Azure Files ondersteunt echter alleen lokaal redundante opslag en standaard georedundante opslag die alleen-lezen toegang toestaat.

**Hoe krijg ik voor een gerepliceerd opslagaccount (zoals zoneredundante opslag, georedundante opslag of georedundante opslag) toegang tot gegevens die in de secundaire regio zijn opgeslagen?**

-   Als u zoneredundante opslag of georedundante opslag gebruikt, hebt u geen toegang tot gegevens uit de secundaire regio, tenzij u een failover naar dat gebied initieert. Zie [Failover voor noodherstel en opslagaccount (preview) in Azure Storage](storage-disaster-recovery-guidance.md)voor meer informatie over het failoverproces.

-   Als u georedundante opslag met leestoegang gebruikt, hebt u op elk gewenst moment toegang tot gegevens uit de secundaire regio. Gebruik een van de volgende methoden:  

    - **AzCopy**: **Secundair** toevoegen aan de naam van het opslagaccount in de URL om toegang te krijgen tot het secundaire eindpunt. Bijvoorbeeld:  

      https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd

    - **SAS-token:** gebruik een SAS-token om toegang te krijgen tot gegevens vanaf het eindpunt. Zie [Handtekeningen voor gedeelde toegang gebruiken](storage-sas-overview.md)voor meer informatie .

**Hoe gebruik ik FTP om toegang te krijgen tot gegevens die zich in een opslagaccount begeven?**

Er is geen manier om rechtstreeks toegang te krijgen tot een opslagaccount met FTP. U echter wel een virtuele Azure-machine instellen en vervolgens een FTP-server op de virtuele machine installeren. U de FTP-serveropslagbestanden op een Azure-bestanden-share of op een gegevensschijf laten maken die beschikbaar is voor de virtuele machine.

Als u alleen gegevens wilt downloaden zonder Storage Explorer of een soortgelijke toepassing te hoeven gebruiken, u mogelijk een SAS-token gebruiken. Zie [Handtekeningen voor gedeelde toegang gebruiken](storage-sas-overview.md)voor meer informatie .

## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Neem contact op met ondersteuning.

Als u nog steeds hulp nodig hebt, neemt u [contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.
