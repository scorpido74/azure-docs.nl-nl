---
title: Persoonlijke gegevens verwijderen en exporteren uit Azure DevTest Labs
description: Meer informatie over het verwijderen en exporteren van persoonsgegevens uit de Azure DevLast Labs-service ter ondersteuning van uw verplichtingen uit hoofde van de Algemene Verordening Gegevensbescherming (AVG).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: c87e2fb534480bbf9bbe625d67782e5a11eda18c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169685"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Persoonlijke gegevens exporteren of verwijderen uit Azure DevTest Labs
In dit artikel vindt u stappen voor het verwijderen en exporteren van persoonlijke gegevens uit de Azure DevTest Labs-service. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>Welke persoonsgegevens verzamelt DevTest Labs?
DevTest Labs verzamelt twee belangrijke stukken van persoonlijke gegevens van de gebruiker. Ze zijn: e-mailadres van de gebruiker en gebruikersnaam. Deze informatie is essentieel voor de service om in-service functies te bieden aan labbeheerders en labgebruikers.

### <a name="user-email-address"></a>E-mailadres van gebruiker
DevTest Labs gebruikt het e-mailadres van de gebruiker om automatische afsluitende e-mailmeldingen naar labgebruikers te verzenden. De e-mail waarschuwt gebruikers van hun machine wordt stilgelegd. De gebruikers kunnen de shutdown uitstellen of overslaan als ze dat willen. U configureert het e-mailadres op labniveau of op VM-niveau.

**E-mail instellen in het lab:**

![E-mail instellen op labniveau](./media/personal-data-delete-export/lab-user-email.png)

**E-mail instellen bij de VM:**

