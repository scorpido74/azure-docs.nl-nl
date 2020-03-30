---
title: Inzicht in uw Azure Cosmos DB-factuur
description: In dit artikel wordt uitgelegd hoe u uw Azure Cosmos DB-factuur begrijpen met enkele voorbeelden.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.reviewer: sngun
ms.openlocfilehash: 5954c8eda370c0734985c47cfff6d073f5d76d17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258019"
---
# <a name="understand-your-azure-cosmos-db-bill"></a>Meer informatie over uw Azure Cosmos DB-factuur

Als volledig beheerde cloud-native databaseservice vereenvoudigt Azure Cosmos DB facturering door alleen kosten in rekening te brengen voor ingerichte doorvoer en verbruikte opslag. Er zijn geen extra licentiekosten, hardware, nutskosten of facilitaire kosten in vergelijking met on-premises of IaaS-gehoste alternatieven. Wanneer u kijkt naar de multi-regiomogelijkheden van Azure Cosmos DB, biedt de databaseservice een aanzienlijke verlaging van de kosten in vergelijking met bestaande on-premises of IaaS-oplossingen.

Met Azure Cosmos DB wordt u per uur gefactureerd op basis van de ingerichte doorvoer en de verbruikte opslag. Voor de ingerichte doorvoer is de eenheid voor facturering 100 RU/sec per uur, in rekening gebracht voor $ 0,008 per uur, uitgaande van standaard openbare prijzen, zie de [prijspagina](https://azure.microsoft.com/pricing/details/cosmos-db/). Voor de verbruikte opslag wordt $ 0,25 per 1 GB opslagruimte per maand in rekening gebracht, zie de [prijspagina](https://azure.microsoft.com/pricing/details/cosmos-db/). 

In dit artikel worden enkele voorbeelden gebruikt om inzicht te krijgen in de details van de maandelijkse factuur. De getallen in de voorbeelden kunnen afwijken wanneer voor uw Azure Cosmos-containers een andere hoeveelheid doorvoer is ingericht, wanneer ze meerdere regio’s bestrijken, of als ze worden uitgevoerd voor een andere periode dan een maand.

> [!NOTE]
> Facturering is voor een deel van een wandklokuur, niet voor een duur van 60 minuten.

## <a name="billing-examples"></a>Voorbeelden van facturering

### <a name="billing-example---throughput-on-a-container-full-month"></a>Voorbeeld van facturering - doorvoer op een container (volledige maand)

* Stel dat u een doorvoer van 1.000 RU/sec op een container configureert en deze bestaat 24 uur * 30 dagen voor de maand = 720 uur in totaal.  

* 1.000 RU/sec is 10 eenheden van 100 RU/sec per uur voor elk uur dat de containers bestaan (dat wil zeggen, 1.000/100 = 10). 

* Vermenigvuldigen 10 eenheden per uur met de kosten van $0.008 (per 100 RU/sec per uur) = $0.08 per uur. 

* Het vermenigvuldigen van de $ 0,08 per uur met het aantal uren in de maand is gelijk aan $ 0,08 * 24 uur * 30 dagen = $ 57,60 voor de maand.  

* De totale maandelijkse factuur toont 7.200 eenheden (van 100 RUs), die $ 57,60 zal kosten.

### <a name="billing-example---throughput-on-a-container-partial-month"></a>Voorbeeld van facturering - doorvoer op een container (gedeeltelijke maand)

* Laten we aannemen dat we een container maken met een ingerichte doorvoer van 2.500 RU/sec. De container leeft gedurende de maand 24 uur lang (we verwijderen deze bijvoorbeeld 24 uur nadat we deze hebben gemaakt).  

* Dan zien we 600 eenheden op de factuur (2.500 RU/ sec / 100 RU/sec/unit * 24 uur). De kosten zullen $4.80 (600 eenheden * $0.008/eenheid) zijn.

* De totale factuur voor de maand is $ 4,80.

### <a name="billing-rate-if-storage-size-changes"></a>Factureringspercentage als de opslaggrootte verandert

Opslagcapaciteit wordt gefactureerd in eenheden van de maximale hoeveelheid gegevens per uur die gedurende een maandelijkse periode in GB is opgeslagen. Als u bijvoorbeeld 100 GB opslagruimte voor de helft van de maand en 50 GB voor de tweede helft van de maand hebt gebruikt, wordt u in die maand gefactureerd voor een equivalent van 75 GB opslagruimte.

### <a name="billing-rate-when-container-or-a-set-of-containers-are-active-for-less-than-an-hour"></a>Factureringspercentage wanneer container of een set containers minder dan een uur actief zijn

Er wordt het vaste tarief in rekening gebracht voor elk uur dat de container of database bestaat, ongeacht het gebruik of als de container of database minder dan een uur actief is. Als u bijvoorbeeld een container of database maakt en deze 5 minuten later verwijdert, bevat uw factuur een uur.

### <a name="billing-rate-when-throughput-on-a-container-or-database-scales-updown"></a>Factureringssnelheid wanneer doorvoer op een container of database omhoog/omlaag wordt opschaald

Als u de ingerichte doorvoer om 9:30 uur verhoogt van 400 RU/sec naar 1.000 RU/sec en vervolgens de ingerichte doorvoer om 10:45 uur terugbrengt naar 400 RU/sec, worden er twee uur kosten in rekening gebracht van 1.000 RU/sec. 

Als u de ingerichte doorvoer voor een container of een set containers om 9:30 uur verhoogt van 100-K RU/sec naar 200 K RU/sec en vervolgens de ingerichte doorvoer om 10:45 uur terugnaar 100-K RU/sec, wordt u twee uur van 200 K RU/sec in rekening gebracht.

### <a name="billing-example-multiple-containers-each-with-dedicated-provisioned-throughput-mode"></a>Voorbeeld van facturering: meerdere containers, elk met een speciale ingerichte doorvoermodus

* Als u een Azure Cosmos-account aanmaakt in Oost-VS 2 met twee containers met een ingerichte doorvoer van respectievelijk 500 RU/sec en 700 RU/sec, hebt u een totale ingerichte doorvoer van 1.200 RU/sec.  

* Er wordt 1.200/100 * $0,008 = $0.096/uur in rekening gebracht. 

* Als uw doorvoerbehoeften zijn gewijzigd en u de capaciteit van elke container met 500 RU/sec hebt verhoogd en tegelijkertijd een nieuwe onbeperkte container met 20.000 RU/sec hebt gemaakt, zou uw totale ingerichte capaciteit 22.200 RU/sec (1.000 RU/sec + 1.200 RU/sec + 20.000RU/sec) zijn.  

* Uw factuur zou dan veranderen in: $0.008 x 222 = $1.776/hour. 

* In een maand van 720 uur (24 uur * 30 dagen), als voor 500 uur ingerichte doorvoer was 1.200 RU/sec en voor de resterende 220 uur ingerichte doorvoer was 22.200 RU/ sec, uw maandelijkse factuur toont: 500 x $ 0.096/uur + 220 x $ 1.776 /uur = $ 438,72 / maand.

![Voorbeeld van speciale doorvoerfactuur](./media/understand-your-bill/bill-example1.png)

### <a name="billing-example-containers-with-shared-throughput-mode"></a>Voorbeeld van facturering: containers met gedeelde doorvoermodus

* Als u een Azure Cosmos-account maakt in Oost-US 2 met twee Azure Cosmos-databases (met een set containers die de doorvoer op databaseniveau delen) met de ingerichte doorvoer van respectievelijk 50-K RU/sec en 70-K RU/sec, hebt u een totale ingerichte doorvoer van 120 K RU/sec.  

* Er wordt 1200 x $ 0,008 = $ 9,60 / uur in rekening gebracht. 

* Als uw doorvoerbehoeften zijn gewijzigd en u de ingerichte doorvoer van elke database met 10K RU/sec voor elke database hebt verhoogd en u een nieuwe container toevoegt aan de eerste database met een speciale doorvoermodus van 15-K RU/sec aan uw gedeelde doorvoerdatabase, zou uw totale ingerichte capaciteit 155-K RU/sec zijn (60 K RU/sec + 80 K RU/sec + 15 K RU/sec).  

* Uw factuur zou dan veranderen in: 1.550 * $0,008 = $12.40/hour.  

* In een maand van 720 uur, als voor 300 uur voorzien doorvoer was 120-K RU / sec en voor de resterende 420 uur voorzien doorvoer was 155-K RU / sec, zal uw maandelijkse factuur laten zien: 300 x $ 9,60 / uur + 420 x $ 12,40 / uur = $ 2.880 + $ 5.208 = $ 8.088 / maand. 

![Voorbeeld van gedeelde doorvoerfactuur](./media/understand-your-bill/bill-example2.png)

## <a name="billing-examples-with-geo-replication-and-multi-master"></a>Voorbeelden van facturering met georeplicatie en multimaster  

U Azure-regio's overal ter wereld op elk gewenst moment toevoegen/verwijderen aan uw Azure Cosmos-databaseaccount. De doorvoer die u hebt geconfigureerd voor verschillende Azure Cosmos-databases en -containers, wordt gereserveerd in elk van de Azure-regio's die zijn gekoppeld aan uw Azure Cosmos-databaseaccount. Als de som van de ingerichte doorvoer (RU/sec) die is geconfigureerd voor alle databases en containers binnen uw Azure Cosmos-databaseaccount (ingericht per uur) T is en het aantal Azure-regio's dat is gekoppeld aan uw databaseaccount N is, vervolgens is de totale ingerichte doorvoer voor een bepaald uur, voor uw Azure Cosmos-databaseaccount, (a) geconfigureerd met één schrijfgebied gelijk aan T x N RU/sec en (b) geconfigureerd met alle regio's die schrijfbewerkingen kunnen verwerken, is gelijk aan respectievelijk T x (N+1) RU/sec. Ingerichte doorvoer (single write region) kost $ 0,008/uur per 100 RU/sec en de ingerichte doorvoer met meerdere beschrijfbare regio's (multi-master config) kost $ 0,016 / per uur per 100 RU/sec (zie de [prijspagina).](https://azure.microsoft.com/pricing/details/cosmos-db/) Azure Cosmos DB, of het nu de afzonderlijke schrijfregio's of meerdere schrijfregio's zijn, met Azure Cosmos DB u gegevens uit elke regio lezen.

### <a name="billing-example-multi-region-azure-cosmos-account-single-region-writes"></a>Voorbeeld van facturering: Azure Cosmos-account met meerdere regio's, schrijft één regio

Stel dat u een Azure Cosmos-container in West-VS hebt. De container is gemaakt met doorvoer 10K RU/sec en u slaat deze maand 1 TB aan gegevens op. Stel dat u drie regio's (Oost-VS, Noord-Europa en Oost-Azië) toevoegt aan uw Azure Cosmos-account, elk met dezelfde opslag en doorvoer. Uw totale maandelijkse factuur zal worden (uitgaande van 30 dagen in een maand). Uw factuur zou als volgt zijn: 

|**Item** |**Gebruik (maand)** |**Tarief** |**Maandelijkse kosten** |
|---------|---------|---------|-------|
|Overslagvoor container in West VS      | 10.000 RU/sec * 24 * 30    |$ 0,008 per 100 RU/sec per uur   |$ 576|
|Doorvoerfactuur voor 3 extra regio's - Oost-VS, Noord-Europa en Oost-Azië       | 3 * 10K RU/sec * 24 * 30    |$ 0,008 per 100 RU/sec per uur  |$ 1.728|
|Opslagfactuur voor container in West-VS      | 250 GB    |$ 0,25/GB  |$ 62,50|
|Opslagfactuur voor 3 extra regio's - Oost-VS, Noord-Europa en Oost-Azië      | 3 * 250 GB    |$ 0,25/GB  |$ 187,50|
|**Totaal**     |     |  |**$ 2.554**|

*Laten we er ook van uitgaan dat u elke maand 100 GB aan gegevens uit de container in West-VS stapt om gegevens te repliceren naar Oost-VS, Noord-Europa en Oost-Azië. U wordt gefactureerd voor uitgaande gegevensoverdrachtssnelheden.*

### <a name="billing-example-multi-region-azure-cosmos-account-multi-region-writes"></a>Voorbeeld van facturering: Azure Cosmos-account met meerdere regio's, schrijft meerdere regio's

Stel dat u een Azure Cosmos-container maakt in West-VS. De container is gemaakt met doorvoer 10K RU/sec en u slaat deze maand 1 TB aan gegevens op. Stel dat u drie regio's (Oost-VS, Noord-Europa en Oost-Azië) toevoegt, elk met dezelfde opslag en doorvoer en u wilt de mogelijkheid hebben om te schrijven naar de containers in alle regio's die zijn gekoppeld aan uw Azure Cosmos-account. Uw totale maandelijkse factuur zal worden (uitgaande van 30 dagen in een maand) als volgt:

|**Item** |**Gebruik (maand)**|**Tarief** |**Maandelijkse kosten** |
|---------|---------|---------|-------|
|Doorvoerfactuur voor container in West-VS (alle regio's zijn beschrijfbaar)       | 10.000 RU/sec * 24 * 30    |$ 0,016 per 100 RU/sec per uur    |$1.152 |
|Doorvoerfactuur voor 3 extra regio's - Oost-VS, Noord-Europa en Oost-Azië (alle regio's zijn beschrijfbaar)        | (3 + 1) * 10K RU/sec * 24 * 30    |$ 0,016 per 100 RU/sec per uur   |$ 4.608 |
|Opslagfactuur voor container in West-VS      | 250 GB    |$ 0,25/GB  |$ 62,50|
|Opslagfactuur voor 3 extra regio's - Oost-VS, Noord-Europa en Oost-Azië      | 3 * 250 GB    |$ 0,25/GB  |$ 187,50|
|**Totaal**     |     |  |**$ 6.010**|

*Laten we er ook van uitgaan dat u elke maand 100 GB aan gegevens uit de container in West-VS stapt om gegevens te repliceren naar Oost-VS, Noord-Europa en Oost-Azië. U wordt gefactureerd voor uitgaande gegevensoverdrachtssnelheden.*

### <a name="billing-example-azure-cosmos-account-with-multi-master-database-level-throughput-including-dedicated-throughput-mode-for-some-containers"></a>Voorbeeld van facturering: Azure Cosmos-account met doorvoer op databaseniveau, inclusief speciale doorvoermodus voor sommige containers

Laten we eens kijken naar het volgende voorbeeld, waarbij we een Azure Cosmos-account met meerdere regio's hebben waarbij alle regio's beschrijfbaar zijn (multi-master config). Voor de eenvoud gaan we ervan uit dat de opslaggrootte constant blijft en niet verandert en het hier weglaat om het voorbeeld eenvoudiger te houden. De ingerichte doorvoer gedurende de maand varieerde als volgt (uitgaande van 30 dagen of 720 uur): 

[0-100 uur]:  

* We hebben een Azure Cosmos-account met drie regio's (West US, Oost-VS, Noord-Europa), waar alle regio's beschrijfbaar zijn 

* We hebben een database (D1) gemaakt met gedeelde doorvoer 10K RU/sec 

* We hebben een database (D2) gemaakt met gedeelde doorvoer 30-K RU/sec en  

* We creëerden een container (C1) met speciale doorvoer 20 K RU/sec 

[101-200 uur]:  

* We hebben de database (D1) opgeschaald naar 50 K RU/sec 

* We hebben de database (D2) opgeschaald naar 70 K RU/sec  

* We hebben de container verwijderd (C1)  

[201-300 uur]:  

* We hebben container (C1) opnieuw gemaakt met speciale doorvoer 20 K RU/sec 

[301-400 uur]:  

* We hebben een van de regio's uit azure cosmos-account verwijderd (# van de beschrijfbare regio's is nu 2) 

