---
title: Herstel naar een bepaald tijdstip voor blok-blobs (preview-versie)
titleSuffix: Azure Storage
description: Herstel naar een bepaald tijdstip voor blok-blobs biedt beveiliging tegen onbedoeld verwijderen of beschadiging door u in te scha kelen om een opslag account terug te zetten naar de vorige status op dat moment.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 66682e953e4e262604d1b0c07720ebaab5995364
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83195218"
---
# <a name="point-in-time-restore-for-block-blobs-preview"></a>Herstel naar een bepaald tijdstip voor blok-blobs (preview-versie)

Herstel naar een bepaald tijdstip biedt beveiliging tegen onbedoeld verwijderen of beschadiging door u in staat te stellen de blok-BLOB-gegevens terug te zetten naar een eerdere status. Herstel naar een bepaald tijdstip is handig in scenario's waarbij een gebruiker of toepassing per ongeluk gegevens verwijdert of een toepassings fout beschadigde gegevens verstuurt. Herstel naar een bepaald tijdstip biedt ook ondersteuning voor test scenario's waarbij een gegevensset naar een bekende status moet worden teruggedraaid voordat verdere tests worden uitgevoerd.

Zie herstel naar een bepaald tijdstip voor [blok-blobs (preview-versie inschakelen en beheren)](point-in-time-restore-manage.md)voor meer informatie over het inschakelen van een herstel punt voor een opslag account.

## <a name="how-point-in-time-restore-works"></a>Hoe herstel naar een bepaald tijdstip werkt

Als u herstel naar een bepaald tijdstip wilt inschakelen, maakt u een beheer beleid voor het opslag account en geeft u een Bewaar periode op. Tijdens de retentie periode kunt u blok-blobs van de huidige status herstellen naar een status op een eerder tijdstip.

Als u een herstel tijdstip voor een bepaald tijdstip wilt initiëren, roept u de bewerking [BLOB Ranges herstellen](/rest/api/storagerp/storageaccounts/restoreblobranges) aan en geeft u een herstel punt op in UTC-tijd. U kunt een lexicographical bereik van container-en BLOB-namen opgeven om te herstellen, of het bereik weglaten om alle containers in het opslag account te herstellen. De bewerking **BLOB-bereiken herstellen** retourneert een Restore-id waarmee de bewerking op unieke wijze wordt geïdentificeerd.

Azure Storage analyseert alle wijzigingen die zijn aangebracht in de opgegeven blobs tussen het aangevraagde herstel punt, opgegeven in UTC-tijd en het huidige moment. De herstel bewerking is atomisch, waardoor alle wijzigingen volledig worden hersteld of niet kan worden uitgevoerd. Als er blobs zijn die niet kunnen worden hersteld, mislukt de bewerking en worden lees-en schrijf bewerkingen naar de betrokken containers hervat.

Wanneer u een herstel bewerking aanvraagt, blokkeert Azure Storage gegevens bewerkingen op de blobs in het bereik dat wordt hersteld voor de duur van de bewerking. Lees-, schrijf-en verwijder bewerkingen worden geblokkeerd op de primaire locatie. Lees bewerkingen van de secundaire locatie kunnen door gaan tijdens de herstel bewerking als het opslag account geo-gerepliceerd is.

Er kan slechts één herstel bewerking tegelijk worden uitgevoerd op een opslag account. Een herstel bewerking kan niet worden geannuleerd zodra deze wordt uitgevoerd, maar er kan wel een tweede herstel bewerking worden uitgevoerd om de eerste bewerking ongedaan te maken.

Als u de status van een herstel tijdstip wilt controleren, roept u de bewerking **herstel status ophalen** aan met de herstel-id die wordt geretourneerd door de bewerking **BLOB-bereiken herstellen** .

Houd bij het herstellen de volgende beperkingen in acht:

