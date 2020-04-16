---
title: Toegangslagen voor hot, cool en archief voor blobs - Azure Storage
description: De toegangslagen voor Azure-opslagaccounts voor hot, cool en archiveren.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/23/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: c803d489b70cda6910865f6096d21c2021c4ae3a
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393709"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Azure Blob-opslag: dynamische en statische toegangslagen, en archieftoegangslaag

Azure-opslag biedt verschillende toegangslagen, waarmee u blob-objectgegevens op de meest kosteneffectieve manier opslaan. De beschikbare toegangsniveaus omvatten:

- **Hot** - Geoptimaliseerd voor het opslaan van gegevens die vaak worden geopend.
- **Cool** - Geoptimaliseerd voor het opslaan van gegevens die zelden worden geopend en gedurende ten minste 30 dagen worden opgeslagen.
- **Archief** - Geoptimaliseerd voor het opslaan van gegevens die zelden worden geopend en opgeslagen voor ten minste 180 dagen met flexibele latentie vereisten (op de volgorde van uren).

De volgende overwegingen zijn van toepassing op de verschillende toegangsniveaus:

- Alleen de hete en koele toegangsniveaus kunnen op accountniveau worden ingesteld. De niveau voor toegang tot het archief is niet beschikbaar op accountniveau.
- Hot,cool en archiveren kunnen tijdens het uploaden of na het uploaden op blobniveau worden ingesteld.
- Gegevens in de koele toegangslaag kunnen een iets lagere beschikbaarheid verdragen, maar vereisen nog steeds een hoge duurzaamheid, latentie en doorvoerkenmerken die vergelijkbaar zijn met hete gegevens. Voor coole gegevens zijn een iets lagere overeenkomst op serviceniveau (SLA) en hogere toegangskosten in vergelijking met hot data aanvaardbare afwegingen voor lagere opslagkosten.
- Archiefopslag slaat gegevens offline op en biedt de laagste opslagkosten, maar ook de hoogste kosten voor gegevensrehydrateren en toegang.

Gegevens die in de cloud worden opgeslagen, groeien exponentieel. Voor een effectief beheer van de kosten voor uw groeiende opslagbehoeften is het vanwege kostenoptimalisatie een goed idee om de gegevens te ordenen op basis van kenmerken als toegangsfrequentie en geplande bewaarperiode. Gegevens die in de cloud zijn opgeslagen, kunnen verschillen op basis van de manier waarop deze gedurende de levensduur worden gegenereerd, verwerkt en geopend. Sommige gegevens worden tijdens hun hele levensduur actief geopend en gewijzigd. Andere gegevens worden in het begin van hun levensduur regelmatig geopend, terwijl dit naarmate de tijd verstrijkt, aanzienlijk minder vaak gebeurt. Sommige gegevens blijven inactief in de cloud en worden zelden of nooit geopend nadat ze zijn opgeslagen.

Elk van deze scenario's voor gegevenstoegang profiteert van een andere toegangslaag die is geoptimaliseerd voor een bepaald toegangspatroon. Met hot,cool en archieftoegangslagen verhelpt Azure Blob-opslag deze behoefte aan gedifferentieerde toegangslagen met afzonderlijke prijsmodellen.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Storage-accounts die ondersteuning bieden voor opslaglagen

Objectopslaggegevens die lagen tussen hot, cool en archive wordt alleen ondersteund in Blob-opslag- en GPv2-accounts (General Purpose). GPv1-accounts (General Purpose v1) ondersteunen geen gelaagdheid. Klanten kunnen hun bestaande GPv1- of Blob-opslagaccounts eenvoudig converteren naar GPv2-accounts via de Azure-portal. GPv2 biedt nieuwe prijzen en functies voor blobs, bestanden en wachtrijen. Sommige functies en prijsverlagingen worden alleen aangeboden in GPv2-accounts. Evalueer het gebruik van GPv2-accounts na een uitgebreide beoordeling van de prijzen. Sommige workloads kunnen duurder zijn op GPv2 dan GPv1. Zie [Overzicht van Azure-opslagaccount](../common/storage-account-overview.md) voor meer informatie.