* We hebben de database (D1) verkleind tot 10K RU/sec 

* We hebben de database (D2) opgeschaald naar 80 K RU/sec  

* We hebben container (C1) weer verwijderd 

[401-500 uur]:  

* We hebben de database (D2) verkleind tot 10K RU/sec  

* We hebben container (C1) opnieuw gemaakt met speciale doorvoer 20 K RU/sec 

[501-700 uur]:  

* We hebben de database (D1) opgeschaald naar 20 K RU/sec  

* We hebben de database (D2) opgeschaald naar 100 K RU/sec  

* We hebben container (C1) weer verwijderd  

[701-720 uur]:  

* We hebben de database (D2) verkleind tot 50 K RU/sec  

Visueel worden de wijzigingen in de totale ingerichte doorvoer gedurende 720 uur voor de maand weergegeven in de onderstaande figuur: 

![Voorbeeld van het echte leven](./media/understand-your-bill/bill-example3.png)

De totale maandelijkse factuur zal worden (uitgaande van 30 dagen/720 uur in een maand) zal worden berekend als volgt:

|**Uur**  |**RU/s** |**Item** |**Gebruik (per uur)** |**Kosten** |
|---------|---------|---------|-------|-------|
|[0-100] |D1:10K <br/>D2:30K <br/>C1:20K |Doorvoerfactuur voor container in West-VS (alle regio's zijn beschrijfbaar)  | `D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 30 K RU/sec/100 * $0.016 * 100 hours = $480` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$960  |
| | |Doorvoerrekening voor 2 extra regio's: Oost-VS, Noord-Europa (alle regio's zijn beschrijfbaar)  |`(2 + 1) * (60 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2.880  |
|[101-200] |D1:50K <br/>D2:70K <br/>C1: -- |Doorvoerfactuur voor container in West-VS (alle regio's zijn beschrijfbaar)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` |$ 1920  |
| | |Doorvoerrekening voor 2 extra regio's: Oost-VS, Noord-Europa (alle regio's zijn beschrijfbaar)  |`(2 + 1) * (120 K RU/sec /100 * $0.016) * 100 hours = $5,760`  |$ 5.760  |
|[201-300]  |D1:50K <br/>D2:70K <br/>C1:20K |Doorvoerfactuur voor container in West-VS (alle regio's zijn beschrijfbaar)  |`D1: 50 K RU/sec/100 * $0.016 * 100 hours = $800` <br/>`D2: 70 K RU/sec/100 * $0.016 * 100 hours = $1,120` <br/>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$2.240  |
| | |Doorvoerrekening voor 2 extra regio's: Oost-VS, Noord-Europa (alle regio's zijn beschrijfbaar)  |`(2 + 1) * (140 K RU/sec /100 * $0.016-) * 100 hours = $6,720` |$6.720 |
|[301-400] |D1:10K <br/>D2:80K <br/>C1: -- |Doorvoerfactuur voor container in West-VS (alle regio's zijn beschrijfbaar)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br/>`D2: 80 K RU/sec/100 * $0.016 * 100 hours = $1,280`  |$ 1.440   |
| | |Doorvoerrekening voor 2 extra regio's: Oost-VS, Noord-Europa (alle regio's zijn beschrijfbaar)  |`(1 + 1) * (90 K RU/sec /100 * $0.016) * 100 hours = $2,880`  |$2.880  |
|[401-500] |D1:10K <br>D2:10K <br>C1:20K |Doorvoerfactuur voor container in West-VS (alle regio's zijn beschrijfbaar)  |`D1: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`D2: 10K RU/sec/100 * $0.016 * 100 hours = $160` <br>`C1: 20 K RU/sec/100 *$0.016 * 100 hours = $320` |$640  |
| | |Doorvoerrekening voor 2 extra regio's: Oost-VS, Noord-Europa (alle regio's zijn beschrijfbaar)  |`(1 + 1) * (40 K RU/sec /100 * $0.016) * 100 hours = $1,280`  |$ 1.280  |
|[501-700] |D1:20K <br>D2:100K <br>C1: -- |Doorvoerfactuur voor container in West-VS (alle regio's zijn beschrijfbaar)  |`D1: 20 K RU/sec/100 * $0.016 * 200 hours = $640` <br>`D2: 100 K RU/sec/100 * $0.016 * 200 hours = $3,200` |$3.840  |
| | |Doorvoerrekening voor 2 extra regio's: Oost-VS, Noord-Europa (alle regio's zijn beschrijfbaar)  |`(1 + 1) * (120 K RU/sec /100 * $0.016) * 200 hours = $1,280`  |7.680 dollar  |
|[701-720] |D1:20K <br/>D2:50K <br/>C1: -- |Doorvoerfactuur voor container in West-VS (alle regio's zijn beschrijfbaar)  |`D1: 20 K RU/sec/100 *$0.016 * 20 hours = $64` <br/>`D2: 50 K RU/sec/100 *$0.016 * 20 hours = $160` |$ 224  |
| | |Doorvoerrekening voor 2 extra regio's: Oost-VS, Noord-Europa (alle regio's zijn beschrijfbaar)  |`(1 + 1) * (70 K RU/sec /100 * $0.016) * 20 hours = $448`  |$ 224  |
|| |**Totale maandelijkse kosten**  | |**$ 38.688**   |