- Een blok dat is geüpload met behulp van de [put-blok kering](/rest/api/storageservices/put-block) of het [put-blok van de URL](/rest/api/storageservices/put-block-from-url), maar niet is doorgevoerd via een put- [blok lijst](/rest/api/storageservices/put-block-list), maakt geen deel uit van een BLOB en wordt dus niet hersteld als onderdeel van een herstel bewerking.
- Een blob met een actieve lease kan niet worden hersteld. Als een blob met een actieve lease is opgenomen in het bereik van blobs dat moet worden hersteld, mislukt de herstel bewerking Atomic.
- Moment opnamen worden niet gemaakt of verwijderd als onderdeel van een herstel bewerking. Alleen de basis-BLOB wordt teruggezet naar de vorige status.
- Als een BLOB tussen de warme en koele lagen is verplaatst in de periode tussen het huidige moment en het herstel punt, wordt de BLOB teruggezet naar de vorige laag. Een blob die is verplaatst naar de archief laag, wordt echter niet hersteld.

> [!CAUTION]
> Herstel naar een bepaald tijdstip biedt alleen ondersteuning voor het herstellen van bewerkingen op blok-blobs. Bewerkingen op containers kunnen niet worden hersteld. Als u een container uit het opslag account verwijdert door de bewerking [Delete container](/rest/api/storageservices/delete-container) aan te roepen tijdens de preview-fase van het herstel punt, kan deze container niet worden hersteld met een herstel bewerking. In plaats van een container te verwijderen, kunt u tijdens de preview-versie de afzonderlijke blobs verwijderen als u deze wilt herstellen.

### <a name="prerequisites-for-point-in-time-restore"></a>Vereisten voor herstel naar een bepaald tijdstip

Herstel naar een bepaald tijdstip vereist dat de volgende Azure Storage-functies zijn ingeschakeld:

- [Voorlopig verwijderen](soft-delete-overview.md)
- [Wijzigings feed (preview-versie)](storage-blob-change-feed.md)
- [Versie beheer van BLOB (preview)](versioning-overview.md)

Schakel deze functies in voor het opslag account voordat u herstel naar een bepaald tijdstip inschakelt. Zorg ervoor dat u zich registreert voor de voor beelden van de wijzigings feed en BLOB-versie voordat u deze inschakelt.

### <a name="retention-period-for-point-in-time-restore"></a>Bewaar periode voor herstel naar een bepaald tijdstip

Wanneer u herstel naar een bepaald tijdstip voor een opslag account inschakelt, geeft u een Bewaar periode op. Blok-blobs in uw opslag account kunnen worden hersteld tijdens de Bewaar periode.

De Bewaar periode begint wanneer u herstel op tijdstippen inschakelt. Denk eraan dat u geen blobs kunt herstellen naar een status vóór het begin van de Bewaar periode. Als u bijvoorbeeld punt-in-time herstel hebt ingeschakeld op 1 mei met een Bewaar periode van 30 dagen, kunt u op 15 mei Maxi maal vijf tien dagen herstellen. Op 1 juni kunt u gegevens terugzetten van tussen de en dertig dagen.

De Bewaar periode voor herstel naar een bepaald tijdstip moet ten minste één dag kleiner zijn dan de retentie periode die is opgegeven voor de tijdelijke verwijdering. Als de Bewaar periode voor zacht verwijderen bijvoorbeeld is ingesteld op 7 dagen, mag de Bewaar periode voor het herstel punt van de tijd tussen 1 en 6 dagen liggen.

### <a name="permissions-for-point-in-time-restore"></a>Machtigingen voor herstel naar een bepaald tijdstip

Een client moet schrijf machtigingen hebben voor alle containers in het opslag account om een herstel bewerking te initiëren. Wijs de rol **Inzender voor opslag accounts** toe aan de beveiligingsprincipal op het niveau van het opslag account, de resource groep of het abonnement om machtigingen te verlenen voor het autoriseren van een herstel bewerking met Azure Active Directory (Azure AD).

## <a name="about-the-preview"></a>Over de preview-versie