Blob-opslag- en GPv2-accounts geven het kenmerk **Access Tier** bloot op accountniveau. Met dit kenmerk u de standaardtoegangslaag opgeven voor een blob die deze niet expliciet heeft ingesteld op objectniveau. Voor objecten met de laag die op objectniveau is ingesteld, is de accountlaag niet van toepassing. De archieflaag kan alleen op objectniveau worden toegepast. U op elk gewenst moment schakelen tussen deze toegangsniveaus.

## <a name="hot-access-tier"></a>Hot Storage-toegangslaag

De hot access-laag heeft hogere opslagkosten dan koele en archieflagen, maar de laagste toegangskosten. Voorbeelden van gebruiksscenario's voor de hot access-laag zijn:

- Gegevens die actief worden gebruikt of die naar verwachting vaak worden geopend (van en naar geschreven).
- Gegevens die zijn gefaseerd voor verwerking en uiteindelijke migratie naar de koele toegangslaag.

## <a name="cool-access-tier"></a>Cool Storage-toegangslaag

De koele toegangslaag heeft lagere opslagkosten en hogere toegangskosten in vergelijking met hot storage. Deze laag is bedoeld voor gegevens die ten minste dertig dagen in de Cold Storage verblijven. Voorbeelden van gebruiksscenario's voor de koele toegangslaag zijn:

- Gegevenssets waarvan voor de korte termijn een back-up is gemaakt en die na een noodgeval zijn hersteld.
- Oudere media-inhoud die niet meer regelmatig wordt bekeken, maar onmiddellijk beschikbaar moet zijn wanneer deze wordt geopend.
- Grote gegevenssets die voordelig moeten worden opgeslagen, terwijl er meer gegevens worden verzameld voor toekomstige verwerking. (*Bijvoorbeeld*: langetermijnopslag van wetenschappelijke gegevens, onbewerkte telemetriegegevens van een productiefaciliteit)

## <a name="archive-access-tier"></a>Archive-toegangslaag

De categorie voor toegang tot het archief heeft de laagste opslagkosten. Maar het heeft hogere kosten voor het ophalen van gegevens in vergelijking met de hete en koele lagen. Het kan enkele uren duren voordat gegevens in de archieflaag zijn opgehaald. Gegevens moeten ten minste 180 dagen in de archieflaag blijven of worden vervroegd verwijderd.

Terwijl een blob zich in archiefopslag bevindt, zijn de blobgegevens offline en kunnen ze niet worden gelezen, overschreven of gewijzigd. Als u een blob in het archief wilt lezen of downloaden, moet u deze eerst opnieuw uitdrogen naar een onlinelaag. U geen momentopnamen maken van een blob in archiefopslag. De blobmetagegevens blijven echter online en beschikbaar, zodat u de blob en de eigenschappen ervan weergeven. Voor blobs in het archief zijn de enige geldige bewerkingen GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier, CopyBlob en DeleteBlob. Zie [Blobgegevens uit de archieflaag opnieuw hydrateren](storage-blob-rehydration.md) voor meer informatie.

Voorbeelden van gebruiksscenario's voor de categorie archieftoegang zijn:

- Langetermijnback-up, secundaire back-up en gegevenssets voor archivering
- Oorspronkelijke (onbewerkte) gegevens die moeten worden bewaard, zelfs nadat deze zijn verwerkt tot hun uiteindelijke, bruikbare vorm.
- Compliance- en archiveringsgegevens die gedurende lange tijd moeten worden opgeslagen en bijna nooit worden geopend.

## <a name="account-level-tiering"></a>Tiering op accountniveau