## <a name="billing-examples-with-free-tier-accounts"></a>Voorbeelden van facturering met gratis laagaccounts
Met de gratis laag Azure Cosmos DB krijgt u de eerste 400 RU/s en 5 GB opslagruimte gratis in uw account, toegepast op accountniveau. Alle RU/s en opslag boven de 400 RU/s en 5 GB worden gefactureerd tegen de normale prijstarieven per prijspagina. Op de factuur, ziet u niet een heffing of lijn item voor de gratis 400 Ru / s en 5 GB, alleen de RU / s en opslag dan wat wordt gedekt door gratis laag. De 400 RU/s is van toepassing op elk type RU/s - ingerichte doorvoer, automatische piloot (preview) en multi-master.  

### <a name="billing-example---container-or-database-with-provisioned-throughput"></a>Factureringsvoorbeeld - container of database met ingerichte doorvoer
- Stel dat we een database of container maken in een gratis laagaccount met 400 RU/s en 5 GB opslag.
- Op uw factuur worden geen kosten in rekening gebracht voor deze bron. Uw uur- en maandelijkse kosten zijn $ 0.
- Nu, laten we veronderstellen in hetzelfde account, voegen we een andere database of container met 1000 RU / s en 10 GB opslag.
- Op uw factuur worden nu kosten in rekening gebracht voor de 1000 RU/s en 10 GB opslagruimte. 

