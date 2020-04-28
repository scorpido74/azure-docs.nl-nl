---
title: Veelgestelde vragen over de modus voor automatisch schalen in Azure Cosmos DB
description: Veelgestelde vragen over het automatisch schalen van ingerichte door Voer voor Azure Cosmos DB data bases en containers
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 413e9c71850b047172859c681cdbb422b7def032
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196463"
---
# <a name="frequently-asked-questions-about-autoscale-provisioned-throughput-in-azure-cosmos-db"></a>Veelgestelde vragen over het automatisch schalen van ingerichte door Voer in Azure Cosmos DB

Met de ingerichte door Voer in de modus voor automatisch schalen, beheert en schaalt Azure Cosmos DB de RU/s van uw container of Data Base op basis van het gebruik. In dit artikel vindt u antwoorden op veelgestelde vragen over automatisch schalen.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="is-autoscale-mode-supported-for-all-apis"></a>Wordt de modus voor automatisch schalen ondersteund voor alle Api's?
Ja, de modus voor automatisch schalen wordt ondersteund voor alle Api's: core (SQL), Gremlin, Table, Cassandra en API voor MongoDB.

### <a name="is-autoscale-mode-supported-for-multi-master-accounts"></a>Wordt de modus voor automatisch schalen ondersteund voor Multi-Master accounts?
Ja, de modus voor automatisch schalen wordt ondersteund voor Multi-Master-accounts. Het maximum aantal RU/s is beschikbaar in elke regio die wordt toegevoegd aan het Cosmos-account. 

### <a name="what-is-the-pricing-for-autoscale"></a>Wat zijn de prijzen voor automatisch schalen?
Raadpleeg de pagina met [prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/) voor Azure Cosmos DB voor meer informatie. 

### <a name="how-do-i-enable-autoscale-for-my-containers-or-databases"></a>Hoe kan ik automatisch schalen inschakelen voor mijn containers of data bases?
De modus voor automatisch schalen kan worden ingeschakeld voor nieuwe containers en data bases die zijn gemaakt met behulp van de Azure Portal. 

### <a name="is-there-cli-or-sdk-support-to-create-containers-or-databases-with-autoscale-mode"></a>Is er CLI-of SDK-ondersteuning voor het maken van containers of data bases met de modus voor automatisch schalen?
Op dit moment kunt u alleen resources maken met de modus voor automatisch schalen vanuit het Azure Portal. Ondersteuning voor CLI en SDK is nog niet beschikbaar.

### <a name="can-i-enable-autoscale-on-an-existing-container-or-a-database"></a>Kan ik automatisch schalen inschakelen voor een bestaande container of een Data Base?
Op dit moment kunt u automatisch schalen inschakelen voor nieuwe containers en data bases bij het maken van deze. Ondersteuning voor het inschakelen van de modus voor automatisch schalen op bestaande containers en data bases is nog niet beschikbaar. U kunt bestaande containers naar een nieuwe container migreren met behulp van [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) of een [andere feed](change-feed.md). 

### <a name="can-i-turn-off-autoscale-mode-on-a-container-or-database"></a>Kan ik de modus voor automatisch schalen uitschakelen voor een container of data base?
Ja, u kunt automatisch schalen uitschakelen door te scha kelen naar de optie ' hand matig ' voor de ingerichte door voer. Nadat u in de huidige versie hebt overgeschakeld van de modus automatisch schalen naar hand matige modus, kunt u automatisch schalen niet meer inschakelen voor dezelfde resource. 

### <a name="is-autoscale-mode-supported-for-shared-throughput-databases"></a>Wordt de modus voor automatisch schalen ondersteund voor gedeelde doorvoer databases?
Ja, de modus voor automatisch schalen wordt ondersteund voor gedeelde doorvoer databases. Als u deze functie wilt inschakelen, selecteert u modus voor automatisch schalen en de optie **door Voer** bij het maken van de data base. 

