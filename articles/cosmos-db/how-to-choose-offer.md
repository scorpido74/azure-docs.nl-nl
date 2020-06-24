---
title: De juiste doorvoer aanbieding kiezen in Azure Cosmos DB
description: Meer informatie over het kiezen van een standaard (hand matige) ingerichte door Voer en het automatisch schalen van ingerichte door Voer voor uw werk belasting.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: dech
ms.openlocfilehash: 94022b9959b6a7f2bc30e31f918f2f5a916ccd8c
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/20/2020
ms.locfileid: "85116805"
---
# <a name="how-to-choose-between-standard-manual-and-autoscale-provisioned-throughput"></a>Kiezen tussen standaard (hand matig) en ingerichte door Voer voor automatisch schalen 

Azure Cosmos DB ondersteunt twee typen of aanbiedingen van ingerichte door Voer: standaard (hand matig) en automatisch schalen. Beide doorvoer typen zijn geschikt voor essentiële workloads die hoge prestaties en schaal vereisen, en die worden ondersteund door dezelfde Azure Cosmos DB Sla's voor door Voer, Beschik baarheid, latentie en consistentie.

In dit artikel wordt beschreven hoe u standaard (hand matig) en ingerichte door Voer voor automatisch schalen voor uw werk belasting opgeeft. 

## <a name="overview-of-provisioned-throughput-types"></a>Overzicht van ingerichte doorvoer typen
Voordat u het verschil tussen Standard (hand matig) en automatisch schalen afneemt, is het belang rijk om eerst te begrijpen hoe ingerichte door Voer werkt in Azure Cosmos DB. 

Wanneer u de ingerichte door Voer gebruikt, stelt u de door Voer in, gemeten in aanvraag eenheden per seconde (RU/s) die vereist zijn voor uw werk belasting. De service richt zich op de capaciteit die nodig is om de doorvoer vereisten te ondersteunen. Database bewerkingen voor de service, zoals Lees bewerkingen, schrijf bewerkingen en query's verbruiken een bepaalde hoeveelheid aanvraag eenheden (RUs). Meer informatie over [aanvraag eenheden](request-units.md).

In de volgende tabel ziet u een vergelijking op hoog niveau tussen standaard (hand matig) en automatisch schalen.

