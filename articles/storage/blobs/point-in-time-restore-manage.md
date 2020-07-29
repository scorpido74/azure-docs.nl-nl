---
title: Herstel naar een bepaald tijdstip voor blok-blobs inschakelen en beheren (preview-versie)
titleSuffix: Azure Storage
description: Meer informatie over het gebruik van punt-in-time Restore (preview) voor het herstellen van blok-blobs naar een status op een eerder tijdstip.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/11/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6948d4d786e918e5f3e32e6bdf2f7e23940f6815
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445437"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>Herstel naar een bepaald tijdstip voor blok-blobs inschakelen en beheren (preview-versie)

U kunt punt-in-time Restore (preview) gebruiken om blok-blobs op een eerder tijdstip terug te zetten naar hun status. In dit artikel wordt beschreven hoe u herstel naar een bepaald tijdstip kunt inschakelen voor een opslag account met Power shell. U ziet ook hoe u een herstel bewerking uitvoert met Power shell.

Zie herstel naar een bepaald [tijdstip voor blok-blobs (preview)](point-in-time-restore-overview.md)voor meer informatie en om te leren hoe u zich kunt registreren voor de preview-versie.

> [!CAUTION]
> Herstel naar een bepaald tijdstip biedt alleen ondersteuning voor het herstellen van bewerkingen op blok-blobs. Bewerkingen op containers kunnen niet worden hersteld. Als u een container uit het opslag account verwijdert door de bewerking [Delete container](/rest/api/storageservices/delete-container) aan te roepen tijdens de preview-fase van het herstel punt, kan deze container niet worden hersteld met een herstel bewerking. In plaats van een container te verwijderen, kunt u tijdens de preview-versie de afzonderlijke blobs verwijderen als u deze wilt herstellen.

