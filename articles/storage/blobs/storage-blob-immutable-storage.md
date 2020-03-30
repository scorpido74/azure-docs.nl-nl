---
title: Onveranderlijke blobopslag - Azure Storage
description: Azure Storage biedt WORM (Write Once, Read Many) ondersteuning voor Blob (object) storage waarmee gebruikers gegevens in een niet-uitwissende, niet-aanpasbare status voor een opgegeven interval kunnen opslaan.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: a980c7bd068a463956191eece43ec1be233e7890
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367615"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>Bedrijfskritieke blobgegevens opslaan met onveranderlijke opslag

Onveranderlijke opslag voor Azure Blob-opslag stelt gebruikers in staat om bedrijfskritieke gegevensobjecten op te slaan in een WORM-status (Write Once, Read Many). Deze status maakt de gegevens niet-wissend en niet-aanpasbaar voor een door de gebruiker opgegeven interval. Voor de duur van het retentieinterval kunnen blobs worden gemaakt en gelezen, maar kunnen ze niet worden gewijzigd of verwijderd. Onveranderlijke opslag is beschikbaar voor v1-, algemene v2-, BlobStorage- en BlockBlobStorage-accounts voor algemene doeleinden in alle Azure-regio's.

Zie [Onveranderlijkbeleid voor Blob-opslag instellen en](storage-blob-immutability-policies-manage.md)wissen voor informatie over het instellen en wissen van wettelijke opslagregels of het maken van een op tijd gebaseerd bewaarbeleid met behulp van de Azure-portal, PowerShell of Azure CLI.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="about-immutable-blob-storage"></a>Over onveranderlijke Blob-opslag

Onveranderlijke opslag helpt zorgorganisatie, financiële&mdash;instellingen en aanverwante&mdash;industrieën, met name broker-dealerorganisaties, om gegevens veilig op te slaan. Onveranderlijke opslag kan ook in elk scenario worden gebruikt om kritieke gegevens te beschermen tegen wijziging of verwijdering.

Typische toepassingen zijn onder andere:

- **Naleving van de regelgeving:** onveranderlijke opslag voor Azure Blob-opslag helpt organisaties sec 17a-4(f), CFTC 1.31(d), FINRA en andere regelgeving aan te pakken. In een technische whitepaper van Cohasset Associates wordt beschreven hoe onveranderlijke opslag deze wettelijke vereisten kan worden vernieuwd via de [Microsoft Service Trust Portal.](https://aka.ms/AzureWormStorage) Het [Azure Trust Center](https://www.microsoft.com/trustcenter/compliance/compliance-overview) bevat gedetailleerde informatie over onze nalevingscertificeringen.

- **Veilige documentbewaring**: Onveranderlijke opslag voor Azure Blob-opslag zorgt ervoor dat gegevens door geen enkele gebruiker kunnen worden gewijzigd of verwijderd, inclusief gebruikers met accountbeheerdersbevoegdheden.

- **Juridische blokkering**: Onveranderlijke opslag voor Azure Blob-opslag stelt gebruikers in staat om gevoelige informatie op te slaan die van cruciaal belang is voor geschillen of zakelijk gebruik in een tamper-proof status voor de gewenste duur totdat de blokkering is verwijderd. Deze functie is niet alleen beperkt tot juridische gebruikszaken, maar kan ook worden gezien als een op gebeurtenissen gebaseerde blokkering of een ondernemingsslot, waarbij de noodzaak om gegevens te beschermen op basis van gebeurtenistriggers of bedrijfsbeleid vereist is.

Onveranderlijke opslag ondersteunt de volgende functies:

- **[Ondersteuning voor het bewaarbeleid op basis van tijd:](#time-based-retention-policies)** gebruikers kunnen beleid instellen om gegevens op te slaan voor een opgegeven interval. Wanneer een op tijd gebaseerd bewaarbeleid is ingesteld, kunnen blobs worden gemaakt en gelezen, maar niet gewijzigd of verwijderd. Nadat de bewaartermijn is verstreken, kunnen blobs worden verwijderd, maar niet worden overschreven.

- **[Ondersteuning voor juridische wachtbeleid:](#legal-holds)** Als het bewaarinterval niet bekend is, kunnen gebruikers wettelijke blokkeringen instellen om onveranderlijke gegevens op te slaan totdat de juridische blokkering is gewist.  Wanneer een juridisch wachtbeleid is ingesteld, kunnen blobs worden gemaakt en gelezen, maar niet gewijzigd of verwijderd. Elke juridische wachtruimte is gekoppeld aan een door de gebruiker gedefinieerde alfanumerieke tag (zoals een case-id, gebeurtenisnaam, enz.) die wordt gebruikt als een id-tekenreeks. 

- **Ondersteuning voor alle bloblagen:** WORM-beleid is onafhankelijk van de Azure Blob-opslaglaag en is van toepassing op alle lagen: hot, cool en archiveren. Gebruikers kunnen gegevens verplaatsen naar de laag die het meest rendabel is voor hun werkbelastingen, met behoud van de onveranderbaarheid van gegevens.

- **Configuratie op containerniveau:** gebruikers kunnen op tijd gebaseerd bewaarbeleid en wettelijke blokkeringstags op containerniveau configureren. Door eenvoudige instellingen op containerniveau te gebruiken, kunnen gebruikers retentiebeleid op basis van tijd maken en vergrendelen, retentieperioden verlengen, juridische bewaring instellen en opheffen enzovoort. Deze beleidsregels zijn van toepassing op alle blobs in de container - zowel op bestaande als nieuwe blobs.

- **Ondersteuning voor controlelogboekregistratie**: Elke container bevat een beleidscontrolelogboek. Er worden maximaal zeven op tijd gebaseerde bewaaropdrachten weergegeven voor vergrendeld op tijd gebaseerd bewaarbeleid en bevat de gebruikers-id, het opdrachttype, tijdstempels en het bewaarinterval. Voor juridische bewaring bevat het logboek de gebruikers-id, het opdrachttype, tijdstempels en de labels voor juridische bewaring. Dit logboek wordt bewaard voor de levensduur van het beleid, in overeenstemming met de wettelijke richtlijnen van SEC 17a-4(f). Het [Azure-activiteitenlogboek](../../azure-monitor/platform/platform-logs-overview.md) toont een uitgebreider logboek van alle beheervlakactiviteiten. terwijl [azure diagnostic logs](../../azure-monitor/platform/platform-logs-overview.md) worden ingehouden en weergegeven met bewerkingen van gegevensvlak. Het is de verantwoordelijkheid van de gebruiker om deze logboeken permanent op te slaan, wat mogelijk vereist is voor andere doeleinden.

## <a name="how-it-works"></a>Hoe werkt het?

Onveranderbare opslag voor Azure Blobs ondersteunt twee soorten WORM-beleidsregels of beleidsregels voor onveranderbare opslag: retentie op basis van tijd en juridische bewaring. Wanneer een op tijd gebaseerd bewaarbeleid of wettelijke blokkering wordt toegepast op een container, worden alle bestaande blobs in minder dan 30 seconden naar een onveranderlijke WORM-status verplaatst. Alle nieuwe blobs die worden geüpload naar die beveiligde container met beleid, worden ook in een onveranderlijke status verplaatst. Zodra alle blobs in een onveranderlijke status staan, wordt het onveranderlijke beleid bevestigd en zijn overschrijf- of verwijderingsbewerkingen in de onveranderlijke container niet toegestaan.

Het verwijderen van container- en opslagaccountis ook niet toegestaan als er blobs in een container zijn die worden beschermd door een wettelijke blokkering of een vergrendeld op tijd gebaseerd beleid. Een juridisch blokkeringsbeleid beschermt tegen het verwijderen van blob-, container- en opslagaccounts. Zowel ontgrendeld als vergrendeld op tijd gebaseerd beleid beschermen gedurende de opgegeven tijd tegen blobverwijdering. Zowel ontgrendeld als vergrendeld op tijd gebaseerd beleid beschermen alleen tegen het verwijderen van containers als er ten minste één blob in de container bestaat. Alleen een container met *vergrendeld* op tijd gebaseerd beleid beschermt tegen verwijdering van opslagaccounts; containers met ontgrendeld tijdgebaseerd beleid bieden geen bescherming voor het verwijderen van opslagaccount en naleving.

Zie [Onveranderlijk beleid voor Blob-opslag instellen en beheren voor](storage-blob-immutability-policies-manage.md)meer informatie over het instellen en vergrendelen van op tijd gebaseerde bewaarbeleid.

## <a name="time-based-retention-policies"></a>Op tijd gebaseerd bewaarbeleid

> [!IMPORTANT]
> Een op tijd gebaseerd bewaarbeleid moet zijn *vergrendeld* om de blob in een onveranderlijke status te hebben (beveiligde status schrijven en verwijderen) voor SEC 17a-4(f) en andere naleving van de regelgeving. We raden u aan het beleid binnen een redelijke tijd te vergrendelen, meestal minder dan 24 uur. De initiële status van een toegepast op tijd gebaseerd bewaarbeleid wordt *ontgrendeld,* zodat u de functie testen en wijzigingen aanbrengen in het beleid voordat u het vergrendelt. Hoewel de *ontgrendelde* status onveranderlijke bescherming biedt, raden we niet aan om de *ontgrendelde* status te gebruiken voor andere doeleinden dan functieproeven op korte termijn. 

Wanneer een op tijd gebaseerd bewaarbeleid wordt toegepast op een container, blijven alle blobs in de container in de onveranderlijke status gedurende de duur van de *effectieve* bewaarperiode. De effectieve bewaartermijn voor blobs is gelijk aan het verschil tussen de **aanmaaktijd** van de blob en het door de gebruiker opgegeven bewaarinterval. Omdat gebruikers het retentie-interval kunnen verlengen, gebruikt onveranderlijke opslag de meest recente waarde van het opgegeven retentie-interval om de effectieve retentieperiode te berekenen.

Stel dat een gebruiker een op tijd gebaseerd bewaarbeleid maakt met een bewaarinterval van vijf jaar. Een bestaande blob in die container, _testblob1,_ is een jaar geleden gemaakt; dus, de effectieve bewaartermijn voor _testblob1_ is vier jaar. Wanneer een nieuwe blob, _testblob2,_ wordt geüpload naar de container, is de effectieve bewaartermijn voor de _testblob2_ vijf jaar vanaf het moment van creatie.

Een op ontgrendelde tijd gebaseerd bewaarbeleid wordt alleen aanbevolen voor het testen van functies en een beleid moet worden vergrendeld om te voldoen aan SEC 17a-4(f) en andere naleving van de regelgeving. Zodra een op tijd gebaseerd bewaarbeleid is vergrendeld, kan het beleid niet worden verwijderd en is een maximum van vijf verhogingen tot de effectieve bewaartermijn toegestaan.

De volgende limieten zijn van toepassing op bewaarbeleid:

- Voor een opslagaccount is het maximum aantal containers met vergrendeld op tijd gebaseerd onveranderlijk beleid 10.000.
- Het minimale retentieinterval is 1 dag. Het maximum is 146.000 dagen (400 jaar).
- Voor een container is het maximum aantal bewerkingen om een retentieinterval voor vergrendeld op tijd gebaseerde onveranderlijke beleidsregels uit te breiden 5.
- Voor een container worden maximaal zeven op tijd gebaseerde controlelogboeken voor bewaarbeleid bewaard voor een vergrendeld beleid.

### <a name="allow-protected-append-blobs-writes"></a>Beveiligde aanhangselblobs schrijven toestaan

Het toevoegen van blobs bestaat uit gegevensblokken en is geoptimaliseerd voor gegevenstoevoegende bewerkingen die vereist zijn door controle- en logboekscenario's. Door het ontwerp, toevoegen blobs alleen toestaan dat de toevoeging van nieuwe blokken aan het einde van de blob. Ongeacht onveranderlijkheid, wijziging of verwijdering van bestaande blokken in een toevoegenblob is fundamenteel niet toegestaan. Zie Over het toevoegen van [blobs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)voor meer informatie over het toevoegen van blobs .

Alleen op tijd gebaseerd `allowProtectedAppendWrites` bewaarbeleid heeft een instelling waarmee nieuwe blokken kunnen worden geschreven naar een blob met inachtneming van de bescherming en naleving van onveranderlijkheid. Als dit is ingeschakeld, u rechtstreeks in de beveiligde container van het beleid een toevoegende blob maken en nieuwe gegevensblokken blijven toevoegen aan het einde van bestaande toevoegende blobs met behulp van de *AppendBlock-API.* Alleen nieuwe blokken kunnen worden toegevoegd en bestaande blokken kunnen niet worden gewijzigd of verwijderd. Bescherming tegen vaccinatie van tijdbehoud is nog steeds van toepassing, waardoor het verwijderen van de toevoegende blob wordt voorkomen totdat de effectieve bewaartermijn is verstreken. Het inschakelen van deze instelling heeft geen invloed op het onveranderlijkheidsgedrag van blokblobs of paginablobs.

Aangezien deze instelling deel uitmaakt van een op tijd gebaseerd bewaarbeleid, blijven de toevoegende blobs nog steeds in de onveranderlijke status gedurende de duur van de *effectieve* bewaarperiode. Aangezien nieuwe gegevens kunnen worden toegevoegd na de eerste creatie van de toevoegende blob, is er een klein verschil in de manier waarop de bewaartermijn wordt bepaald. De effectieve retentie is het verschil tussen de **laatste wijzigingstijd** van de blob en het door de gebruiker opgegeven bewaarinterval. Op dezelfde manier gebruikt onveranderlijke opslag de meest recente waarde van het door de gebruiker opgegeven bewaarinterval om de effectieve bewaarperiode te berekenen.

Stel dat een gebruiker bijvoorbeeld een op tijd `allowProtectedAppendWrites` gebaseerd bewaarbeleid maakt met ingeschakeld en een bewaarinterval van 90 dagen. Er wordt vandaag een toevoegende blob, _logblob1_, gemaakt in de container, nieuwe logboeken worden de komende 10 dagen toegevoegd aan de toevoegende blob; dus, de effectieve bewaartermijn voor de _logblob1_ is 100 dagen vanaf vandaag (de tijd van de laatste aanhangsel + 90 dagen).

Met een ontgrendeld op tijd `allowProtectedAppendWrites` gebaseerd bewaarbeleid kan de instelling op elk gewenst moment worden ingeschakeld en uitgeschakeld. Zodra het op tijd gebaseerde `allowProtectedAppendWrites` bewaarbeleid is vergrendeld, kan de instelling niet meer worden gewijzigd.

Legal hold policies `allowProtectedAppendWrites` cannot enable and any legal holds will nullify the 'allowProtectedAppendWrites' property. Als een wettelijke blokkering wordt toegepast op `allowProtectedAppendWrites` een op tijd gebaseerd bewaarbeleid met ingeschakeld, mislukt de *API appendBlock* totdat de juridische blokkering is opgeheven.

## <a name="legal-holds"></a>Juridische bewaring

Juridische houdt zijn tijdelijke ruimen die kunnen worden gebruikt voor juridisch onderzoek doeleinden of algemene bescherming beleid. Elk juridisch wachtbeleid moet worden gekoppeld aan een of meer tags. Tags worden gebruikt als een benoemde id, zoals een case-id of gebeurtenis, om het doel van de wachtstand te categoriseren en te beschrijven.

Een container kan zowel een wettelijke greep als een tijdgebaseerd bewaarbeleid tegelijkertijd hebben. Alle blobs in die container blijven onveranderbaar totdat alle juridische bewaringen zijn opgeheven, zelfs als hun effectieve retentieperiode is verlopen. Een blob blijft daarentegen onveranderbaar totdat de effectieve retentieperiode is verlopen, zelfs als alle juridische blokkeringen zijn gewist.

De volgende limieten gelden voor wettelijke bepalingen:

- Voor een opslagaccount is het maximum aantal containers met een wettelijke blokkering 10.000.
- Voor een container is het maximum aantal wettelijke hold-tags 10.
- De minimale lengte van een juridische hold-tag is drie alfanumerieke tekens. De maximale lengte is 23 alfanumerieke tekens.
- Voor een container worden maximaal 10 juridische controlelogboeken voor het wettelijk houdbeleid bewaard voor de duur van het beleid.

## <a name="scenarios"></a>Scenario's
In de volgende tabel worden de typen Blob-opslagbewerkingen weergegeven die zijn uitgeschakeld voor de verschillende onveranderlijke scenario's. Zie de [API-documentatie van Azure Blob Service REST voor](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) meer informatie.

|Scenario  |Blobstatus  |Blob-bewerkingen geweigerd  |Bescherming van containers en rekening
|---------|---------|---------|---------|
|Effectieve retentieperiode voor de blob is nog niet verlopen en/of wettelijke bewaring is ingesteld     |Onveranderbaar: beveiligd tegen verwijderen en schrijven         | Blob<sup>1</sup>, Plaats Blok<sup>1</sup>, Lijst met blokken<sup>1</sup>, Verwijder container, Blob verwijderen, Blob-metagegevens instellen, Pagina zetten, Blob-eigenschappen instellen, Blob momentopname, incrementele kopieerblob, blok<sup>toevoegen 2</sup>         |Verwijdering van containers geweigerd; Verwijdering van opslagaccount geweigerd         |
|Effectief bewaarinterval op de blob is verlopen en er is geen wettelijke blokkering ingesteld    |Alleen beveiligd tegen schrijven (verwijderen is toegestaan)         |Blob<sup>1</sup>, Plaats blok<sup>1</sup>, Lijst met plaatsen<sup>blokkeren 1</sup>, Blob-metagegevens instellen, Pagina plaatsen, Blob-eigenschappen instellen, Blob momentopname, incrementele kopieblob, blok<sup>toevoegen 2</sup>         |Verwijdering van containers geweigerd als er ten minste 1 blob bestaat binnen een beveiligde container; Verwijdering van opslagaccount wordt alleen geweigerd voor *vergrendeld* op tijd gebaseerd beleid         |
|Geen WORM-beleid toegepast (geen op tijd gebaseerde retentie en geen wettelijke hold-tag)     |Veranderlijk         |Geen         |Geen         |

<sup>1</sup> Met de blobservice kunnen deze bewerkingen één keer een nieuwe blob maken. Alle volgende overschrijfbewerkingen op een bestaand blobpad in een onveranderlijke container zijn niet toegestaan.

<sup>2</sup> Append Block is alleen toegestaan voor op `allowProtectedAppendWrites` tijd gebaseerdbewaarbeleid met de eigenschap ingeschakeld. Zie de sectie [Beveiligde schrijfblobs toestaan](#allow-protected-append-blobs-writes) voor meer informatie.

## <a name="pricing"></a>Prijzen

Er zijn geen extra kosten verbonden aan het gebruik van deze functie. Onveranderlijke gegevens worden op dezelfde manier geprijsd als veranderlijke gegevens. Zie de [prijspagina azure storage](https://azure.microsoft.com/pricing/details/storage/blobs/)voor prijsgegevens over Azure Blob-opslag.

## <a name="faq"></a>Veelgestelde vragen

**u documentatie van WORM compliance?**

Ja. Om de naleving te documenteren, behield Microsoft een toonaangevend onafhankelijk beoordelingsbureau dat gespecialiseerd is in recordsbeheer en informatiebeheer, Cohasset Associates, om onveranderlijke Blob-opslag en de naleving ervan te evalueren aan vereisten die specifiek zijn voor de financiële dienstverlening. Cohasset heeft gevalideerd dat onveranderlijke Blob-opslag, wanneer deze wordt gebruikt om op tijd gebaseerde Blobs in een WORM-status te houden, voldoet aan de relevante opslagvereisten van CFTC-regel 1.31(c)-(d), FINRA-regel 4511 en SEC-regel 17a-4. Microsoft heeft deze set regels getarget, omdat deze wereldwijd de meest prescriptieve richtlijnen vertegenwoordigen voor het bewaren van records voor financiële instellingen. Het rapport Cohasset is beschikbaar in het [Microsoft Service Trust Center.](https://aka.ms/AzureWormStorage) Neem contact op met Azure Support om een attestbrief van Microsoft aan te vragen met betrekking tot de naleving van worm-onveranderlijkheid.

**Is de functie alleen van toepassing op het blokkeren van blobs en het toevoegen van blobs, of op paginablobs?**

Onveranderlijke opslag kan worden gebruikt met elk blobtype zoals deze is ingesteld op containerniveau, maar we raden u aan WORM te gebruiken voor containers die voornamelijk blokblobs opslaan en blobs toevoegen. Bestaande blobs in een container worden beschermd door een nieuw ingesteld WORM-beleid. Maar elke nieuwe pagina blobs moeten worden gemaakt buiten de WORM container, en vervolgens gekopieerd. Eenmaal gekopieerd in een WORM-container, zijn geen verdere wijzigingen in een paginablob toegestaan. Het instellen van een WORM-beleid op een container die VHD's (paginablobs) opslaat voor actieve virtuele machines wordt afgeraden omdat deze de VM-schijf vergrendelt. We raden u aan de documentatie grondig te bekijken en uw scenario's te testen voordat u een op tijd gebaseerd beleid vergrendelt.

**Moet ik een nieuw opslagaccount maken om deze functie te kunnen gebruiken?**

Nee, u onveranderlijke opslag gebruiken met bestaande of nieuw gemaakte v1-, v2-, blobopslag- of BlockBlobStorage-accounts voor algemene doeleinden. V1-opslagaccounts voor algemene doeleinden worden ondersteund, maar we raden u aan te upgraden naar v2 voor algemene doeleinden, zodat u profiteren van meer functies. Zie [Een opslagaccount upgraden](../common/storage-account-upgrade.md)voor informatie over het upgraden van een bestaand v1-opslagaccount voor algemene doeleinden.

**Kan ik zowel een wettelijk wacht- als tijdgebonden bewaarbeleid toepassen?**

Ja, een container kan zowel een wettelijke greep als een op tijd gebaseerd bewaarbeleid tegelijkertijd hebben; de instelling 'allowProtectedAppendWrites' is echter niet van toepassing totdat de juridische greep is gewist. Alle blobs in die container blijven onveranderbaar totdat alle juridische bewaringen zijn opgeheven, zelfs als hun effectieve retentieperiode is verlopen. Een blob blijft daarentegen onveranderbaar totdat de effectieve retentieperiode is verlopen, zelfs als alle juridische blokkeringen zijn gewist. 

**Zijn juridische hold-beleid alleen voor gerechtelijke procedures of zijn er andere gebruiksscenario's?**

Nee, Legal Hold is slechts de algemene term die wordt gebruikt voor een niet-time-based retentiebeleid. Het hoeft niet alleen te worden gebruikt voor geschillengerelateerde procedures. Legal Hold-beleid is handig voor het uitschakelen van overschrijven en verwijderen voor het beschermen van belangrijke WORM-gegevens voor bedrijven, waarbij de bewaartermijn onbekend is. U het gebruiken als een bedrijfsbeleid om uw bedrijfskritieke WORM-workloads te beschermen of het te gebruiken als een faseringsbeleid voordat een aangepaste gebeurtenistrigger het gebruik van een op tijd gebaseerd bewaarbeleid vereist. 

**Kan ik een _vergrendeld_ op tijd gebaseerd bewaarbeleid of wettelijke blokkering verwijderen?**

Alleen ontgrendeld op tijd gebaseerd bewaarbeleid kan uit een container worden verwijderd. Zodra een op tijd gebaseerd bewaarbeleid is vergrendeld, kan het niet worden verwijderd. alleen effectieve bewaartermijnverlengingen zijn toegestaan. Juridische hold-tags kunnen worden verwijderd. Wanneer alle juridische tags worden verwijderd, wordt de juridische blokkering verwijderd.

**Wat gebeurt er als ik probeer een container te verwijderen met een op tijd gebaseerd bewaarbeleid of wettelijke blokkering?**

De bewerking Container verwijderen mislukt als er ten minste één blob in de container bestaat met een vergrendeld of ontgrendeld op tijd gebaseerd bewaarbeleid of als de container een wettelijke blokkering heeft. De bewerking Container verwijderen wordt alleen uitgevoerd als er geen blobs in de container bestaan en er geen wettelijke greep zijn. 

**Wat gebeurt er als ik probeer een opslagaccount te verwijderen met een container met een op tijd gebaseerd bewaarbeleid of wettelijke blokkering?**

Het verwijderen van het opslagaccount mislukt als er ten minste één container is met een wettelijke blokkeringsset of een **vergrendeld** op tijd gebaseerd beleid. Een container met een ontgrendeld op tijd gebaseerd beleid beschermt niet tegen het verwijderen van opslagaccounts. U moet alle wettelijke ruimen verwijderen en alle **vergrendelde** containers verwijderen voordat u het opslagaccount verwijderen. Zie de voorgaande vraag voor informatie over het verwijderen van containers. U ook verdere verwijderingsbeveiligingen voor uw opslagaccount toepassen met [Azure Resource Manager-vergrendelingen.](../../azure-resource-manager/management/lock-resources.md)

**Kan ik de gegevens over verschillende bloblagen verplaatsen (hot, cool, archiveren) wanneer de blob zich in de onveranderlijke status bevindt?**

Ja, u de opdracht Blob Tier instellen gebruiken om gegevens over de bloblagen te verplaatsen terwijl de gegevens in de compatibele onveranderlijke status blijven. Onveranderlijke opslag wordt ondersteund op hot-, cool- en archiefbloblagen.

**Wat gebeurt er als ik niet meer betaal en mijn retentieperiode niet is verlopen?**

In het geval van niet-betaling is het normale beleid voor het bewaren van gegevens van toepassing zoals bepaald in de algemene voorwaarden van uw contract met Microsoft. Zie [Gegevensbeheer bij Microsoft voor](https://www.microsoft.com/en-us/trust-center/privacy/data-management)algemene informatie. 

**Biedt u ook een evaluatie- of respijtperiode voor de zojuist beschreven functie?**

Ja. Wanneer een op tijd gebaseerd bewaarbeleid voor *unlocked* het eerst wordt gemaakt, is het ontgrendeld. In deze status u elke gewenste wijziging aanbrengen in het bewaarinterval, zoals het beleid verhogen of verkleinen en zelfs verwijderen. Nadat het beleid is vergrendeld, blijft het vergrendeld totdat het bewaarinterval is verlopen. Dit vergrendelde beleid voorkomt verwijdering en wijziging van het bewaarinterval. We raden u ten zeerste aan de *ontgrendelde* status alleen te gebruiken voor proefdoeleinden en het beleid binnen een periode van 24 uur te vergrendelen. Deze praktijken helpen u te voldoen aan SEC 17a-4(f) en andere voorschriften.

**Kan ik soft delete gebruiken naast onveranderlijkblobbeleid?**

Ja, als uw nalevingsvereisten het mogelijk maken om soft delete in te schakelen. [Soft delete voor Azure Blob-opslag](storage-blob-soft-delete.md) is van toepassing op alle containers binnen een opslagaccount, ongeacht een wettelijke blokkering of een op tijd gebaseerd bewaarbeleid. We raden u aan soft delete in te schakelen voor extra bescherming voordat er onveranderlijke WORM-beleidsregels worden toegepast en bevestigd.

## <a name="next-steps"></a>Volgende stappen

- [Onveranderlijkbeleid voor Blob-opslag instellen en beheren](storage-blob-immutability-policies-manage.md)
- [Regels instellen om blobgegevens automatisch te combineren en te verwijderen met levenscyclusbeheer](storage-lifecycle-management-concepts.md)
- [Voorlopig verwijderen voor Azure Storage-blobs](../blobs/storage-blob-soft-delete.md)
- [Bescherm abonnementen, brongroepen en resources met Azure Resource Manager-vergrendelingen](../../azure-resource-manager/management/lock-resources.md).