### <a name="billing-example---container-or-database-with-autopilot-throughput-preview"></a>Factureringsvoorbeeld - container of database met automatische pilootdoorvoer (voorbeeld)
- Laten we aannemen dat in een gratis tier-account, maken we een database of container met automatische piloot ingeschakeld, met een maximum RU / s van 4000 RU / s. Deze resource wordt automatisch geschaald tussen 400 RU/s - 4000 RU/s. 
- Stel dat in uur 1 tot en met uur 10 de resource minimaal 400 RU/s bedraagt. Tijdens uur 11 schaalt de resource op tot 1000 RU/s en vervolgens binnen het uur terug naar 400 RU/s.
- In uren 1 tot en met 10 wordt $ 0 in rekening gebracht voor doorvoer, omdat de 400 RU/s onder de gratis laag vielen. 
- In uur 11 wordt u gefactureerd voor een effectieve 1000 RU/s - 400 RU/s = 600 RU/s, omdat dit de hoogste RU/s per uur is. Dit zal 6 eenheden van 100 RU / s voor het uur, dus de totale doorvoer kosten voor het uur zal 6 eenheden * $ 0,012 = $ 0,072. 
- Elke opslag buiten de eerste 5 GB wordt gefactureerd tegen normale opslagsnelheden. 

### <a name="billing-example---multi-region-single-write-region-account"></a>Voorbeeld van facturering - account met meerdere regio's, één schrijfregio
- Laten we aannemen dat we in een gratis laagaccount een database of container maken met 1200 RU/s en 10 GB opslag. We repliceren het account naar 3 regio's en we hebben een account met één master (één schrijfregio).
- In totaal, zonder gratis laag, zouden we worden gefactureerd voor 3 * 1200 RU / s = 3600 RU / s en 3 * 10 GB = 30 GB opslag.
- Met de gratis laagkorting, na het verwijderen van 400 RU/s en 5 GB opslag, worden we gefactureerd voor een effectieve 3200 RU/s (32 eenheden) van de ingerichte doorvoer tegen de single write regio snelheid en 25 GB opslag.
- De maandelijkse kosten voor RU /s zou zijn: 32 eenheden * $0.008 * 24 uur * 31 dagen = $190.46. De maandelijkse kosten voor opslag zou zijn: 25 GB * 0,25 / GB = $ 6,25. De totale kosten zouden $190.46 + $6.25 = $196.71 zijn.
- Opmerking: als de eenheidsprijs voor RU/s of opslag verschilt in de regio's, geven de gratis tier 400 RU/s en 5 GB de tarieven weer van de regio waarin het account is gemaakt.