![E-mail instellen op VM-niveau](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>Gebruikersnaam
DevTest Labs gebruikt de gebruikersobject-id om kostentrends en kosten per resource aan labbeheerders per maand weer te geven. Het stelt hen in staat om kosten bij te houden en drempels voor hun Lab te beheren. 

**Geschatte kostentrend voor de huidige kalendermaand:**
![trend van geschatte kosten voor de huidige kalendermaand](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Geschatte kosten per maand per resource:**
![geschatte kosten per maand per resource](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>Waarom hebben we deze persoonsgegevens nodig?
De DevTest Labs-service gebruikt de persoonsgegevens voor operationele doeleinden. Deze gegevens zijn essentieel voor de service om belangrijke functies te leveren. Als u een bewaarbeleid instelt op het e-mailadres van de gebruiker, ontvangen labgebruikers niet tijdig automatische verwijderingsmeldingen van e-mailmeldingen nadat hun e-mailadres uit ons systeem is verwijderd. Op dezelfde manier kan de labbeheerder kostentrends en kosten per resource voor machines in hun labs niet bekijken als de id's van het gebruikersobject worden verwijderd op basis van een bewaarbeleid. Daarom moeten deze gegevens worden bewaard zolang de bron van de gebruiker actief is in het lab.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>Hoe kan ik het systeem hebben om mijn persoonlijke gegevens te vergeten?
Als u als labgebruiker deze persoonsgegevens wilt laten verwijderen, u dit doen door de bijbehorende bron in het lab te verwijderen. De DevTest Labs-service anonimiseert de verwijderde persoonlijke gegevens 30 dagen nadat deze door de gebruiker zijn verwijderd.

Als u bijvoorbeeld uw virtuele machine verwijdert of uw e-mailadres hebt verwijderd, anonimiseert de service DevTest Labs deze gegevens 30 dagen nadat de bron is verwijderd. Het bewaarbeleid van 30 dagen na verwijdering is om ervoor te zorgen dat we een nauwkeurige kostenprognose per maand bieden aan de labbeheerder.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>Hoe kan ik een export aanvragen op mijn persoonsgegevens?
U persoonlijke gegevens en laboratoriumgebruik exporteren met behulp van de Azure-portal of PowerShell. De gegevens worden geëxporteerd als twee verschillende CSV-bestanden:

- **disks.csv** - bevat informatie over de schijven die worden gebruikt door de verschillende VM's
- **virtualmachines.csv** - bevat informatie over de VM's in het lab.

### <a name="azure-portal"></a>Azure Portal
Als labgebruiker u een export aanvragen op de persoonsgegevens die de DevTest Labs-service opslaat. Als u een exportaanvraag wilt aanvragen, navigeert u naar de optie **Persoonsgegevens** op de **overzichtspagina** van uw lab. Selecteer de knop **Export aanvragen** start bij het maken van een downloadbaar excelbestand in het opslagaccount van uw Lab-beheerder. U vervolgens contact opnemen met uw labbeheerder om deze gegevens te bekijken.

1. Selecteer **Persoonlijke gegevens** in het linkermenu. 

    ![Pagina persoonsgegevens](./media/personal-data-delete-export/personal-data-page.png)
2. Selecteer de **resourcegroep** die het lab bevat.

    ![Resourcegroep selecteren](./media/personal-data-delete-export/select-resource-group.png)
3. Selecteer het **opslagaccount** in de resourcegroep.
4. Selecteer **Blobs**op de pagina **Opslagaccount** .

    ![Blobs-tegel selecteren](./media/personal-data-delete-export/select-blobs-tile.png)
5. Selecteer de container met de naam **labresourceusage** in de lijst met containers.

    ![Blobcontainer selecteren](./media/personal-data-delete-export/select-blob-container.png)
6. Selecteer de **map** die naar uw lab is vernoemd. In deze map vindt u **csv-bestanden** voor **schijven** en **virtuele machines** in uw lab. U deze csv-bestanden downloaden, de inhoud filteren voor de labgebruiker die toegang aanvraagt en deze met hen delen.

    ![CSV-bestand downloaden](./media/personal-data-delete-export/download-csv-file.png)

### <a name="azure-powershell"></a>Azure PowerShell

```powershell
Param (
    [Parameter (Mandatory=$true, HelpMessage="The storage account name where to store usage data")]
    [string] $storageAccountName,

    [Parameter (Mandatory=$true, HelpMessage="The storage account key")]
    [string] $storageKey,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab name to get usage data from")]
    [string] $labName,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab subscription")]
    [string] $labSubscription
    )

#Login
Login-AzureRmAccount

# Set the subscription for the lab
Get-AzureRmSubscription -SubscriptionId $labSubscription  | Select-AzureRmSubscription

# DTL will create this container in the storage when invoking the action, cannot be changed currently
$containerName = "labresourceusage"

# Get the storage context
$Ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageKey 
$SasToken = New-AzureStorageAccountSASToken -Service Blob, File -ResourceType Container, Service, Object -Permission rwdlacup -Protocol HttpsOnly -Context $Ctx

# Generate the storage blob uri
$blobUri = $Ctx.BlobEndPoint + $SasToken

# blobStorageAbsoluteSasUri and usageStartDate are required
$actionParameters = @{
    'blobStorageAbsoluteSasUri' = $blobUri    
}

$startdate = (Get-Date).AddDays(-7)

$actionParameters.Add('usageStartDate', $startdate.Date.ToString())

# Get the lab resource group
$resourceGroupName = (Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $labName}).ResourceGroupName
    
# Create the lab resource id
$resourceId = "/subscriptions/" + $labSubscription + "/resourceGroups/" + $resourceGroupName + "/providers/Microsoft.DevTestLab/labs/" + $labName + "/"

# !!!!!!! this is the new resource action to get the usage data.
$result = Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
 
# Finish up cleanly
if ($result.Status -eq "Succeeded") {
    Write-Output "Telemetry successfully downloaded for " $labName
    return 0
}
else
{
    Write-Output "Failed to download lab: " + $labName
    Write-Error $result.toString()
    return -1
}
```

De belangrijkste componenten in het bovenstaande monster zijn:

- De opdracht Aanroepen-AzureRmResourceAction.
   
    ```
    Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
    ```
- Twee actieparameters
    - **blobStorageAbsoluteSasUri** - Het opslagaccount URI met het SAS-token (Shared Access Signature). In het PowerShell-script kan deze waarde worden doorgegeven in plaats van de opslagsleutel.
    - **gebruikStartDatum** - De begindatum om gegevens op te halen, waarbij de einddatum de huidige datum is waarop de actie wordt uitgevoerd. De granulariteit is op dagniveau, dus zelfs als u tijdsinformatie toevoegt, wordt deze genegeerd.

### <a name="exported-data---a-closer-look"></a>Geëxporteerde gegevens - een kijkje
Laten we nu eens een kijkje nemen op de geëxporteerde gegevens. Zoals eerder vermeld, zodra de gegevens met succes worden geëxporteerd, zullen er twee CSV-bestanden zijn. 

De **virtualmachines.csv** bevat de volgende gegevenskolommen:

| Kolomnaam | Beschrijving |
| ----------- | ----------- | 
| SubscriptionId | De abonnements-id waarin het lab bestaat. |
| Labuid (Labuid) | Unieke GUID-id voor het lab. |
| LabName (LabName) | Naam van het lab. |
| LabResourceId (LabResourceId) | Volledig gekwalificeerde lab resource ID. |
| ResourceGroupName | Naam van de resourcegroep die de VM bevat | 
| ResourceId | Volledig gekwalificeerde resource-ID voor de VM. |
| ResourceUId | GUID voor de VM |
| Name | Virtuele machine naam. |
| CreatedTime | De datumwaarop de VM is gemaakt. |
| VerwijderdDatum | De datumwaarop de VM is verwijderd. Als het leeg is, is verwijdering nog niet opgetreden. |
| ResourceOwner | Eigenaar van de VM. Als de waarde leeg is, is het een claimbare VM of gemaakt door een serviceprincipal. |
| PricingTier | Prijscategorie van de VM |
| Resourcestatus | Beschikbaarheidsstatus van de VM. Actief, als deze nog steeds bestaat of Inactief, als de VM is verwijderd. |
| ComputeResourceId | Volledig gekwalificeerde compute resource-id voor virtuele machines. |
| Claimable | Ingesteld op true als de VM een claimbare VM is | 
| EnvironmentId | De omgevingsresource-id waarbinnen de virtuele machine is gemaakt. Deze is leeg wanneer de vm niet is gemaakt als onderdeel van een omgevingsbron. |
| Vervaldatum | Vervaldatum voor de VM. Het is ingesteld op leeg, als een vervaldatum niet is ingesteld.
| GalleryImageReferenceVersie |  Versie van de VM-basisafbeelding. |
| GalleryImageReferenceAanbieding | Aanbieding van de VM-basisafbeelding. |
| Publisher van GalerijImageReference | Uitgever van de VM-basisafbeelding. |
| GalerijafbeeldingReferenceSku | Sku van de VM-basisafbeelding |
| GalleryImageReferenceOsType | Besturingssysteem type van de VM-basisafbeelding |
| CustomImageId (CustomImageId) | Volledig gekwalificeerde ID van de aangepaste VM-basisafbeelding. |

De gegevenskolommen in **disks.csv** worden hieronder weergegeven:

| Kolomnaam | Beschrijving | 
| ----------- | ----------- | 
| SubscriptionId | ID van het abonnement dat het lab bevat |
| Labuid (Labuid) | GUID voor het lab |
| LabName (LabName) | Naam van het lab | 
| LabResourceId (LabResourceId) | Volledig gekwalificeerde resource-ID voor het lab | 
| ResourceGroupName | Naam van de resourcegroep die het lab bevat | 
| ResourceId | Volledig gekwalificeerde resource-ID voor de VM. |
| ResourceUId | GUID voor de VM |
 |Name | De naam van de bijgevoegde schijf |
| CreatedTime |De datum en het tijdstip waarop de gegevensschijf is gemaakt. |
| VerwijderdDatum | De datum en het tijdstip waarop de gegevensschijf is verwijderd. |
| Resourcestatus | Status van de resource. Actief als de resource bestaat. Inactief, wanneer verwijderd. |
| DiskBlobName | Blobnaam voor de gegevensschijf. |
| DiskSizeGB | De grootte van de gegevensschijf. |
| DiskType | Type van de gegevensschijf. 0 voor Standaard, 1 voor Premium. |
| LeasedByvmId | Resource-id van de VM waaraan de gegevensschijf is gekoppeld. |


> [!NOTE]
> Als u te maken hebt met meerdere labs en wilt algemene informatie te krijgen, de twee belangrijkste kolommen zijn de **LabUID** en de **ResourceUId**, die de unieke id's tussen abonnementen.

De geëxporteerde gegevens kunnen worden gemanipuleerd en gevisualiseerd met behulp van tools, zoals SQL Server, Power BI, enz. Deze functie is vooral handig wanneer u het gebruik van uw lab wilt melden aan uw managementteam dat mogelijk niet hetzelfde Azure-abonnement gebruikt als u.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen: 

- [Beleid instellen voor een lab](devtest-lab-get-started-with-lab-policies.md)
- [Veelgestelde vragen](devtest-lab-faq.md)