### <a name="what-is-the-number-of-allowed-collections-per-shared-throughput-database-when-autoscale-is-enabled"></a>Wat is het aantal toegestane verzamelingen per gedeelde doorvoer database wanneer automatisch schalen is ingeschakeld?
Voor gedeelde doorvoer databases waarvoor de modus voor automatisch schalen is ingeschakeld, is het aantal toegestane verzamelingen: MIN (25, Max. RU/s van data base/1000). Als de maximale door Voer van de data base bijvoorbeeld 20.000 RU/s is, kan de Data Base mini maal (25, 20.000 RU/s/1000) = 20 verzamelingen bevatten. 


### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Wat is de opslag limiet die is gekoppeld aan elke maximum RU/s optie?  
De opslag limiet in GB voor elke maximum versie van RU/s is: Max RU/s van data base of container/100. Als het maximum aantal RU/s 20.000 RU/s is, kan de resource bijvoorbeeld 200 GB aan opslag ondersteunen. Zie het artikel [limieten voor automatisch schalen](provision-throughput-autoscale.md#autoscale-limits) voor de beschik bare Max ru/s en opslag opties. 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>Wat gebeurt er als ik de opslag limiet overschrijdt die is gekoppeld aan mijn maximale door Voer?
Als de opslag limiet die is gekoppeld aan de maximale door Voer van de data base of container wordt overschreden, wordt door Azure Cosmos DB de maximale door Voer automatisch verhoogd naar de volgende hoogste laag die dat opslag niveau kan ondersteunen. Stel bijvoorbeeld dat een Data Base of container is ingericht met de optie maximale door Voer van 4000 RU/s, die een opslag limiet van 50 GB heeft. Als de opslag van de resource toeneemt tot 100 GB, wordt het maximum aantal RU/s van de data base of container automatisch verhoogd tot 20.000 RU/s, die Maxi maal 200 GB kan ondersteunen. 

### <a name="how-quickly-will-autoscale-up-and-down-based-on-spikes-in-traffic"></a>Hoe snel wordt automatisch omhoog en omlaag geschaald op basis van pieken in verkeer?
In de modus voor automatisch schalen kunt u de RU/s in het minimale en maximale RU/s-bereik direct omhoog of omlaag schalen, op basis van binnenkomend verkeer. Facturering geschiedt met een granulatie van 1 uur, waarbij u in rekening wordt gebracht voor de hoogste RU/s in een bepaald uur.

### <a name="can-i-specify-a-custom-max-throughput-rus-value-for-autoscale-mode"></a>Kan ik een aangepaste waarde voor maximale door Voer (RU/s) voor de modus voor automatisch schalen opgeven?
Op dit moment kunt u kiezen uit [vier opties](provision-throughput-autoscale.md#autoscale-limits) voor maximale door Voer (ru/s).

### <a name="can-i-increase-the-max-rus-move-to-a-higher-tier-on-the-database-or-container"></a>Kan ik het maximum aantal RU/s (verplaatsen naar een hogere laag) op de data base of container verhogen? 
Ja. Vanuit de optie **schaal & instellingen** voor uw container of **schaal** optie voor uw data base kunt u een hoger maximum aantal ru/s voor de modus voor automatisch schalen selecteren. Dit is een asynchrone opschalende bewerking die veel tijd in beslag kan nemen (meestal 4-6 uur, afhankelijk van de geselecteerde RU/s), aangezien de service meer bronnen nodig heeft om de hogere schaal te ondersteunen. 

### <a name="can-i-reduce-the-max-rus-move-to-a-lower-tier-on-the-database-or-container"></a>Kan ik het maximum aantal RU/s (verplaatsen naar een lagere laag) op de data base of container verlagen?
Ja. Zolang de huidige opslag van de data base of container zich onder de [opslag limiet](#what-is-the-storage-limit-associated-with-each-max-rus-option) bevindt die is gekoppeld aan de Max ru/s-laag die u omlaag wilt schalen, kunt u het maximum aantal ru/s beperken tot die laag. Als u bijvoorbeeld 20.000 RU/s hebt geselecteerd als maximum aantal RU/s, kunt u het maximum aantal RU/s naar 4000 RU/s schalen als u minder dan 50 GB opslag ruimte hebt (de opslag limiet die is gekoppeld aan 4000 RU/s).

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Wat is de toewijzing tussen de Max RU/s en fysieke partities?
Wanneer u voor het eerst het maximum aantal RU/s selecteert, wordt Azure Cosmos DB ingericht: max. RU/s/10.000 RU/s = aantal fysieke partities. Elke [fysieke partitie](partition-data.md#physical-partitions) kan maxi maal 10.000 ru/s en 50 GB opslag ondersteunen. Wanneer de grootte van de opslag groeit, neemt Azure Cosmos DB automatisch de partities op om meer fysieke partities toe te voegen om de opslag toename te verwerken, of om de maximale RU/s-laag te verg Roten als [de opslag de bijbehorende limiet overschrijdt](#what-is-the-storage-limit-associated-with-each-max-rus-option). 

Het maximum aantal RU/s van de data base of container wordt gelijkmatig verdeeld over alle fysieke partities. De totale door Voer van elke fysieke partitie kan worden geschaald naar is: Max RU/s data base of container/# fysieke partities. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>Wat gebeurt er als inkomende aanvragen het maximum aantal RU/s van de data base of container overschrijden?
Als de totale hoeveelheid opgehaalde RU/s het maximum aantal RU/s van de container of data base overschrijdt, worden aanvragen die groter zijn dan de maximale grootte van RU/s, beperkt en wordt de status code 429 geretourneerd. Aanvragen die resulteren in meer dan 100% genormaliseerd gebruik, worden ook beperkt. Genormaliseerd gebruik wordt gedefinieerd als het maximum van het gebruik van de RU/s voor alle fysieke partities. Stel bijvoorbeeld dat uw maximale door Voer 20.000 RU/s is en dat u twee fysieke partities, P_1 en P_2 hebt, die allemaal kunnen worden geschaald naar 10.000 RU/s. Als P_1 6000 RUs heeft gebruikt in een gegeven tweede, is het genormaliseerde gebruik Maxi maal (6000 RU/10.000 RU, 8000 RU/10.000 RU) = 0,8, als de geP_2 8000.

> [!NOTE]
> De Azure Cosmos DB-hulpprogram ma's voor client-Sdk's en gegevens import (Azure Data Factory, bulk-uitvoerder bibliotheek) maken automatisch opnieuw een nieuwe poging op 429s, zodat af en toe 429s goed zijn. Een aanhoudende groot aantal 429s kan erop duiden dat u de maximale hoeveelheid RU/s moet verhogen of uw partitie strategie voor een [Hot partitie](#autoscale-rate-limiting)kunt controleren.

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autoscale-is-enabled"></a><a id="autoscale-rate-limiting"></a>Is het nog steeds mogelijk om 429s (beperking/snelheids beperking) te zien wanneer automatisch schalen is ingeschakeld? 
Ja. Het is mogelijk om 429s in twee scenario's te bekijken. Als de totale hoeveelheid gebruikte RU/s de maximale RU/s van de container of data base overschrijdt, worden de aanvragen eerst door de service beperkt. 

Ten tweede, als er sprake is van een dynamische partitie, dat wil zeggen een logische partitie sleutel waarde met een onevenredig groter aantal aanvragen vergeleken met andere partitie sleutel waarden, is het mogelijk dat de onderliggende fysieke partitie het bovenliggende item/s-budget overschrijdt. Als best practice om warme partities te voor komen, [kiest u een goede partitie sleutel](partitioning-overview.md#choose-partitionkey) die resulteert in een gelijkmatige verdeling van zowel de opslag als de door voer. 

Als u bijvoorbeeld de optie Max-doorvoer capaciteit 20.000 RU/s selecteert en 200 GB aan opslag hebt, met vier fysieke partities, kan elke fysieke partitie automatisch worden geschaald naar 5000 RU/s. Als er een hete partitie is op een bepaalde logische-partitie sleutel, ziet u 429s wanneer de onderliggende fysieke partitie in meer dan 5000 RU/s is, d.w.z. meer dan 100% genormaliseerd gebruik.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [inschakelen van automatisch schalen op een Azure Cosmos-container of-data base](provision-throughput-autoscale.md#create-db-container-autoscale).
* Meer informatie over de [voor delen van ingerichte door Voer in de modus voor automatisch schalen](provision-throughput-autoscale.md#autoscale-benefits).
* Meer informatie over [logische en fysieke partities](partition-data.md).