|Beschrijving|Standaard (hand matig)|Automatisch schalen|
|-------------|------|-------|
|Geschikt voor|Workloads met constant of voorspelbaar verkeer|Werk belastingen met variabele of onvoorspelbaar verkeer. Zie [use cases of automatisch schalen](provision-throughput-autoscale.md#use-cases-of-autoscale).|
|Hoe werkt het?|U voorziet in een vaste hoeveelheid RU/s `T` die gedurende een bepaalde periode statisch is, tenzij u deze hand matig wijzigt. Elke seconde kunt u de `T` door Voer tot ru/s gebruiken. <br/><br/>Als u bijvoorbeeld standaard (hand matig) 400 RU/s instelt, blijft de door Voer op 400 RU/s.|U stelt het hoogste of maximum aantal RU/s `Tmax` in dat u niet meer wilt voor het systeem. Het systeem schaalt de door Voer `T` zo automatisch `0.1* Tmax <= T <= Tmax` . <br/><br/>Als u bijvoorbeeld automatisch schalen instelt voor het maximum aantal RU/s 4000 RU/s, wordt het systeem geschaald tussen 400-4000 RU/s.|
|Wanneer te gebruiken|U wilt uw doorvoer capaciteit (RU/s) hand matig beheren en zelf schalen.<br/><br/>U hebt een hoog, consistent gebruik van ingerichte RU/s. Als u de ingerichte RU/s hebt ingesteld `T` en het volledige bedrag voor 66% van de uren of meer wilt gebruiken, kunt u het beste besparen met standaard (hand matig) ingerichte ru/s.<br/><br/>Dit is gebaseerd op een vergelijking tussen instelling `T` in standaard (hand matig) en hetzelfde bedrag `Tmax` in automatisch schalen. |U wilt dat Azure Cosmos DB uw doorvoer capaciteit (RU/s) en schalen op basis van gebruik beheert.<br/><br/>U hebt RU/s-gebruik dat variabel of moeilijk te voors pellen is. Als u het maximum aantal RU/s hebt ingesteld voor automatisch schalen `Tmax` en het volledige bedrag `Tmax` voor 66% van de uren gebruikt, kunt u het beste besparen met automatisch schalen.<br/><br/>Dit is gebaseerd op een vergelijking tussen het instellen van automatisch schalen `Tmax` en dezelfde hoeveelheid `T` in de standaard doorvoer (hand matig).|
|Factureringsmodel|Facturering geschiedt per uur voor de ingerichte RU/s, ongeacht het aantal verbruikte i/o's.<br/><br/>Voorbeeld: <li>400 RU/s inrichten</li><li>Uur 1: geen aanvragen</li><li>Aantal uur 2:400 RU/s aanvragen</li><br/><br/>Voor zowel uur 1 als 2 wordt 400 RU/s gefactureerd voor beide uren met de [standaard tarieven (hand matig)](https://azure.microsoft.com/pricing/details/cosmos-db/).|Facturering geschiedt per uur voor de hoogste RU/s waarmee het systeem in het uur is geschaald. <br/><br/>Voorbeeld: <li>Automatisch schalen inrichten Max. RU/s van 4000 RU/s (schalen tussen 400-4000 RU/s)</li><li>Uur 1: systeem geschaald tot hoogste waarde van 3500 RU/s</li><li>Uur 2: systeem omlaag geschaald naar mini maal 400 RU/s (altijd 10% van `Tmax` ), omdat er geen gebruik is</li><br/><br/>Er worden kosten in rekening gebracht voor 3500 RU/s in uur 1 en 400 RU/s in uur 2 bij de [ingerichte doorvoer tarieven voor automatisch schalen](https://azure.microsoft.com/pricing/details/cosmos-db/). Het automatisch schalen per RU/s is 1,5 * de standaard tarieven (hand matig).
|Wat gebeurt er als u de ingerichte RU/s overschrijdt|De RU/s blijven statisch bij wat is ingericht. Alle aanvragen die in een seconde buiten het ingerichte RUs worden gebruikt, zijn beperkt tot een bepaald aantal, met een reactie die een tijd moet wachten voordat het opnieuw wordt geprobeerd. U kunt de RU/s hand matig Verg Roten of verkleinen als dat nodig is.| Het systeem schaalt de RU/s tot het maximum aantal RU/s voor automatisch schalen. Aanvragen die buiten het automatisch schalen worden gebruikt, zijn in een tweede beperkt tot een bepaald aantal, met een reactie die een wacht tijd moet wachten voordat het opnieuw wordt geprobeerd.|

## <a name="understand-your-traffic-patterns"></a>Inzicht in uw verkeers patronen

### <a name="new-applications"></a>Nieuwe toepassingen ###
Als u een nieuwe toepassing bouwt en uw verkeers patroon nog niet kent, kunt u beginnen bij het ingangs punt RU/s (of de mini maal RU/s) om te voor komen dat de inrichting in het begin begint. Als u een kleine toepassing hebt waarvoor geen hoge schaal nodig is, kunt u het beste alleen het minimale invoer punt RU/s inrichten om de kosten te optimaliseren. In beide gevallen zijn standaard (hand matig) of automatisch schalen geschikt. U moet rekening houden met het volgende:

Als u standaard (hand matig) RU/s inricht op het ingangs punt van 400 RU/s, kunt u niet meer dan 400 RU/s gebruiken, tenzij u de door Voer hand matig wijzigt. Er worden kosten in rekening gebracht voor 400 RU/s bij de standaard (hand matig) ingerichte doorvoer snelheid per uur.

Als u de door Voer voor automatisch schalen inricht op het ingangs punt van Max. RU/s van 4000 RU/s, wordt de resource geschaald van 400 naar 4000 RU/s. Omdat het facturerings tarief voor automatisch schalen per RU/s 1,5 x van het standaard tarief (hand matig) is, gelden voor uren waarbij het systeem is geschaald naar het minimum van 400 RU/s, uw factuur hoger dan wanneer u 400 RU/s hand matig hebt ingericht. Met automatisch schalen kunt u echter op elk gewenst moment, als uw toepassings verkeer piekt, Maxi maal 4000 RU/s gebruiken zonder dat de gebruiker actie hoeft te ondernemen. Over het algemeen moet u het voor deel afwegen om Maxi maal het maximum aantal RU/s te kunnen gebruiken op elk gewenst moment met de 1,5 x-snelheid voor automatisch schalen.

Gebruik de Azure Cosmos DB [capacity Calculator](estimate-ru-with-capacity-planner.md) om uw doorvoer vereisten te ramen. 

### <a name="existing-applications"></a>Bestaande toepassingen ###

Als u een bestaande toepassing hebt die gebruikmaakt van Standard (hand matig) ingerichte door Voer, kunt u [Azure monitor metrische gegevens](../azure-monitor/insights/cosmosdb-insights-overview.md) gebruiken om te bepalen of uw verkeers patroon geschikt is voor automatisch schalen. 

Zoek eerst de metrische gegevens over het verbruik van de [genormaliseerde aanvraag eenheid](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric) van uw data base of container. Genormaliseerd gebruik is een meting van de hoeveelheid die momenteel gebruikmaakt van de ingerichte door Voer van Standard (hand matig). Hoe dichter het getal is tot 100%, hoe meer u volledig gebruikt voor uw ingerichte RU/s. Meer [informatie](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric) over de metrische gegevens.

Bepaal vervolgens hoe het genormaliseerde gebruik in de loop van de tijd verschilt. Als u ziet dat het genormaliseerde gebruik variabel of onvoorspelbaar is, kunt u de functie voor automatisch schalen inschakelen voor uw data base of container. Als het daarentegen stabiel en voorspelbaar is, kunt u het beste overblijven op standaard (hand matig) ingerichte door voer. 

> [!TIP]
> Met de standaard-door Voer (hand matig) kunt u de genormaliseerde metrische gegevens over gebruik gebruiken om een schatting te maken van de werkelijke RU/s die u kunt gebruiken als u overschakelt naar automatisch schalen. Het genormaliseerde gebruik op een bepaald moment vermenigvuldigen met de momenteel ingerichte (hand matig) RU/s. Als u bijvoorbeeld 5000 RU/s hebt ingericht en het genormaliseerde gebruik 90% is, is het gebruik van de RU/s 0,9 * 5000 = 4500 RU/s. Als u ziet dat het verkeers patroon variabel is, maar u zich boven of onder de inrichting bevindt, kunt u automatisch schalen inschakelen en de instelling maximum aantal RU/s voor automatisch schalen dienovereenkomstig wijzigen.

## <a name="measure-and-monitor-your-usage"></a>Uw gebruik meten en bewaken
Nadat u het type door Voer hebt gekozen, moet u de toepassing na verloop van tijd controleren en zo nodig wijzigingen aanbrengen. 

Wanneer u automatisch schalen gebruikt, gebruikt u Azure Monitor om het ingerichte maximum aantal RU/s voor automatische schaal baarheid te bekijken (**maximale door Voer voor automatisch schalen**) en de ru/s waarmee het systeem op dit moment is geschaald (**ingerichte door Voer**). Hieronder ziet u een voor beeld van een variabele of onvoorspelbare werk belasting met automatisch schalen. Opmerking: wanneer er geen verkeer is, wordt de RU/s door het systeem geschaald naar het minimum van 10% van het maximum aantal RU/s, in dit geval 5000 RU/s en 50.000 RU/s. 

:::image type="content" source="media/how-to-choose-offer/autoscale-metrics-azure-monitor.png" alt-text="Voor beeld van een workload met automatisch schalen":::

> [!NOTE]
> Wanneer u standaard (hand matig) ingerichte door Voer gebruikt, verwijst de **ingerichte doorvoer** metriek naar wat u als gebruiker hebt ingesteld. Wanneer u de door Voer van automatisch schalen gebruikt, verwijst deze waarde naar de RU/s waarop het systeem momenteel is geschaald.

## <a name="next-steps"></a>Volgende stappen
* Gebruik [ru Calculator](https://cosmos.azure.com/capacitycalculator/) om de door Voer voor nieuwe workloads te schatten.
* Gebruik [Azure monitor](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) om uw bestaande workloads te bewaken.
* Meer informatie over het [inrichten van de door Voer voor automatisch schalen in een Azure Cosmos-data base of-container](how-to-provision-autoscale-throughput.md).
* Bekijk de [Veelgestelde vragen over automatisch schalen](autoscale-faq.md).