### <a name="billing-example---multi-region-multi-master-multiple-write-region-account"></a>Voorbeeld van facturering - multi-region, multi-master (multiple write region) account

Dit voorbeeld weerspiegelt [multi-master prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/) voor accounts die na 1 december 2019 zijn gemaakt. 
- Laten we aannemen dat we in een gratis laagaccount een database of container maken met 1200 RU/s en 10 GB opslag. We repliceren het account naar 3 regio's en we hebben een multi-master (multiple write region) account. 
- In totaal, zonder gratis laag, zouden we worden gefactureerd voor 3 * 1200 RU / s = 3600 RU / s en 3 * 10 GB = 30 GB opslag.
- Met de gratis laagkorting, na het verwijderen van 400 RU/s en 5 GB opslag, worden we gefactureerd voor een effectieve 3200 RU/s (32 eenheden) van de ingerichte doorvoer tegen de meervoudige schrijfregiosnelheid en 25 GB opslag.
- De maandelijkse kosten voor RU /s zou zijn: 32 eenheden * $0.016 * 24 uur * 31 dagen = $380.93. De maandelijkse kosten voor opslag zou zijn: 25 GB * 0,25 / GB = $ 6,25. De totale kosten zouden $380.93 + $6.25 = $387.18 zijn.

## <a name="proactively-estimating-your-monthly-bill"></a>Proactief uw maandelijkse factuur inschatten  

