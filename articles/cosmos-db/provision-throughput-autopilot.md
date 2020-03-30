---
title: Azure Cosmos-containers en databases maken in de automatische pilootmodus.
description: Meer informatie over de voordelen, use cases en hoe u Azure Cosmos-databases en -containers in de automatische pilootmodus inrichten.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 89af30788fe5129cddc6a3607b8c722549b610d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246654"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autopilot-mode-preview"></a>Azure Cosmos-containers en -databases maken in de Autopilot-modus (preview)

Met Azure Cosmos DB kunt u de doorvoer voor uw containers inrichten, in de handmatige modus of in de Autopilot-modus. In dit artikel worden de voordelen en gebruikscases van de Autopilot-modus beschreven.

> [!NOTE]
> De automatische pilootmodus is momenteel beschikbaar in een openbare preview. U [alleen automatische piloot inschakelen voor nieuwe databases en containers.](#create-a-database-or-a-container-with-autopilot-mode) Het is niet beschikbaar voor bestaande containers en databases.

Naast het handmatig inrichten van doorvoer, u azure cosmos-containers nu configureren in de automatische pilootmodus. Containers en databases die zijn geconfigureerd in de automatische pilootmodus, **schalen automatisch en direct de ingerichte doorvoer op basis van uw toepassingsbehoeften zonder dat dit gevolgen heeft voor de beschikbaarheid, latentie, doorvoer of prestaties van de werkbelasting wereldwijd.**

Wanneer u containers en databases in de automatische pilootmodus configureert, moet u de maximale doorvoer `Tmax` opgeven die niet mag worden overschreden. Containers kunnen vervolgens hun `0.1*Tmax < T < Tmax`doorvoer schalen, zodat . Met andere woorden, containers en databases schalen direct op basis van de werkbelastingbehoeften, vanaf 10% van de maximale doorvoerwaarde die u hebt geconfigureerd tot de geconfigureerde maximale doorvoerwaarde. U de maximale doorvoer ()`Tmax`instelling op een automatische piloot database of container op elk moment wijzigen. Met de optie automatische piloot is de minimale doorvoer van 400 RU/s per container of database niet meer van toepassing.

Tijdens de preview van de automatische piloot, voor de opgegeven maximale doorvoer op de container of de database, staat het systeem het werken binnen de berekende opslaglimiet toe. Als de opslaglimiet wordt overschreden, wordt de maximale doorvoer automatisch aangepast naar een hogere waarde. Bij het gebruik van de doorvoer van databaseniveau met de automatische `0.001*TMax`pilootmodus wordt het aantal containers dat in een database is toegestaan, berekend als: . Als u bijvoorbeeld 20.000 automatische piloot RU/s indient, kan de database 20 containers bevatten.

## <a name="benefits-of-autopilot-mode"></a>Voordelen van de automatische pilootmodus

Azure Cosmos-containers die zijn geconfigureerd in de automatische pilootmodus hebben de volgende voordelen:

* **Eenvoudig:** Containers in de automatische pilootmodus verwijderen de complexiteit om de ingerichte doorvoer (RU's) en capaciteit handmatig voor verschillende containers te beheren.

* **Schaalbaar:** Containers in de automatische pilootmodus schalen de ingerichte doorvoercapaciteit naadloos naar behoefte. Er is geen verstoring van clientverbindingen, toepassingen en ze hebben geen invloed op bestaande SLA's.

* **Kosteneffectief:** Wanneer u containers gebruikt die zijn geconfigureerd in de automatische pilootmodus, betaalt u alleen voor de resources die uw workloads per uur nodig hebben.

* **Zeer beschikbaar:** Containers in de automatische pilootmodus gebruiken dezelfde wereldwijd gedistribueerde, fouttolerante, zeer beschikbare backend om de duurzaamheid van gegevens en hoge beschikbaarheid te garanderen.

## <a name="use-cases-of-autopilot-mode"></a>Use cases van de automatische piloot modus

De use cases voor Azure Cosmos containers geconfigureerd in de automatische pilootmodus zijn:

* **Variabele workloads:** Wanneer u een licht gebruikte toepassing met piekgebruik van 1 uur tot enkele uren een paar keer per dag of meerdere keren per jaar uitvoert. Voorbeelden hiervan zijn toepassingen voor human resources, budgettering en operationele rapportage. Voor dergelijke scenario's kunnen containers die in de automatische pilootmodus zijn geconfigureerd, worden gebruikt en hoeft u niet langer handmatig in te richten op piek- of gemiddelde capaciteit.

* **Onvoorspelbare workloads:** Wanneer u workloads uitvoert waar er de hele dag databasegebruik is, maar ook pieken van activiteit die moeilijk te voorspellen zijn. Een voorbeeld hiervan is een verkeerssite die een toename van activiteit ziet wanneer de weersverwachting verandert. Containers die zijn geconfigureerd in de automatische pilootmodus, passen de capaciteit aan om te voldoen aan de behoeften van de piekbelasting van de toepassing en schalen terug wanneer de activiteitspiek voorbij is.

* **Nieuwe toepassingen:** Als u een nieuwe toepassing implementeert en niet zeker weet hoeveel ingerichte doorvoer (d.w.z. hoeveel RU's) u nodig hebt. Met containers geconfigureerd in de automatische pilootmodus, u automatisch schalen naar de capaciteitsbehoeften en vereisten van uw toepassing.

* **Zelden gebruikte toepassingen:** Als u een toepassing hebt die slechts enkele keren per dag of week of maand voor een paar uur wordt gebruikt, zoals een toepassing met een laag volume/web/blogsite.

* **Databases ontwikkelen en testen:** Als u ontwikkelaars hebt die containers gebruiken tijdens werkuren, maar deze niet nodig hebben op nachten of in het weekend. Met containers geconfigureerd in de automatische piloot modus, ze schalen tot een minimum wanneer niet in gebruik.

* **Geplande productieworkloads/query's:** Wanneer u een reeks geplande aanvragen/bewerkingen/query's op één container hebt en als er niet-actieve perioden zijn waarin u wilt uitvoeren bij een absoluut lage doorvoer, u dat nu eenvoudig doen. Wanneer een geplande query/aanvraag wordt ingediend bij een container die is geconfigureerd in de automatische pilootmodus, wordt deze automatisch zo veel opgeschaald als nodig is en wordt de bewerking uitgevoerd.

Oplossingen voor de vorige problemen vereisen niet alleen een enorme hoeveelheid tijd in de implementatie, maar ze introduceren ook complexiteit in de configuratie of uw code, en vereisen vaak handmatige interventie om ze aan te pakken. De automatische pilootmodus maakt de bovenstaande scenario's uit de doos mogelijk, zodat u zich geen zorgen meer hoeft te maken over deze problemen.

## <a name="comparison--containers-configured-in-manual-mode-vs-autopilot-mode"></a>Vergelijking – Containers geconfigureerd in handmatige modus versus automatische pilootmodus

|  | Containers geconfigureerd in handmatige modus  | Containers geconfigureerd in automatische pilootmodus |
|---------|---------|---------|
| **Ingerichte doorvoer** | Handmatig ingericht. | Automatisch en onmiddellijk geschaald op basis van de gebruikspatronen van de werkbelasting. |
| **Tariefbeperking van aanvragen/bewerkingen (429)**  | Kan gebeuren, als het verbruik de ingerichte capaciteit overschrijdt. | Zal niet gebeuren als de doorvoer verbruikt is binnen de maximale doorvoer die u kiest met de automatische piloot modus.   |
| **Capaciteitsplanning** |  U moet een initiële capaciteitsplanning en levering van de doorvoer doen die u nodig hebt. |    U hoeft zich geen zorgen te maken over capaciteitsplanning. Het systeem zorgt automatisch voor capaciteitsplanning en capaciteitsbeheer. |
| **Prijzen** | Handmatig ingerichte RU/s per uur. | Voor single write regio accounts betaalt u voor de doorvoer die op uurbasis wordt gebruikt, met behulp van de automatische piloot RU/s per uur tarief. <br/><br/>Voor accounts met meerdere schrijfregio's, is er geen extra kosten voor de automatische piloot. U betaalt voor de doorvoer die op uurbasis wordt gebruikt met hetzelfde multi-master RU/s per uurtarief. |
| **Het meest geschikt voor werkbelastingtypen** |  Voorspelbare en stabiele workloads|   Onvoorspelbare en variabele workloads  |

## <a name="create-a-database-or-a-container-with-autopilot-mode"></a>Een database of een container maken met automatische pilootmodus

U de automatische piloot configureren voor nieuwe databases of containers wanneer u deze maakt via de Azure-portal. Gebruik de volgende stappen om een nieuwe database of container te maken, automatische piloot in te schakelen en de maximale doorvoer (RU/s) op te geven.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) of de [Azure Cosmos DB-verkenner.](https://cosmos.azure.com/)

1. Navigeer naar uw Azure Cosmos DB-account en open het tabblad **Gegevensverkenner.**

1. Selecteer **Nieuwe container.** Voer een naam in voor uw database, container en een partitiesleutel. Selecteer **onder Doorvoer**de optie Automatische **piloot** en kies de maximale doorvoer (RU/s) die de database of container niet kan overschrijden wanneer u de optie automatische piloot gebruikt.

   ![Een container maken en de doorvoer van Autopilot configureren](./media/provision-throughput-autopilot/create-container-autopilot-mode.png)

1. Selecteer **OK**.

U een gedeelde doorvoerdatabase maken met de automatische pilootmodus door de optie **Databasedoorvoer voorziening te** selecteren.

## <a name="throughput-and-storage-limits-for-autopilot"></a><a id="autopilot-limits"></a>Doorvoer- en opslaglimieten voor automatische piloot

In de volgende tabel worden de maximale limieten en opslaglimieten voor verschillende opties in de automatische pilootmodus weergegeven:

|Maximale doorvoerlimiet  |Maximale opslaglimiet  |
|---------|---------|
|4000 RU/s  |   50 GB    |
|20.000 RU/s  |  200 GB  |
|100.000 RU/s    |  1 TB   |
|500.000 RU/s    |  5 TB  |

## <a name="next-steps"></a>Volgende stappen

* Bekijk de [veelgestelde vragen over](autopilot-faq.md)de automatische piloot .
* Meer informatie over [logische partities](partition-data.md).
* Meer informatie over het [inrichten van doorvoer op een Azure Cosmos-container](how-to-provision-container-throughput.md).
* Meer informatie over het [inrichten van doorvoer op een Azure Cosmos-database](how-to-provision-database-throughput.md).
