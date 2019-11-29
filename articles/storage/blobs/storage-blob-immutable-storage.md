---
title: Onveranderbare Blob Storage-Azure Storage
description: Azure Storage biedt WORM (eenmaal schrijven, lezen) voor Blob-opslag (object) die gebruikers in staat stelt om gegevens op te slaan in een niet-bewerk bare, niet-aanpas bare status voor een opgegeven interval.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: 92bfa4f13467763fd88b9ae993554aef69355d75
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555235"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>Bedrijfs kritieke blobgegevens opslaan met onveranderlijke opslag

Onveranderbare opslag voor Azure Blob-opslag stelt gebruikers in staat om bedrijfskritische gegevens objecten op te slaan in een WORM (eenmaal schrijven, gelezen). Met deze status worden de gegevens niet-kan worden gewist en niet kunnen worden gewijzigd voor een door de gebruiker opgegeven interval. Voor de duur van het retentie-interval kunnen blobs worden gemaakt en gelezen, maar niet worden gewijzigd of verwijderd. Onveranderbare opslag is beschikbaar voor algemeen gebruik v2-en Blob Storage-accounts in alle Azure-regio's.

Zie [Onveranderbaarheid-beleid instellen en beheren voor Blob Storage](storage-blob-immutability-policies-manage.md)voor meer informatie over het instellen en wissen van juridische bewaringen of het maken van een op tijd gebaseerd Bewaar beleid met behulp van de Azure Portal, Power shell of Azure cli.

## <a name="about-immutable-blob-storage"></a>Over onveranderbare Blob Storage

Onveranderbare opslag helpt de organisatie van de gezondheids zorg, financiële instellingen en gerelateerde branches&mdash;met name broker-dealer organisaties&mdash;om gegevens veilig op te slaan. Onveranderbare opslag kan ook worden gebruikt in elk scenario om kritieke gegevens te beschermen tegen wijziging of verwijdering.

Typische toepassingen zijn onder andere:

- **Naleving van regelgeving**: onveranderbare opslag voor Azure Blob-opslag helpt organisaties bij het adresseren van 17a-4 (f), CFTC 1.31 (d), FINRA en andere voor Schriften. Een technisch document met Cohasset Associates bevat informatie over hoe onveranderbare opslag adressen deze regelgeving kan worden gedownload via de [micro soft-portal voor service vertrouwen](https://aka.ms/AzureWormStorage). De [Vertrouwenscentrum van Azure](https://www.microsoft.com/trustcenter/compliance/compliance-overview) bevat gedetailleerde informatie over onze nalevings certificeringen.

- **Bewaren van beveiligde documenten**: onveranderbare opslag voor Azure Blob-opslag zorgt ervoor dat gegevens niet kunnen worden gewijzigd of verwijderd door een gebruiker, met inbegrip van gebruikers met beheerders bevoegdheden voor het account.

- **Juridische bewaring**: onveranderbare opslag voor Azure Blob-opslag biedt gebruikers de mogelijkheid gevoelige gegevens op te slaan die van cruciaal belang zijn voor rechts zaken of voor het gebruik van een onrecht matige afsluitings status voor de gewenste duur totdat de bewaring wordt verwijderd. Deze functie is niet beperkt tot juridische use-cases, maar kan ook worden beschouwd als een op gebeurtenissen gebaseerde bewaring of een Enter prise Lock, waarbij de nood zaak om gegevens te beveiligen op basis van gebeurtenis triggers of bedrijfs beleid vereist is.

Onveranderbare opslag ondersteunt de volgende functies:

- **[Ondersteuning voor Bewaar beleid op basis van tijd](#time-based-retention-policies)** : gebruikers kunnen beleid instellen om gegevens op te slaan voor een opgegeven interval. Wanneer een Bewaar beleid op basis van tijd is ingesteld, kunnen blobs worden gemaakt en gelezen, maar niet gewijzigd of verwijderd. Nadat de Bewaar periode is verlopen, kunnen blobs worden verwijderd, maar niet worden overschreven.

- **[Ondersteuning voor beleid voor juridische bewaring](#legal-holds)** : als het Bewaar interval niet bekend is, kunnen gebruikers juridische bewaringen instellen om onveranderbare gegevens op te slaan totdat de juridische bewaring is gewist.  Als er een beleid voor juridische bewaring is ingesteld, kunnen blobs worden gemaakt en gelezen, maar niet gewijzigd of verwijderd. Elke juridische bewaring is gekoppeld aan een door de gebruiker gedefinieerde alfanumerieke code (zoals een case-ID, gebeurtenis naam enzovoort) die wordt gebruikt als een id-teken reeks. 

- **Ondersteuning voor alle BLOB-lagen**: het virus beleid is onafhankelijk van de Azure Blob Storage-laag en is van toepassing op alle lagen: Hot, cool en Archive. Gebruikers kunnen gegevens overzetten naar de meest kosten geoptimaliseerde laag voor hun werk belastingen terwijl ze gegevens Onveranderbaarheid onderhouden.

- **Configuratie op container niveau**: gebruikers kunnen op het niveau van de Bewaar termijn op basis van een op tijd gebaseerd beleid voor het bewaren van inhoud en juridische bewarings codes configureren. Met behulp van eenvoudige instellingen op container niveau kunnen gebruikers op tijd gebaseerd Bewaar beleid maken en vergren delen, bewaar intervallen uitbreiden, juridische bewaringen instellen en wissen. Deze beleids regels zijn van toepassing op alle blobs in de container, zowel bestaande als nieuwe.

- **Ondersteuning voor de controle logboek registratie**: elke container bevat een beleids audit logboek. Het bevat Maxi maal zeven op tijd gebaseerde Bewaar opdrachten voor vergrendeld Bewaar beleid op basis van tijd en bevat de gebruikers-ID, het type opdracht, de tijds tempels en het Bewaar interval. Voor juridische bewaringen bevat het logboek de Tags gebruikers-ID, opdracht type, tijds tempels en juridische bewaring. Dit logboek wordt bewaard gedurende de levens duur van het beleid, overeenkomstig de richt lijnen van de 17a-4 (f). In het [activiteiten logboek van Azure](../../azure-monitor/platform/activity-logs-overview.md) wordt een uitgebreidere logboek van alle activiteiten van het controle vlak weer gegeven. bij het inschakelen van [Azure Diagnostische logboeken](../../azure-monitor/platform/resource-logs-overview.md) worden de gegevenslaag bewerkingen bewaard en weer gegeven. Het is de verantwoordelijkheid van de gebruiker om deze logboeken permanent op te slaan, zoals vereist voor regelgevende of andere doel einden.

## <a name="how-it-works"></a>Het werkt als volgt

Onveranderbare opslag voor Azure Blob Storage ondersteunt twee soorten WORMen of onveranderbaar beleid: Bewaar periode en juridische bewaring op basis van tijd. Wanneer een Bewaar beleid op basis van tijd of wettelijk geblokkeerd wordt toegepast op een container, worden alle bestaande blobs in minder dan 30 seconden omgezet in een onveranderbare WORM status. Alle nieuwe blobs die naar die container worden geüpload, worden ook verplaatst naar de onveranderlijke status. Zodra alle blobs zijn verplaatst naar de onveranderbare status, wordt het onveranderbare beleid bevestigd en worden alle overschrijvings-of verwijder bewerkingen voor bestaande en nieuwe objecten in de onveranderbare container niet toegestaan.

Het verwijderen van containers en opslag accounts is niet toegestaan als er blobs in de container of het opslag account zijn die worden beveiligd door een onveranderbaar beleid. De bewerking voor het verwijderen van de container mislukt als er ten minste één BLOB bestaat met een vergrendeld Bewaar beleid op basis van tijd of een juridische bewaring. De bewerking voor het verwijderen van het opslag account mislukt als er ten minste één WORM container is met een juridische bewaring of een blob met een actief Bewaar interval.

### <a name="time-based-retention-policies"></a>Bewaar beleid op basis van tijd

> [!IMPORTANT]
> Een Bewaar beleid op basis van tijd moet worden *vergrendeld* om de BLOB een compatibele onveranderlijke status (schrijven en verwijderen beveiligd) te hebben voor SEC 17a-4 (f) en andere naleving van regelgeving. We raden u aan het beleid binnen een redelijke hoeveelheid tijd te vergren delen, meestal minder dan 24 uur. De initiële status van een toegepast Bewaar beleid op basis van tijd is *ontgrendeld*, zodat u de functie kunt testen en wijzigingen kunt aanbrengen in het beleid voordat u het vergrendelt. Hoewel de status *ontgrendeld* Onveranderbaarheid beveiliging biedt, is het niet raadzaam om de *Ontgrendelde* status te gebruiken voor andere doel einden dan kortings functies. 

Wanneer een Bewaar beleid op basis van tijd op een container wordt toegepast, blijven alle blobs in de container onveranderbaar voor de duur van de *daad werkelijke* Bewaar periode. De effectieve retentieperiode voor bestaande blobs is gelijk aan het verschil tussen het tijdstip waarop de blob is gemaakt en de door de gebruiker opgegeven retentieperiode.

Voor nieuwe blobs is de effectieve retentieperiode gelijk aan de door de gebruiker opgegeven retentieperiode. Omdat gebruikers het Bewaar interval kunnen uitbreiden, gebruikt onveranderlijke opslag de meest recente waarde van het door de gebruiker opgegeven Bewaar interval om de ingangs periode te berekenen.

Stel bijvoorbeeld dat een gebruiker een Bewaar beleid op basis van tijd maakt met een Bewaar interval van vijf jaar. Een bestaande Blob in die container, _testblob1_, is één jaar geleden gemaakt. De daad werkelijke Bewaar periode voor _testblob1_ is vier jaar. Wanneer een nieuwe blob, _testblob2_, wordt geüpload naar de container, is de daad werkelijke Bewaar periode voor de nieuwe BLOB vijf jaar.

Een niet-vergrendeld op tijd gebaseerd Bewaar beleid wordt alleen aanbevolen voor het testen van functies en een beleid moet worden vergrendeld om compatibel te zijn met SEC 17a-4 (f) en andere naleving van regelgeving. Wanneer een Bewaar beleid op basis van tijd is vergrendeld, kan het beleid niet worden verwijderd en zijn Maxi maal vijf verhogingen van de ingangs periode toegestaan. Zie [Onveranderbaarheid-beleid instellen en beheren voor Blob Storage](storage-blob-immutability-policies-manage.md)voor meer informatie over het instellen en vergren delen van op tijd gebaseerd Bewaar beleid.

De volgende limieten gelden voor het Bewaar beleid:

- Voor een opslag account is het maximum aantal containers met vergrendeld op tijd gebaseerd onveranderbaar beleid 1.000.
- Het minimale Bewaar interval is één dag. De maximum waarde is 146.000 dagen (400 jaar).
- Voor een container is het maximum aantal bewerkingen voor het uitbreiden van een Bewaar interval voor vergrendelde, op tijd gebaseerde onveranderlijke beleids regels 5.
- Voor een container worden Maxi maal zeven op tijd gebaseerde controle logboeken voor Bewaar beleid bewaard voor een vergrendeld beleid.

### <a name="legal-holds"></a>Juridische bewaring

Wanneer u een juridische wacht ruimte instelt, blijven alle bestaande en nieuwe blobs in de status onveranderbaar totdat de juridische bewaring is gewist. Zie [Onveranderbaarheid-beleid instellen en beheren voor Blob Storage](storage-blob-immutability-policies-manage.md)voor meer informatie over het instellen en wissen van juridische bewaringen.

Een container kan op hetzelfde moment zowel een juridische bewaring als een op tijd gebaseerd Bewaar beleid hebben. Alle blobs in die container blijven onveranderbaar totdat alle juridische bewaringen zijn gewist, zelfs als hun ingangs periode is verlopen. Een BLOB blijft daarentegen onveranderbaar totdat de daad werkelijke Bewaar periode is verstreken, zelfs als alle juridische bewaringen zijn gewist.

De volgende tabel bevat de typen Blob Storage-bewerkingen die zijn uitgeschakeld voor de verschillende onveranderbare scenario's. Zie de [Azure Blob Service rest API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) -documentatie voor meer informatie.

|Scenario  |BLOB-status  |BLOB-bewerkingen niet toegestaan  |
|---------|---------|---------|
|Effectieve retentieperiode voor de blob is nog niet verlopen en/of wettelijke bewaring is ingesteld     |Onveranderbaar: beveiligd tegen verwijderen en schrijven         | Plaats BLOB<sup>1</sup>, put blok<sup>1</sup>, blok lijst<sup>1</sup>, container verwijderen, Blob verwijderen, Blob-meta gegevens instellen, pagina plaatsen, Blob-eigenschappen instellen, moment opname-blob, incrementele Kopieer-blob, toevoeg blok         |
|Effectieve retentieperiode voor blob is verlopen     |Alleen beveiligd tegen schrijven (verwijderen is toegestaan)         |Put-BLOB<sup>1</sup>, blok<sup>1</sup>plaatsen, blokkerings lijst<sup>1</sup>toevoegen, Blob-meta gegevens instellen, pagina plaatsen, Blob-eigenschappen instellen, moment opname-blob, incrementele Kopieer-blob, blok toevoegen         |
|Alle juridische bewaringen zijn gewist en er is geen Bewaar beleid op basis van tijd ingesteld op de container     |Veranderlijk         |Geen         |
|Er wordt geen WORM beleid gemaakt (Bewaar periode op basis van tijd of juridische bewaring)     |Veranderlijk         |Geen         |

<sup>1</sup> met de toepassing kunnen deze bewerkingen een nieuwe Blob maken. Alle volgende overschrijvings bewerkingen op een bestaand BLOB-pad in een onveranderbare container zijn niet toegestaan.

De volgende limieten gelden voor juridische bewaringen:

- Voor een opslag account is het maximum aantal containers met een geldige Hold instelling 1.000.
- Voor een container is het maximum aantal juridische bewarings codes 10.
- De minimale lengte van een geldige Hold-tag is drie alfanumerieke tekens. De maximale lengte is 23 alfanumerieke tekens.
- Voor een container worden voor de duur van het beleid Maxi maal 10 controle logboeken voor juridische bewaring bewaard.

## <a name="pricing"></a>Prijzen

Er worden geen extra kosten in rekening gebracht voor het gebruik van deze functie. Onveranderbare gegevens worden op dezelfde manier geprijsd als geveranderde gegevens. Zie de [pagina met prijzen voor Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/)voor prijs informatie over Azure Blob-opslag.

## <a name="faq"></a>Veelgestelde vragen

**Kunt u documentatie voor WORM compatibiliteit opgeven?**

Ja. Om naleving te documenteren, heeft micro soft een toonaangevende onafhankelijke beoordelings onderneming gehouden die is gespecialiseerd in record beheer en Information governance, Cohasset Associates, om onveranderlijke Blob-opslag te evalueren en te voldoen aan de vereisten die specifiek zijn voor de financiële dienst verlening. Cohasset valideerde dat onveranderbare Blob Storage, wanneer deze wordt gebruikt om op tijd gebaseerde blobs in een WORM te bewaren, voldoet aan de relevante opslag vereisten van CFTC regel 1.31 (c)-(d), FINRA regel 4511 en seconde regel 17a-4. Micro soft heeft deze regels als doel gesteld, omdat deze de meest beschrijvende richt lijnen voor het bewaren van records voor financiële instellingen vertegenwoordigen. Het Cohasset-rapport is beschikbaar in het [vertrouwens centrum van micro soft-Services](https://aka.ms/AzureWormStorage). Neem contact op met de ondersteuning van Azure als u een verklaring van micro soft wilt aanvragen met betrekking tot de WORM naleving.

**Is de functie alleen van toepassing op blok-blobs, maar ook op pagina-en toevoeg-blobs?**

Onveranderbare opslag kan worden gebruikt in combi natie met een BLOB-type dat is ingesteld op het niveau van de container, maar we raden u aan om WORM te gebruiken voor containers die voornamelijk blok-blobs bevatten. In tegens telling tot blok-blobs moeten nieuwe pagina-blobs en toevoeg-blobs worden gemaakt buiten een WORM container en vervolgens worden gekopieerd in. Nadat u deze blobs naar een WORM container hebt gekopieerd, worden er geen nieuwe *toevoeg* -blobs of wijzigingen aan een pagina-BLOB toegestaan. Het instellen van een WORM beleid voor een container waarin Vhd's worden opgeslagen (pagina-blobs) voor actieve virtuele machines wordt afgeraden, omdat hiermee de VM-schijf wordt vergrendeld.

**Moet ik een nieuw opslag account maken om deze functie te gebruiken?**

Nee, u kunt onveranderlijke opslag gebruiken met alle bestaande of nieuw gemaakte v2-of Blob Storage-accounts voor algemeen gebruik. Deze functie is bedoeld voor gebruik met blok-blobs in GPv2-en Blob Storage-accounts. V1-opslag accounts voor algemeen gebruik worden niet ondersteund, maar kunnen eenvoudig worden geüpgraded naar de v2 voor algemeen gebruik. Zie [een opslag account upgraden](../common/storage-account-upgrade.md)voor meer informatie over het upgraden van een bestaand v1-opslag account voor algemeen gebruik.

**Kan ik zowel een rechts Bewaar beleid op basis van een wacht tijd Toep assen?**

Ja, een container kan zowel een juridische bewaring als een op tijd gebaseerd Bewaar beleid hebben. Alle blobs in die container blijven onveranderbaar totdat alle juridische bewaringen zijn gewist, zelfs als hun ingangs periode is verlopen. Een BLOB blijft daarentegen onveranderbaar totdat de daad werkelijke Bewaar periode is verstreken, zelfs als alle juridische bewaringen zijn gewist.

**Zijn beleids regels voor juridische bewaring alleen voor juridische procedures of zijn er andere scenario's voor het gebruik?**

Nee, juridisch geblokkeerd is alleen de algemene term die wordt gebruikt voor een Bewaar beleid waarvoor geen tijd is gebaseerd. Het hoeft niet alleen te worden gebruikt voor procedures met betrekking tot rechts zaken. Beleid voor juridische bewaring is handig voor het uitschakelen van overschrijving en verwijderingen voor het beveiligen van belang rijke gegevens van de bedrijfs WORM, waarbij de Bewaar periode onbekend is. U kunt dit als bedrijfs beleid gebruiken voor het beveiligen van de workloads van uw missie kritieke WORM of voor het gebruik van het beleid als stagingbeleid voordat een aangepaste gebeurtenis trigger het gebruik van een op tijd gebaseerd Bewaar beleid vereist. 

**Kan ik een _vergrendeld_ op tijd gebaseerd Bewaar beleid of juridische bewaring verwijderen?**

Alleen een niet-vergrendeld op tijd gebaseerd Bewaar beleid kan uit een container worden verwijderd. Wanneer een Bewaar beleid op basis van tijd is vergrendeld, kan het niet meer worden verwijderd. alleen efficiënte Bewaar periode-uitbrei dingen zijn toegestaan. Codes voor juridische bewaring kunnen worden verwijderd. Wanneer alle juridische Tags worden verwijderd, wordt de juridische bewaring verwijderd.

**Wat gebeurt er als ik een container probeer te verwijderen die een *vergrendeld* retentiebeleid op basis van tijd of een juridische bewaring heeft?**

De bewerking voor het verwijderen van een container mislukt als er ten minste één BLOB bestaat met een vergrendeld Bewaar beleid op basis van tijd of een juridische bewaring. De bewerking voor het verwijderen van een container wordt alleen uitgevoerd als er geen blob met een actief Bewaar interval bestaat en er geen geldige bewaringen zijn. U moet de blobs verwijderen voordat u de container kunt verwijderen.

**Wat gebeurt er als ik een opslagaccount probeer te verwijderen met een WORM-container die een *vergrendeld* retentiebeleid op basis van tijd of een juridische bewaring heeft?**

De verwijdering van het opslagaccount mislukt als er minimaal één WORM-container is die een juridische bewaring of een blob met een actieve retentieperiode bevat. U moet alle WORM containers verwijderen voordat u het opslag account kunt verwijderen. Zie de vorige vraag voor meer informatie over het verwijderen van een container.

**Kan ik de gegevens verplaatsen tussen verschillende blob-lagen (dynamische toegang, statische toegang, archieftoegang) als de blob de status onveranderbaar heeft?**

Ja, u kunt de opdracht BLOB-laag instellen gebruiken om gegevens over de BLOB-lagen te verplaatsen terwijl de gegevens in de status compatibel onveranderlijk blijven. Onveranderbare opslag wordt ondersteund op de BLOB-lagen hot, cool en Archive.

**Wat gebeurt er als ik niet meer betaal en mijn retentieperiode niet is verlopen?**

In het geval van een niet-betaling, geldt het normale Bewaar beleid voor gegevens, zoals is vastgelegd in de voor waarden van uw contract met micro soft.

**Biedt u ook een evaluatie- of respijtperiode voor de zojuist beschreven functie?**

Ja. Wanneer een Bewaar beleid op basis van tijd voor het eerst wordt gemaakt, heeft dit een status die niet is *vergrendeld* . In deze status kunt u elke gewenste wijziging aanbrengen in het Bewaar interval, zoals verg Roten of verkleinen en zelfs het beleid verwijderen. Nadat het beleid is vergrendeld, blijft het vergrendeld totdat het retentie-interval verloopt. Met dit vergrendelde beleid voor komt u dat het Bewaar interval wordt verwijderd en gewijzigd. We raden u ten zeerste aan de *Ontgrendelde* status alleen te gebruiken voor proef doeleinden en het beleid binnen een periode van 24 uur te vergren delen. Deze procedures helpen u te voldoen aan de beschik over de SEC 17a-4 (f) en andere voor Schriften.

**Kan ik zacht verwijderen naast onveranderbaar BLOB-beleid gebruiken?**

Ja. [Zacht verwijderen voor Azure Blob Storage](storage-blob-soft-delete.md) is van toepassing op alle containers in een opslag account, ongeacht of het Bewaar beleid geldig is of op basis van tijd. We raden u aan om voorlopig verwijderen in te scha kelen voor extra beveiliging voordat onveranderbaar WORM beleid wordt toegepast en bevestigd.

**Waar is de functie beschikbaar?**

Onveranderbare opslag is beschikbaar in de regio's openbaar, China en overheid van Azure. Als er geen onveranderlijke opslag ruimte beschikbaar is in uw regio, neemt u contact op met ondersteuning en e-mail azurestoragefeedback@microsoft.com.

## <a name="next-steps"></a>Volgende stappen

[Onveranderbaarheid-beleid instellen en beheren voor Blob Storage](storage-blob-immutability-policies-manage.md)