Laten we eenander voorbeeld overwegen, waarbij u uw factuur proactief wilt inschatten voor het einde van de maand. U uw factuur als volgt schatten:

|**Opslagkosten** | |
|----|----|
|AVG-recordgrootte (KB) |1 |
|Aantal records  |100,000,000  |
|Totale opslag (GB)  |100 |
|Maandelijkse kosten per GB  |$ 0,25  |
|Verwachte maandelijkse kosten voor opslag   |$ 25,00  |

<br>

|**Doorvoerkosten** | | | |
|----|----|----|----|
|Type bewerking| Aanvragen/sec| Avg. RU/verzoek| RU's nodig|
|Schrijven| 100 | 5 | 500|
|Lezen| 400| 1| 400|

Totaal RU/sec: 500 + 400 = 900 uurkosten: 900/100 * $0,008 = $0,072 Verwachte maandelijkse kosten voor doorvoer (uitgaande van 31 dagen): $0.072 * 24 * 31 = $53.57

**Totale maandelijkse kosten**

Totale maandelijkse kosten = maandelijkse kosten voor opslag + maandelijkse kosten voor doorvoer Totale maandelijkse kosten = $ 25,00 + $ 53,57 = $ 78,57

*Prijzen kunnen per regio verschillen. Zie de [prijspagina](https://azure.microsoft.com/pricing/details/cosmos-db/)voor actuele prijzen.*

## <a name="billing-with-azure-cosmos-db-reserved-capacity"></a>Facturering met gereserveerde capaciteit Azure Cosmos DB

Met de gereserveerde capaciteit van Azure Cosmos DB u vooraf ingerichte doorvoer (een gereserveerde capaciteit of een reservering) aanschaffen die kan worden toegepast op alle Azure Cosmos-databases en -containers (voor elk API- of gegevensmodel) in alle Azure-regio's. Omdat de ingerichte doorvoerprijs per regio varieert, helpt het om gereserveerde capaciteit te zien als een monetair krediet dat u met korting hebt gekocht, dat kan worden afgeleid uit de ingerichte doorvoer tegen de respectieve prijs in elke regio. Stel dat u een Azure Cosmos-account hebt met één container die is ingericht met 50 K RU/sec en twee regio's wereldwijd hebt gerepliceerd: Oost-VS en Japan-Oost. Als u kiest voor de pay-as-you-go-optie, betaalt u het gewenste aantal:  

* in Oost-VS: voor 50-K RU/sec tegen het tarief van $0.008 per 100 RU/sec in die regio 

* in Japan East: voor 50-K RU/ sec tegen het tarief van $ 0,009 per 100 RU / sec in die regio

Uw totale factuur (zonder gereserveerde capaciteit) zou zijn (uitgaande van 30 dagen of 720 uur): 

|**Regio**| **Uurprijs per 100 RU/s**|**Eenheden (RU/s)**|**Gefactureerd bedrag (per uur)**| **Gefactureerd bedrag (maandelijks)**|
|----|----|----|----|----|
|VS - oost|$ 0,008 |50 K|$ 4|$2.880 |
|Japan - oost|$ 0,009 |50 K| $ 4,50 |$3.240 |
|Totaal|||$ 8,50|$6.120 |

Laten we eens kijken of u in plaats daarvan gereserveerde capaciteit hebt gekocht. U gereserveerde capaciteit kopen voor 100-K RU/sec tegen de prijs van $ 56.064 voor een jaar (tegen 20% korting), of $ 6,40 per uur. Zie gereserveerde capaciteitsprijzen op de [prijspagina](https://azure.microsoft.com/pricing/details/cosmos-db/)).  

* Kosten van doorvoer (pay-as-you-go): 100.000 RU/sec/100 * $0.008/hour * 8760 uur in een jaar = $70.080 

* Kosten van doorvoer (met gereserveerde capaciteit) $ 70.080 verdisconteerd op 20% = $ 56.064 

Wat je effectief hebt gekocht is een tegoed van $ 8 per uur, voor 100 K RU / sec met behulp van de catalogusprijs in Oost-VS, tegen de prijs van $ 6,40 per uur. U vervolgens op uurbasis deze vooraf betaalde doorvoerreservering opnemen voor de ingerichte doorvoercapaciteit in een globaal Azure-gebied tegen de respectieve regionale catalogusprijzen die voor uw abonnement zijn ingesteld. In dit voorbeeld, waar u 50 K RU/sec in Oost-VS en Japan East indient, u $ 8,00 aan ingerichte doorvoer per uur tekenen en wordt de overschrijding van $ 0,50 per uur (of $ 360/maand) in rekening gebracht. 

|**Regio**| **Uurprijs per 100 RU/s**|**Eenheden (RU/s)**| **Gefactureerd bedrag (per uur)**| **Gefactureerd bedrag (maandelijks)**|
|----|----|----|----|----|
|VS - oost|$ 0,008 |50 K|$ 4|$2.880 |
|Japan - oost|$ 0,009 |50 K| $ 4,50 |$3.240 |
|||Pay-as-you-go|$ 8,50|$6120|
|Gereserveerde capaciteit aangeschaft|$ 0,0064 (20% korting) |100 RU/sec of $8 capaciteit vooraf gekocht |-$8|-$5.760 |
|Netto Factuur|||$ 0,50 |$360 |

## <a name="next-steps"></a>Volgende stappen

Vervolgens u meer te weten komen over kostenoptimalisatie in Azure Cosmos DB met de volgende artikelen:

* Meer informatie over [hoe cosmos DB-prijsmodel kosteneffectief is voor klanten](total-cost-ownership.md)
* Meer informatie over [optimaliseren voor ontwikkeling en testen](optimize-dev-test.md)
* Meer informatie over [het optimaliseren van doorvoerkosten](optimize-cost-throughput.md)
* Meer informatie over [het optimaliseren van opslagkosten](optimize-cost-storage.md)
* Meer informatie over [het optimaliseren van de kosten van lezen en schrijven](optimize-cost-reads-writes.md)
* Meer informatie over [het optimaliseren van de kosten van query's](optimize-cost-queries.md)
* Meer informatie over [het optimaliseren van de kosten van Azure Cosmos-accounts met meerdere regio's](optimize-cost-regions.md)