Blobs in alle drie de toegangslagen kunnen naast elkaar bestaan binnen hetzelfde account. Elke blob die geen expliciet toegewezen laag heeft, leidt de laag af van de instelling voor accounttoegangsniveaus. Als de toegangslaag afkomstig is van het account, ziet u de eigenschap **Access Tier Inferred** blob ingesteld op 'true' en komt de eigenschap **Access Tier** overeen met de accountlaag. In de Azure-portal wordt de _inferred eigenschap van_ de toegangslaag weergegeven met de blobtoegangslaag als **Hot (afgeleid)** of **Cool (afgeleid).**

Het wijzigen van de accounttoegangslaag is van toepassing op alle inafgeleide objecten _met toegangslagen_ die zijn opgeslagen in het account en die geen expliciete laagset hebben. Als u de accountlaag inschakelt van hot naar cool, worden er kosten in rekening gebracht voor schrijfbewerkingen (per 10.000) voor alle blobs zonder een ingestelde laag in alleen GPv2-accounts. Er zijn geen kosten verbonden aan deze wijziging in Blob-opslagaccounts. Er worden kosten in rekening gebracht voor zowel leesbewerkingen (per 10.000) als voor het ophalen van gegevens (per GB) als u schakelt van koel naar heet in Blob-opslag- of GPv2-accounts.

## <a name="blob-level-tiering"></a>Laaginstelling op blobniveau