Herstel naar een bepaald tijdstip wordt alleen ondersteund voor v2-opslag accounts voor algemeen gebruik. Alleen gegevens in de warme en coolbar-toegangs lagen kunnen worden hersteld met herstel naar een bepaald tijdstip.

In de volgende regio's wordt herstel naar een bepaald tijdstip in de preview-versie ondersteund:

- Canada - midden
- Canada - oost
- Frankrijk - centraal

Het voor beeld bevat de volgende beperkingen:

- Het herstellen van Premium-blok-blobs wordt niet ondersteund.
- Het herstellen van blobs in de opslaglaag wordt niet ondersteund. Als een BLOB in de warme laag bijvoorbeeld twee dagen geleden is verplaatst naar de archief laag en een herstel bewerking herstelt naar een bepaald punt drie dagen geleden, wordt de BLOB niet teruggezet naar de warme laag.
- Het herstellen van Azure Data Lake Storage Gen2 vlakke en hiërarchische naam ruimten wordt niet ondersteund.
- Het herstellen van opslag accounts met door de klant verschafte sleutels wordt niet ondersteund.

> [!IMPORTANT]
> De preview-versie van het herstel punt is bedoeld voor niet-productie gebruik. Service Level Agreements (Sla's) op het niveau van de productie zijn momenteel niet beschikbaar.

### <a name="register-for-the-preview"></a>Registreren voor de preview-versie

Als u zich voor de preview wilt registreren, voert u de volgende opdrachten uit Azure PowerShell:

```powershell
# Register for the point-in-time restore preview
Register-AzProviderFeature -FeatureName RestoreBlobRanges -ProviderNamespace Microsoft.Storage

# Register for change feed (preview)
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage

# Register for blob versioning (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="check-registration-status"></a>Registratie status controleren

Voer de volgende opdrachten uit om de status van uw registratie te controleren:

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName RestoreBlobRanges

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Changefeed
```

## <a name="pricing-and-billing"></a>Prijzen en facturering

Facturering voor tijdstippen herstel is afhankelijk van de hoeveelheid gegevens die wordt verwerkt om de herstel bewerking uit te voeren. De hoeveelheid verwerkte gegevens is gebaseerd op het aantal wijzigingen dat is opgetreden tussen het herstel punt en het huidige moment. Als er bijvoorbeeld een relatief constant tempo van wijziging is voor het blok keren van BLOB-gegevens in een opslag account, zou een herstel bewerking die terugkeert in de tijd 1 dag kosten 1/tiende van een herstel datum van 10 dagen in beslag nemen.

Als u de kosten van een herstel bewerking wilt schatten, bekijkt u het logboek voor wijzigings invoer om de hoeveelheid gegevens te schatten die tijdens de herstel periode is gewijzigd. Als de retentie periode voor de wijziging 30 dagen is en de grootte van de wijzigings feed 10 MB is, zal het herstellen naar een punt dat 10 dagen eerder duurt, ongeveer een derde van de vermelde prijs voor een LRS-account in die regio kosten. Als u een punt van 27 dagen eerder herstelt, kost dit ongeveer negen tienden van de weer gegeven prijs.

Zie de [prijzen voor blok-blobs](https://azure.microsoft.com/pricing/details/storage/blobs/)voor meer informatie over de prijzen voor herstel naar een bepaald tijdstip.

## <a name="ask-questions-or-provide-feedback"></a>Vragen stellen of feedback geven

Neem contact op met micro soft op om vragen te stellen over de preview-versie van het herstel punt in de tijd of om feedback te geven pitrdiscussion@microsoft.com .

## <a name="next-steps"></a>Volgende stappen

- [Herstel naar een bepaald tijdstip voor blok-blobs inschakelen en beheren (preview-versie)](point-in-time-restore-manage.md)
- [Ondersteuning voor feed wijzigen in Azure Blob Storage (preview-versie)](storage-blob-change-feed.md)
- [Voorlopig verwijderen voor blobs inschakelen](soft-delete-enable.md)
- [BLOB-versie beheer inschakelen en beheren](versioning-enable.md)