> [!IMPORTANT]
> De preview-versie van het herstel punt is bedoeld voor niet-productie gebruik. Service Level Agreements (Sla's) op het niveau van de productie zijn momenteel niet beschikbaar.

## <a name="install-the-preview-module"></a>De preview-module installeren

Als u Azure Point-in-time Restore met Power shell wilt configureren, installeert u eerst de module AZ. Storage preview versie 1.14.1-Preview of hoger. Het gebruik van de nieuwste preview-versie wordt aanbevolen, maar herstel naar een bepaald tijdstip wordt ondersteund in versie 1.14.1-Preview en hoger. Verwijder alle andere versies van de module AZ. storage.

Met de volgende opdracht installeert AZ. Storage [2.0.1-Preview-](https://www.powershellgallery.com/packages/Az.Storage/2.0.1-preview) module:

```powershell
Install-Module -Name Az.Storage -RequiredVersion 2.0.1-preview -AllowPrerelease
```

Voor de bovenstaande opdracht is versie 2.2.4.1 of hoger van PowerShellGet vereist om te kunnen installeren. U kunt als volgt bepalen welke versie momenteel is geladen:
```powershell
Get-Module PowerShellGet
```
Zie [Install Azure PowerShell with PowerShellGet](/powershell/azure/install-az-ps)(Engelstalig) voor meer informatie over het installeren van Azure PowerShell.

## <a name="enable-and-configure-point-in-time-restore"></a>Herstel naar een bepaald tijdstip inschakelen en configureren

Voordat u herstel naar een bepaald tijdstip inschakelt en configureert, moet u de vereisten voor het opslag account inschakelen: voorlopig verwijderen, feed wijzigen en BLOB-versie beheer. Raadpleeg de volgende artikelen voor meer informatie over het inschakelen van elk van deze functies:

- [Voorlopig verwijderen voor blobs inschakelen](soft-delete-enable.md)
- [De wijzigings feed in-en uitschakelen](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [BLOB-versie beheer inschakelen en beheren](versioning-enable.md)

Als u Azure Point-in-time Restore met Power shell wilt configureren, roept u de opdracht Enable-AzStorageBlobRestorePolicy aan. In het volgende voor beeld wordt zacht verwijderen ingeschakeld en wordt de Bewaar periode voor de tijdelijke verwijdering ingesteld, wordt wijzigings invoer ingeschakeld en wordt vervolgens herstel naar een bepaald tijdstip ingeschakeld. Voordat u het voor beeld uitvoert, moet u de Azure Portal-of Azure Resource Manager-sjabloon gebruiken om ook BLOB-versie beheer in te scha kelen.

Wanneer u het voor beeld uitvoert, moet u de waarden tussen punt haken vervangen door uw eigen waarden:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable soft delete with a retention of 6 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 6

# Enable change feed.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true

# Enable point-in-time restore with a retention period of 5 days.
# The retention period for point-in-time restore must be at least one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 5

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

## <a name="perform-a-restore-operation"></a>Een herstel bewerking uitvoeren

Als u een herstel bewerking wilt starten, roept u de opdracht **Restore-AzStorageBlobRange** aan, waarbij u het herstel punt opgeeft als een UTC- **datum/tijd** -waarde. U kunt lexicographical-bereiken van blobs opgeven die u wilt herstellen, of een bereik weglaten om alle blobs in alle containers in het opslag account te herstellen. Er worden Maxi maal 10 lexicographical-bereiken ondersteund per herstel bewerking. Pagina-blobs en toevoeg-blobs worden niet opgenomen in de herstel bewerking. Het kan enkele minuten duren voordat de herstel bewerking is voltooid.

Houd bij het opgeven van een aantal blobs dat moet worden hersteld de volgende regels in acht:

- Het container patroon dat is opgegeven voor het begin bereik en het eind bereik moet mini maal drie tekens bevatten. De slash (/) die wordt gebruikt om een container naam te scheiden van een BLOB-naam, telt niet mee aan dit minimum.
- Er kan Maxi maal 10 bereiken worden opgegeven per herstel bewerking.
- Joker tekens worden niet ondersteund. Ze worden beschouwd als standaard tekens.
- U kunt blobs in de `$root` containers herstellen `$web` door ze expliciet op te geven in een bereik dat is door gegeven aan een herstel bewerking. De `$root` `$web` containers en worden alleen hersteld als ze expliciet zijn opgegeven. Andere systeem containers kunnen niet worden hersteld.

> [!IMPORTANT]
> Wanneer u een herstel bewerking uitvoert, blokkeert Azure Storage gegevens bewerkingen op de blobs in de bereiken die worden teruggezet voor de duur van de bewerking. Lees-, schrijf-en verwijder bewerkingen worden geblokkeerd op de primaire locatie. Daarom kunnen bewerkingen, zoals het weer geven van containers in het Azure Portal, niet worden uitgevoerd zoals verwacht tijdens het terugzetten.
>
> Lees bewerkingen van de secundaire locatie kunnen door gaan tijdens de herstel bewerking als het opslag account geo-gerepliceerd is.

### <a name="restore-all-containers-in-the-account"></a>Alle containers in het account herstellen

Als u alle containers en blobs in het opslag account wilt herstellen, roept u de opdracht **Restore-AzStorageBlobRange** aan, waarbij u de `-BlobRestoreRange` para meter weglaat. In het volgende voor beeld worden de containers in het opslag account teruggezet naar de status 12 uur vóór het huidige tijdstip:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
```

### <a name="restore-a-single-range-of-block-blobs"></a>Eén bereik van blok-blobs herstellen

Als u een bereik van blobs wilt herstellen, roept u de opdracht **Restore-AzStorageBlobRange** aan en geeft u een lexicographical-bereik van container-en BLOB-namen op voor de `-BlobRestoreRange` para meter. Het begin van het bereik is inclusief en het einde van het bereik is exclusief.

Als u bijvoorbeeld de blobs in één container met de naam *sample-container*wilt herstellen, kunt u een bereik opgeven dat begint met voor *beeld-container* en eindigt met *sample-container1*. Er zijn geen vereisten voor de containers met de naam in het begin-en eind bereik. Omdat het einde van het bereik exclusief is, zelfs als het opslag account een container met de naam *sample-container1*bevat, wordt alleen de container met de naam *sample-container* teruggezet:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container -EndRange sample-container1
```

Als u een subset van blobs in een container wilt herstellen, gebruikt u een slash (/) om de container naam te scheiden van het BLOB-patroon. Het volgende bereik selecteert bijvoorbeeld blobs in één container waarvan de naam begint met de letters *d* t/m *f*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d -EndRange sample-container/g
```

Geef vervolgens het bereik op voor de opdracht **Restore-AzStorageBlobRange** . Geef het herstel punt op door een UTC- **datum** waarde voor de para meter op te geven `-TimeToRestore` . In het volgende voor beeld worden de blobs in het opgegeven bereik tot de status 3 dagen vóór het huidige moment hersteld:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

### <a name="restore-multiple-ranges-of-block-blobs"></a>Meerdere bereiken van blok-blobs herstellen

Als u meerdere bereiken van blok-blobs wilt herstellen, geeft u een matrix met bereiken op voor de `-BlobRestoreRange` para meter. Er worden Maxi maal tien bereiken ondersteund per herstel bewerking. In het volgende voor beeld wordt twee bereiken opgegeven voor het herstellen van de volledige inhoud van *container1* en *container4*:

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

### <a name="restore-block-blobs-asynchronously"></a>Blok-blobs asynchroon herstellen

Als u een herstel bewerking asynchroon wilt uitvoeren, voegt u de `-AsJob` para meter toe aan de aanroep **Restore-AzStorageBlobRange** en slaat u het resultaat van de aanroep op in een variabele. De opdracht **Restore-AzStorageBlobRange** retourneert een object van het type **AzureLongRunningJob**. U kunt de eigenschap **State** van dit object controleren om te bepalen of de herstel bewerking is voltooid. De waarde van de eigenschap **State** kan worden **uitgevoerd** of **voltooid**.

In het volgende voor beeld ziet u hoe u een herstel bewerking asynchroon aanroept:

```powershell
$job = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-5) `
    -AsJob

# Check the state of the job.
$job.State
```

Als u wilt wachten op het volt ooien van de herstel bewerking nadat deze is uitgevoerd, roept u de opdracht [wait-job](/powershell/module/microsoft.powershell.core/wait-job) aan, zoals wordt weer gegeven in het volgende voor beeld:

```powershell
$job | Wait-Job
```

## <a name="known-issues"></a>Bekende problemen
- Voor een subset van herstel bewerkingen waarbij toevoeg-blobs aanwezig zijn, mislukt het herstellen. Voer op dit moment geen herstel bewerkingen uit als toevoeg-blobs aanwezig zijn in het account.

## <a name="next-steps"></a>Volgende stappen

- [Herstel naar een bepaald tijdstip voor blok-blobs (preview-versie)](point-in-time-restore-overview.md)
- [Voorlopig verwijderen](soft-delete-overview.md)
- [Wijzigings feed (preview-versie)](storage-blob-change-feed.md)
- [Versie beheer van BLOB (preview)](versioning-overview.md)