Met blob-level tiering u gegevens uploaden naar de toegangslaag van uw keuze met behulp van de [bewerkingen Blob-](/rest/api/storageservices/put-blob) of [Putbloklijst](/rest/api/storageservices/put-block-list) plaatsen en de laag van uw gegevens op objectniveau wijzigen met behulp van de functie [Blob Tier](/rest/api/storageservices/set-blob-tier) instellen of [Levenscyclusbeheer.](#blob-lifecycle-management) U gegevens uploaden naar de vereiste toegangslaag en vervolgens eenvoudig de blobtoegangslaag wijzigen tussen de hot-, cool- of archieflagen als gebruikspatronen veranderen, zonder dat gegevens tussen accounts hoeven te worden verplaatst. Alle aanvragen voor laagwijzigingen worden onmiddellijk uitgevoerd en laagwijzigingen tussen hot en cool zijn onmiddellijk. Het hydrateren van een blob uit het archief kan echter enkele uren duren.

Het tijdstip waarop de laatste wijziging aan de blob-laag heeft plaatsgevonden, wordt weergegeven via de blob-eigenschap **Access Tier Change Time**. Bij het overschrijven van een blob in de hot- of cool-laag, neemt de nieuw gemaakte blob de laag over van de blob die is overschreven, tenzij de nieuwe blobtoegangslaag expliciet is ingesteld op creatie. Als een blob zich in de archieflaag bevindt, kan deze niet worden overschreven, dus het uploaden van dezelfde blob is in dit scenario niet toegestaan. 

> [!NOTE]
> Archiefopslag en laaginstelling op blobniveau ondersteunen alleen blok-blobs. U momenteel ook de laag van een blokblob met momentopnamen niet wijzigen.

### <a name="blob-lifecycle-management"></a>Blob-levenscyclusbeheer

Blob Storage lifecycle management biedt een rijk, op regels gebaseerd beleid dat u gebruiken om uw gegevens over te zetten naar de beste toegangslaag en om gegevens aan het einde van de levenscyclus te verlopen. Zie [De levenscyclus van Azure Blob-opslag beheren](storage-lifecycle-management-concepts.md) voor meer informatie.  

> [!NOTE]
> Gegevens die zijn opgeslagen in een account voor opslag van een blokblob (Premium-prestaties) kunnen momenteel niet worden gelaagd tot hot, cool of archiveren met [Blob Tier instellen](/rest/api/storageservices/set-blob-tier) of azure blob-opslaglevenscyclusbeheer gebruiken.
> Als u gegevens wilt verplaatsen, moet u blobs synchroon kopiëren van het blokblobopslagaccount naar de hot access-laag in een ander account met behulp van de [URL-API putblok of](/rest/api/storageservices/put-block-from-url) een versie van AzCopy die deze API ondersteunt.
> De *API Voor het blokkeren van URL* kopieert gegevens op de server synchroon, wat betekent dat de aanroep pas wordt voltooid zodra alle gegevens van de oorspronkelijke serverlocatie naar de doellocatie zijn verplaatst.

### <a name="blob-level-tiering-billing"></a>Facturering van laaginstelling op blobniveau

Wanneer een blob wordt geüpload of verplaatst naar de hot-, cool- of archieflaag, wordt deze direct na de wijziging van de laag in rekening gebracht tegen het bijbehorende tarief.

Wanneer een blob wordt verplaatst naar een koelere laag (hot->cool, hot->archief of >archief), wordt de bewerking gefactureerd als een schrijfbewerking naar de doellaag, waar de schrijfbewerking (per 10.000) en gegevens (per GB) kosten van toepassing zijn op de bestemmingslaag.

Wanneer een blob wordt verplaatst naar een warmere laag (archief->cool, archief->heet, of cool->hot), wordt de bewerking gefactureerd als een gelezen van de bronlaag, waar de leesbewerking (per 10.000) en gegevens ophalen (per GB) kosten van toepassing zijn. Kosten voor vroegtijdige verwijdering van een blob die is verplaatst uit de opslaglaag Cool of Archive kunnen ook van toepassing zijn. [Het hydrateren van gegevens uit het archief](storage-blob-rehydration.md) kost tijd en gegevens worden in rekening gebracht archiefprijzen totdat de gegevens online worden hersteld en bloblaag wordt gewijzigd in hot of cool. In de volgende tabel wordt samengevat hoe laagwijzigingen worden gefactureerd:

| | **Schrijfkosten (Bewerking + toegang)** | **Leeskosten (Bewerking + Toegang)**
| ---- | ----- | ----- |
| **De richting van De Klodderrij instellen** | hot->koel,<br> hot->archief,<br> koel->archief | archief->cool,<br> archief->heet,<br> koel->heet

### <a name="cool-and-archive-early-deletion"></a>Vroege verwijdering voor Koud en Archief

Elke blob die wordt verplaatst naar de koele laag (GPv2-accounts alleen) is onderworpen aan een koele vroege verwijdering periode van 30 dagen. Elke blob die naar de archieflaag wordt verplaatst, is onderworpen aan een vroege verwijderingsperiode van 180 dagen voor een archief. Deze kosten zijn evenredig verdeeld. Als een blob bijvoorbeeld wordt verplaatst naar het archief en vervolgens na 45 dagen naar de hot-laag wordt verwijderd of verplaatst, wordt een vroege verwijderingsvergoeding in rekening gebracht die gelijk is aan 135 (180 min 45) dagen voor het opslaan van die blob in het archief.

U de vroege verwijdering berekenen met behulp van de **blob-eigenschap Last-Modified**als er geen wijzigingen in de toegangslaag zijn geweest. Anders u gebruiken wanneer de toegangslaag voor het laatst is gewijzigd om te koelen of te archiveren door de blobeigenschap te bekijken: **toegangs-tier-change-time**. Zie [Blob-eigenschappen opvragen voor](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties)meer informatie over blob-eigenschappen .

## <a name="comparing-block-blob-storage-options"></a>Opties voor opslag van blokblobs vergelijken

In de volgende tabel ziet u een vergelijking van blobopslag met premiumprestaties en de toegangslagen hot, cool en archief.

|                                           | **Premium prestaties**   | **Hot-laag** | **Koele laag**       | **Archieflaag**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Beschikbaarheid**                          | 99,9%                     | 99,9%        | 99%                 | Offline           |
| **Beschikbaarheid** <br> **(RA-GRS-leesbewerkingen)**  | N.v.t.                       | 99,99%       | 99,9%               | Offline           |
| **Gebruikskosten**                         | Hogere opslagkosten, lagere toegangs- en transactiekosten | Hogere opslagkosten, lagere toegangs- en transactiekosten | Lagere opslagkosten, hogere toegang en transactiekosten | Laagste opslagkosten, hoogste toegangs- en transactiekosten |
| **Minimale objectgrootte**                   | N.v.t.                       | N.v.t.          | N.v.t.                 | N.v.t.               |
| **Minimale opslagduur**              | N.v.t.                       | N.v.t.          | 30 dagen<sup>1</sup> | 180 dagen
| **Latentie** <br> **(Tijd tot eerste byte)** | Milliseconden met één cijfer | milliseconden | milliseconden        | uur<sup>2</sup> |

<sup>1</sup> Objecten in de koele laag op GPv2-accounts hebben een minimale bewaarduur van 30 dagen. Blob-opslagaccounts hebben geen minimale bewaarduur voor de koele laag.

<sup>2</sup> Archive Storage ondersteunt momenteel 2 rehydraterenprioriteiten, High en Standard, die verschillende latencies voor ophalen bieden. Zie [Blobgegevens uit de archieflaag opnieuw hydrateren](storage-blob-rehydration.md)voor meer informatie.

> [!NOTE]
> Blob-opslagaccounts ondersteunen dezelfde prestatie- en schaalbaarheidsdoelen als v2-opslagaccounts voor algemene doeleinden. Zie [Schaalbaarheids- en prestatiedoelen voor Blob-opslag voor](scalability-targets.md)meer informatie.

## <a name="quickstart-scenarios"></a>Snelstartscenario's

In deze sectie worden de volgende scenario's gedemonstreerd met behulp van de Azure-portal en powershell:

- Het wijzigen van de toegangslaag van het standaardaccount van een GPv2- of Blob Storage-account.
- Het wijzigen van de opslaglaag van een blob in een GPv2- of Blob Storage-account.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>De toegangslaag van het standaardaccount van een GPv2- of Blob Storage-account wijzigen

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Zoek en selecteer **alle bronnen**in de Azure-portal.

1. Selecteer uw opslagaccount.

1. Selecteer **in Instellingen**de optie **Configuratie** om de accountconfiguratie weer te geven en te wijzigen.

1. Selecteer de juiste toegangslaag voor uw behoeften: stel de **categorie Access** in op **Cool** of **Hot.**

1. Klik bovenaan **op Opslaan.**

![Opslagaccountlaag wijzigen](media/storage-tiers/account-tier.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Het volgende PowerShell-script kan worden gebruikt om de accountlaag te wijzigen. De `$rgName` variabele moet worden geïnitialiseerd met de naam van uw resourcegroep. De `$accountName` variabele moet worden geïnitialiseerd met de naam van uw opslagaccount. 
```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```
---

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>De laag van een blob in een GPv2- of Blob-opslagaccount wijzigen
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Zoek en selecteer **alle bronnen**in de Azure-portal.

1. Selecteer uw opslagaccount.

1. Selecteer de container en selecteer vervolgens de blob.

1. Selecteer laag **Wijzigen**in de **eigenschappen van de Blob**.

1. Selecteer de categorie **Hot,** **Cool**of **Archive** access. Als uw blob momenteel in het archief is en u wilt rehydrateren naar een onlinelaag, u ook een prioriteit voor rehydrateren van **standaard** of **hoog**selecteren.

1. Selecteer **Opslaan** onderin.

![Opslagaccountlaag wijzigen](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Het volgende PowerShell-script kan worden gebruikt om de bloblaag te wijzigen. De `$rgName` variabele moet worden geïnitialiseerd met de naam van uw resourcegroep. De `$accountName` variabele moet worden geïnitialiseerd met de naam van uw opslagaccount. De `$containerName` variabele moet worden geïnitialiseerd met uw containernaam. De `$blobName` variabele moet worden geïnitialiseerd met de naam van uw blob. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```
---

## <a name="pricing-and-billing"></a>Prijzen en facturering

Alle opslagaccounts gebruiken een prijsmodel voor blobopslag blokkeren op basis van de laag van elke blob. Houd rekening met de volgende factureringsoverwegingen:

- **Opslagkosten**: de kosten voor het opslaan van gegevens hangen niet alleen af van de hoeveelheid opgeslagen gegevens, maar ook van de gebruikte toegangslaag. De kosten per GB nemen af als de laag minder dynamisch ('cooler') wordt.
- **Kosten van gegevenstoegang**: de kosten voor gegevenstoegang nemen toe als de laag minder dynamisch ('cooler') wordt. Voor gegevens in de koele en archieftoegangslaag worden kosten in rekening gebracht voor gegevenstoegang per gigabyte voor reads.
- **Transactiekosten:** Er zijn kosten per transactie voor alle lagen die toenemen naarmate de laag koeler wordt.
- **Kosten voor gegevensoverdracht met geo-replicatie**: deze kosten zijn alleen van toepassing op accounts waarvoor geo-replicatie is geconfigureerd, inclusief GRS en RA-GRS. Kosten voor gegevensoverdracht met geo-replicatie worden in rekening gebracht per GB.
- **Kosten voor uitgaande gegevensoverdracht**: uitgaande gegevensoverdracht (gegevens die buiten een Azure-regio worden overgedragen) worden gefactureerd voor bandbreedtegebruik per GB, net zoals bij opslagaccounts voor algemeen gebruik.
- **De toegangslaag wijzigen:** als u de accounttoegangslaag wijzigt, worden de kosten voor laagwijzigingskosten voor inafgeleide blobs van de _toegangslaag_ die zijn opgeslagen in het account en waarvoor geen expliciete laagisset is ingesteld, in rekening gebracht. Raadpleeg facturering op [blobniveau](#blob-level-tiering-billing)voor informatie over het wijzigen van de toegangslaag voor één blob .

> [!NOTE]
> Zie de pagina [Azure Storage Pricing](https://azure.microsoft.com/pricing/details/storage/blobs/) voor meer informatie over de prijzen voor blokkeerblobs. Zie de pagina [Prijsinformatie voor bandbreedte](https://azure.microsoft.com/pricing/details/data-transfers/) voor meer informatie over de kosten voor uitgaande gegevensoverdracht.

## <a name="faq"></a>Veelgestelde vragen

**Moet ik Blob Storage- of GPv2-accounts gebruiken als ik mijn gegevens in lagen wil opslaan?**

Wij raden u aan GPv2 te gebruiken in plaats van Blob Storage-accounts voor opslaglagen. GPv2-accounts ondersteunen alle functies die Blob Storage-accounts ondersteunen, plus nog veel meer. Prijzen van Blob Storage en GPv2 zijn bijna identiek, maar sommige nieuwe functies en prijsverlagingen zijn alleen beschikbaar bij GPv2-accounts. GPv1-accounts bieden geen ondersteuning voor tiering.

De prijsstructuur tussen GPv1- en GPv2-accounts is verschillend en klanten moeten beide zorgvuldig evalueren alvorens te besluiten GPv2-accounts te gebruiken. U kunt een bestaand Blob Storage- of GPv1-account eenvoudig met één muisklik omzetten naar GPv2. Zie [Overzicht van Azure-opslagaccount](../common/storage-account-overview.md) voor meer informatie.

**Kan ik objecten opslaan in alle drie de (hot, cool en archive) toegangslagen in hetzelfde account?**

Ja. Het kenmerk **Access Tier** dat op accountniveau is ingesteld, is de standaardaccountlaag die van toepassing is op alle objecten in dat account zonder een expliciete setlaag. Met blobniveaulagen u de toegangslaag instellen op objectniveau, ongeacht wat de instelling voor de toegangslaag op het account is. Blobs in een van de drie toegangslagen (hot, cool of archive) kunnen binnen hetzelfde account bestaan.

**Kan ik de standaardtoegangslaag van mijn Blob- of GPv2-opslagaccount wijzigen?**

Ja, u de standaardaccountlaag wijzigen door het kenmerk **Access-laag** in te stellen op het opslagaccount. Het wijzigen van de accountlaag is van toepassing op alle objecten die zijn opgeslagen in het account en die geen expliciete laagset hebben (bijvoorbeeld **Hot (afgeleid)** of **Cool (afgeleid).** Het omdelen van de accountlaag van hot naar cool maakt schrijfbewerkingen (per 10.000) voor alle blobs zonder alleen een ingestelde laag in GPv2-accounts en toggling van koel naar heet maakt zowel leesbewerkingen (per 10.000) als kosten voor het ophalen van gegevens (per GB) voor alle blobs in Blob-opslag- en GPv2-accounts.

**Kan ik de toegangslaag van mijn standaardaccount instellen op Archive?**

Nee. Alleen hete en koele toegangslagen kunnen worden ingesteld als de standaardaccounttoegangslaag. Archive kan alleen op objectniveau worden ingesteld. Bij blob-upload geeft u op dat de toegangslaag van uw keuze heet, koel of gearchiveerd is, ongeacht de standaardaccountlaag. Met deze functionaliteit u gegevens rechtstreeks in de archieflaag schrijven om kostenbesparingen te realiseren vanaf het moment dat u gegevens maakt in blob-opslag.

**In welke regio's zijn de hot, cool en archieftoegangslagen beschikbaar in?**

De hot en cool access tiers samen met blob-level tiering zijn beschikbaar in alle regio's. Archive-opslag is in eerste instantie alleen beschikbaar in bepaalde regio's. Zie voor een volledige lijst [Azure-producten beschikbaar per regio](https://azure.microsoft.com/regions/services/).

**Gedragen de blobs in de koele toegangslaag zich anders dan die in de hot access-laag?**

Blobs in de hot access-laag hebben dezelfde latentie als blobs in Opslagaccounts voor GPv1, GPv2 en Blob. Blobs in de koele toegangslaag hebben een vergelijkbare latentie (in milliseconden) als blobs in GPv1-, GPv2- en Blob-opslagaccounts. Blobs in de categorie archieftoegangs hebben enkele uren latentie in GPv1-, GPv2- en Blob-opslagaccounts.

Blobs in de koele toegangslaag hebben een iets lager serviceniveau (SLA) dan de blobs die zijn opgeslagen in de hot access-laag. Zie [Dienstovereenkomst voor Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/) voor meer informatie.

**Zijn de bewerkingen tussen de dynamische-, statische- en archiefopslaglagen hetzelfde?**

Alle bewerkingen tussen de dynamische en statische laag zijn 100% consistent. Alle geldige archiefbewerkingen, waaronder GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier en DeleteBlob, zijn 100% consistent met hot and cool. Blob-gegevens kunnen niet worden gelezen of gewijzigd terwijl ze zich in de archieflaag begeven totdat ze zijn gerehydrateerd. alleen blob metagegevens leesbewerkingen worden ondersteund terwijl in het archief.

**Als ik een blob reactiveer vanuit de archiefopslaglaag naar de dynamische- of statische-opslaglaag, hoe weet ik dan wanneer de reactivering is voltooid?**

Tijdens rehydratie u de bewerking Blob-eigenschappen gebruiken om het kenmerk **Archiefstatus** te peilen en te bevestigen wanneer de laagwijziging is voltooid. De status is "rehydrate-pending-to-hot" of "rehydrate-pending-to-cool", afhankelijk van de doellaag. Na voltooiing wordt de eigenschap archiefstatus verwijderd en weerspiegelt de eigenschap **Access Tier** blob de nieuwe hot- of cool-laag. Zie [Blobgegevens uit de archieflaag opnieuw hydrateren](storage-blob-rehydration.md) voor meer informatie.

**Na het instellen van de opslaglaag van een blob, wanneer wordt er dan gefactureerd tegen het juiste tarief?**

Elke blob wordt altijd gefactureerd op basis van de laag die wordt aangegeven door de eigenschap **Access Tier van** de blob. Wanneer u een nieuwe onlinelaag instelt voor een blob, geeft de eigenschap **Access Tier** onmiddellijk de nieuwe laag weer voor alle overgangen. Het hydrateren van een blob van de offline archieflaag naar een hete of koele laag kan echter enkele uren duren. In dit geval wordt u gefactureerd tegen archiveren totdat de rehydratie is voltooid, waarna de eigenschap **Access Tier** de nieuwe laag weerspiegelt. Eenmaal gehydrateerd naar de online laag, wordt u gefactureerd voor die blob op de hete of koele snelheid.

**Hoe bepaal ik of ik een vroege verwijderingskosten maak bij het verwijderen of verplaatsen van een blob uit de koel- of archieflaag?**

Voor elke blob die binnen 30 dagen of 180 dagen wordt verwijderd of verplaatst uit respectievelijk de statische (alleen GPv2-accounts) of archiefopslaglaag, wordt een vooraf vastgesteld bedrag voor vroegtijdige verwijdering in rekening gebracht. U bepalen hoe lang een blob zich in de koel- of archieflaag bevindt door de eigenschap **Access Tier Change Time-** blob te controleren, die een stempel van de laatste laagwijziging biedt. Als de laag van de blob nooit is gewijzigd, u de eigenschap **Laatst gewijzigd** blob controleren. Zie [Cool en archiveren voor](#cool-and-archive-early-deletion)meer informatie in een vroeg gebruik .

**Welke Azure Tools en SDK's ondersteunen laaginstelling op blobniveau en archiefopslag?**

Azure Portal, PowerShell en CLI Tools en .NET, Java, Python en Node.js-clientbibliotheken bieden ondersteuning voor laaginstelling op blobniveau en archiefopslag.  

**Hoeveel gegevens kan ik opslaan in de dynamische-, statische- en archiefopslaglaag?**

Gegevensopslag samen met andere limieten worden ingesteld op accountniveau en niet per toegangslaag. U ervoor kiezen om al uw limiet in één laag of in alle drie de lagen te gebruiken. Zie [Schaalbaarheids- en prestatiedoelen voor standaardopslagaccounts voor](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)meer informatie .

## <a name="next-steps"></a>Volgende stappen

Hot, cool en archiveren evalueren in Opslagaccounts van GPv2 en Blob

- [De beschikbaarheid controleren van de dynamische, statische en archieflaag per regio](https://azure.microsoft.com/regions/#services)
- [De levenscyclus van Azure Blob-opslag beheren](storage-lifecycle-management-concepts.md)
- [Meer informatie over het opnieuw hydrateren van blobgegevens uit de archieflaag](storage-blob-rehydration.md)
- [Bepalen of premium prestaties uw app ten goede komen](storage-blob-performance-tiers.md)
- [Gebruik van de huidige opslagaccounts evalueren door metrische gegevens voor Azure Storage in te schakelen](../common/storage-enable-and-view-metrics.md)
- [Controleer de prijzen voor hot, cool en archiveren in Blob-opslag- en GPv2-accounts per regio](https://azure.microsoft.com/pricing/details/storage/)
- [Prijzen voor gegevensoverdracht controleren](https://azure.microsoft.com/pricing/details/data-transfers/)
