---
title: Azure Cosmos-containers en-data bases maken in de modus voor automatisch schalen.
description: Meer informatie over de voor delen, use cases en het inrichten van Azure Cosmos-data bases en-containers in de modus voor automatisch schalen.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2020
ms.openlocfilehash: 20b0bcfe5043d4767199c36796fa1123ed779363
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/15/2020
ms.locfileid: "84791143"
---
# <a name="create-azure-cosmos-containers-and-databases-with-autoscale-throughput"></a>Azure Cosmos-containers en-data bases maken met de door Voer van automatisch schalen

Met Azure Cosmos DB kunt u standaard (hand matig) of ingerichte door Voer voor automatisch schalen instellen voor uw data bases en containers. In dit artikel worden de voor delen en het gebruik beschreven van de door Voer ingericht voor automatisch schalen. 

Het automatisch schalen van de ingerichte door Voer is goed geschikt voor bedrijfskritische workloads met variabele of onvoorspelbare verkeers patronen, en voor het vereisen van Sla's voor hoge prestaties en schaal baarheid. 

Azure Cosmos DB automatisch schalen, worden **de door Voer (ru/s)** van uw data base of container op basis van het gebruik direct geschaald, zonder dat dit van invloed is op de beschik baarheid, latentie, door Voer of de prestaties van de werk belasting. 

## <a name="benefits-of-autoscale"></a>Voordelen van automatisch schalen

Azure Cosmos-data bases en containers die zijn geconfigureerd met automatisch schalen ingerichte door Voer, hebben de volgende voor delen:

* **Eenvoudig:** Automatisch schalen verwijdert de complexiteit van het beheer van RU/s met aangepaste scripting of hand matig schalen van capaciteit. 

* **Schaalbaar:** Data bases en containers schalen automatisch de ingerichte door Voer als dat nodig is. Er zijn geen onderbrekingen voor client verbindingen, toepassingen of gevolgen voor Azure Cosmos DB Sla's.

* **Rendabel:** Automatisch schalen helpt u bij het optimaliseren van het gebruik van RU/s en het gebruik van kosten door te schalen wanneer het niet wordt gebruikt. U betaalt alleen voor de resources die uw werk belasting per uur nodig heeft. Als u alle uren in een maand hebt ingesteld, kunt u de maximale grootte van RU/s (tmax) instellen en het volledige tmax voor 66% van de uren of minder gebruiken. Zie voor meer informatie het artikel [Provisioning kiezen tussen standaard (hand matig) en ingericht door Voer voor automatisch schalen](how-to-choose-offer.md) .

* **Maxi maal beschikbaar:** Data bases en containers die gebruikmaken van automatisch schalen, gebruiken dezelfde wereld wijd gedistribueerde, fout tolerante, Maxi maal beschik bare Azure Cosmos DB back-end om gegevens duurzaamheid en hoge Beschik baarheid te garanderen

## <a name="use-cases-of-autoscale"></a>Use-cases van automatisch schalen

De use-cases van automatisch schalen zijn onder andere:

* **Variabele of onvoorspelbare workloads:** Wanneer uw werk belastingen variabele of onvoorspelbare pieken in het gebruik hebben, helpt automatisch schalen door automatische schaling op basis van het gebruik. Voor beelden zijn handels websites met verschillende verkeers patronen, afhankelijk van de seizoensgebondenheid; IOT-workloads die op verschillende tijdstippen gedurende de dag pieken hebben. line-of-business-toepassingen die het piek gebruik enkele keren per maand of jaar weer geven. Met automatisch schalen hoeft u niet meer hand matig in te richten op de piek of de gemiddelde capaciteit. 

* **Nieuwe toepassingen:** Als u een nieuwe toepassing ontwikkelt en niet zeker weet over de door Voer (RU/s) die u nodig hebt, kunt u met automatisch schalen gemakkelijk aan de slag gaan. U kunt beginnen met het toegangs punt voor automatisch schalen van 400-4000 RU/s, uw gebruik controleren en de juiste RU/s na verloop van tijd vaststellen.

* **Zelden gebruikte toepassingen:** Als u een toepassing hebt die slechts enkele uren per dag, week of maand wordt gebruikt, zoals een toepassing per laag volume/web/blog-site, past automatisch schalen de capaciteit aan voor het afhandelen van piek gebruik en wordt geschaald wanneer deze zich voordoet. 

* **Werk belastingen voor ontwikkelen en testen:** Als u of uw team Azure Cosmos-data bases en containers tijdens werk uren gebruikt, maar u deze niet nodig hebt om over te slaan of weekenden, bespaart u met automatisch schalen kosten door omhoog te schalen naar een minimum wanneer het niet in gebruik is. 

* **Geplande werk belastingen/query's voor productie:** Als u een reeks geplande aanvragen, bewerkingen of query's hebt die u wilt uitvoeren tijdens inactieve Peri Oden, kunt u dit eenvoudig doen met automatisch schalen. Wanneer u de werk belasting moet uitvoeren, wordt de door Voer automatisch geschaald naar wat er nodig is, waarna u omlaag kunt schalen. 

Het bouwen van een aangepaste oplossing voor deze problemen vergt niet alleen een enorme hoeveelheid tijd, maar introduceert ook de complexiteit van de configuratie of code van uw toepassing. Met automatisch schalen kunt u de bovenstaande scenario's uit het vak maken en de behoefte aan aangepaste of hand matige schaling van capaciteit verwijderen. 

## <a name="how-autoscale-provisioned-throughput-works"></a>Hoe automatisch schalen ingerichte door Voer werkt

Wanneer u containers en data bases met automatisch schalen configureert, geeft u de maximale door Voer `Tmax` vereist op. Azure Cosmos DB de door Voer `T` zodanig schalen `0.1*Tmax <= T <= Tmax` . Als u bijvoorbeeld de maximale door Voer instelt op 20.000 RU/s, wordt de door Voer geschaald van 2000 naar 20.000 RU/s. Omdat schalen automatisch en direct kan worden gebruikt, kunt u op elk gewenst moment tot aan het inrichten zonder `Tmax` vertraging. 

Elk uur wordt u gefactureerd voor de hoogste door Voer `T` die het systeem binnen het uur is geschaald.

Het ingangs punt voor automatisch schalen maximale door Voer `Tmax` wordt gestart om 4000 ru/s, die tussen 400-4000 ru/s. U kunt `Tmax` in stappen van 1000 ru/s instellen en de waarde op elk gewenst moment wijzigen.  

## <a name="enable-autoscale-on-existing-resources"></a>Automatisch schalen op bestaande resources inschakelen

Gebruik de [Azure Portal](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container) om automatisch schalen in te scha kelen op een bestaande data base of container. U kunt op elk gewenst moment scha kelen tussen de ingerichte door Voer van automatisch schalen en standaard (hand matig). Raadpleeg deze [documentatie](autoscale-faq.md#how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work) voor meer informatie. Voor alle Api's kunt u op dit moment alleen de Azure Portal gebruiken om automatisch schalen in te scha kelen op bestaande resources.

## <a name="throughput-and-storage-limits-for-autoscale"></a><a id="autoscale-limits"></a>Doorvoer-en opslag limieten voor automatisch schalen

Voor elke waarde van `Tmax` kan de data base of container een totaal van bevatten `0.01 * Tmax GB` . Nadat deze hoeveelheid opslag is bereikt, wordt het maximum aantal RU/s automatisch verhoogd op basis van de nieuwe opslag waarde, zonder dat dit van invloed is op uw toepassing. 

Als u bijvoorbeeld begint met een maximum van RU/s 50.000 RU/s (schalen tussen 5000-50.000 RU/s), kunt u Maxi maal 500 GB aan gegevens opslaan. Als u meer dan 500 GB hebt, bijvoorbeeld opslag is nu 600 GB, zijn de nieuwe maximum-RU/s 60.000 RU/s (schaalbaar tussen 6000-60.000 RU/s).

Wanneer u door Voer op database niveau met automatisch schalen gebruikt, kunt u de eerste 25 containers een automatisch schaalbaar maximum RU/s van 4000 (schalen tussen 400-4000 RU/s) delen, op voor waarde dat u niet meer dan 40 GB opslag ruimte overschrijdt. Raadpleeg deze [documentatie](autoscale-faq.md#can-i-change-the-max-rus-on-the-database-or-container) voor meer informatie.

## <a name="comparison--containers-configured-with-manual-vs-autoscale-throughput"></a>Vergelijking: containers die zijn geconfigureerd met hand matig en automatisch schalen door Voer
Raadpleeg deze [documentatie](how-to-choose-offer.md) voor meer informatie over het kiezen van standaard (hand matig) en door Voer automatisch schalen.  

|| Containers met standaard (hand matig) door Voer  | Containers met automatisch schalen door Voer |
|---------|---------|---------|
| **Ingerichte door Voer (RU/s)** | Hand matig ingericht. | Automatisch en onmiddellijk geschaald op basis van de gebruiks patronen van de werk belasting. |
| **Frequentie waarmee aanvragen/bewerkingen worden beperkt (429)**  | Dit kan gebeuren als het verbruik de ingerichte capaciteit overschrijdt. | Treedt niet op als u RU/s gebruikt binnen het bereik voor automatisch schalen dat u hebt ingesteld.    |
| **Capaciteitsplanning** |  U moet rekening houden met capaciteits planning en de exacte door Voer die u nodig hebt inrichten. |    Het systeem zorgt automatisch voor capaciteits planning en capaciteits beheer. |
| **Prijzen** | U betaalt voor de hand matig ingerichte RU/s per uur met het [tarief standaard (hand matig) ru/s per uur](https://azure.microsoft.com/pricing/details/cosmos-db/). | U betaalt per uur voor de hoogste RU/s waarbij het systeem binnen het uur omhoog is geschaald. <br/><br/> Voor accounts met enkele schrijf regio's betaalt u de RU/s per uur, met behulp van het tarief voor [automatisch schalen van ru/SEC](https://azure.microsoft.com/pricing/details/cosmos-db/). <br/><br/>Voor accounts met meerdere schrijf regio's zijn er geen extra kosten voor automatisch schalen. U betaalt voor de door Voer die op elk uur wordt gebruikt met hetzelfde tarief van één [of meerdere masters per uur](https://azure.microsoft.com/pricing/details/cosmos-db/). |
| **Geschikt voor typen werk belastingen** |  Voorspel bare en stabiele workloads|   Onvoorspelbare en variabele workloads  |

## <a name="next-steps"></a>Volgende stappen

* Bekijk de [Veelgestelde vragen over automatisch schalen](autoscale-faq.md).
* Meer informatie over hoe u [kunt kiezen tussen hand matige door Voer en automatisch schalen](how-to-choose-offer.md).
* Meer informatie over het [inrichten van de door Voer voor automatisch schalen in een Azure Cosmos-data base of-container](how-to-provision-autoscale-throughput.md).
* Meer informatie over [partitioneren](partition-data.md) in azure Cosmos db.


