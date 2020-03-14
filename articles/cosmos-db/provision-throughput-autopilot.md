---
title: Azure Cosmos-containers en-data bases maken in de automatische test modus.
description: Meer informatie over de voor delen, use cases en het inrichten van Azure Cosmos-data bases en-containers in de automatische test modus.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 89af30788fe5129cddc6a3607b8c722549b610d1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246654"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autopilot-mode-preview"></a>Azure Cosmos-containers en-data bases maken in de automatische test modus (preview)

Met Azure Cosmos DB kunt u de doorvoer voor uw containers inrichten, in de handmatige modus of in de Autopilot-modus. In dit artikel worden de voordelen en gebruikscases van de Autopilot-modus beschreven.

> [!NOTE]
> De automatische test modus is momenteel beschikbaar in de open bare preview. U kunt auto [pilot alleen inschakelen voor nieuwe data bases en containers](#create-a-database-or-a-container-with-autopilot-mode) . De functie is niet beschikbaar voor bestaande containers en data bases.

Naast het hand matig inrichten van de door Voer kunt u nu Azure Cosmos-containers configureren in de automatische test modus. Containers en data bases die zijn geconfigureerd in de automatische test modus, kunnen **de ingerichte door Voer automatisch en direct schalen op basis van de behoeften van uw toepassing, zonder dat dit van invloed is op de beschik baarheid, latentie, door Voer of prestaties van de werk belasting wereld wijd.**

Bij het configureren van containers en data bases in de modus voor automatische prototypen, moet u de maximale door Voer opgeven `Tmax` niet worden overschreden. Containers kunnen vervolgens hun door Voer zodanig schalen dat `0.1*Tmax < T < Tmax`. Met andere woorden, containers en data bases worden direct geschaald op basis van de behoeften van de werk belasting, van slechts 10% van de maximale doorvoer waarde die u hebt geconfigureerd tot de geconfigureerde waarde voor maximale door voer. U kunt de instelling voor de maximale door Voer (`Tmax`) voor een auto pilot-data base of container op elk gewenst moment wijzigen. Met de optie auto pilot is de minimale door Voer van 400 RU/s per container of Data Base niet meer van toepassing.

Tijdens de preview-versie van auto pilot, voor de opgegeven maximale door Voer voor de container of de data base, kan het systeem binnen de berekende opslag limiet werken. Als de opslag limiet wordt overschreden, wordt de maximale door Voer automatisch aangepast naar een hogere waarde. Wanneer u door Voer op database niveau gebruikt met de modus Automatische test, wordt het aantal containers dat in een Data Base is toegestaan berekend als: `0.001*TMax`. Als u bijvoorbeeld 20.000 auto pilot RU/s inricht, kan de data base 20 containers bevatten.

## <a name="benefits-of-autopilot-mode"></a>Voor delen van automatische pilot modus

Azure Cosmos-containers die zijn geconfigureerd in de automatische test modus, hebben de volgende voor delen:

* **Eenvoudig:** Containers in de automatische test modus verwijderen de complexiteit om ingerichte door Voer (RUs) en capaciteit hand matig te beheren voor verschillende containers.

* **Schaalbaar:** Containers in de automatische test modus kunnen de ingerichte doorvoer capaciteit naadloos schalen naar behoefte. Er zijn geen onderbrekingen voor client verbindingen, toepassingen en ze hebben geen invloed op bestaande Sla's.

* **Rendabel:** Wanneer u containers gebruikt die zijn geconfigureerd in de modus voor automatische test doeleinden, betaalt u alleen voor de resources die de werk belasting per uur nodig heeft.

* **Maxi maal beschikbaar:** Containers in de automatische test modus gebruiken dezelfde wereld wijd gedistribueerde, fout tolerante, Maxi maal beschik bare back-end om de duurzaamheid van gegevens en hoge Beschik baarheid te garanderen.

## <a name="use-cases-of-autopilot-mode"></a>Gebruiks voorbeelden van de automatische test modus

De use cases voor Azure Cosmos-containers die zijn geconfigureerd in de automatische test modus zijn onder andere:

* **Variabele werk belastingen:** Wanneer u een intensief gebruikte toepassing gebruikt met een piek gebruik van 1 uur en enkele uren een paar keer per dag. Voor beelden zijn toepassingen voor personeels zaken, budget tering en operationele rapportage. Voor dergelijke scenario's kunnen containers die zijn geconfigureerd in de modus voor automatisch testen, worden gebruikt. u hoeft niet langer hand matig in te richten op de piek of de gemiddelde capaciteit.

* **Onvoorspelbare workloads:** Wanneer u werk belastingen uitvoert waarbij de data base gedurende de hele dag wordt gebruikt, maar ook pieken in de activiteit die moeilijk te voors pellen zijn. Een voor beeld bevat een verkeers site die een piek activiteit ziet wanneer de weers verwachting verandert. In containers die zijn geconfigureerd in de modus voor automatisch testen, past u de capaciteit aan om te voldoen aan de behoeften van de piek belasting van de toepassing en maakt u deze terug wanneer de activiteit wordt overschreden.

* **Nieuwe toepassingen:** Als u een nieuwe toepassing implementeert en weet u niet zeker hoeveel ingerichte door Voer (dat wil zeggen, hoeveel RUs) u nodig hebt. Als containers zijn geconfigureerd in de modus voor automatisch testen, kunt u automatisch schalen naar de capaciteits behoeften en vereisten van uw toepassing.

* **Zelden gebruikte toepassingen:** Als u een toepassing hebt die slechts enkele uren per dag of week of maand wordt gebruikt, zoals een toepassing/Web/blog-site met weinig volume.

* **Ontwikkelings-en test databases:** Als u ontwikkel aars hebt die containers gebruiken tijdens werk uren, maar ze niet op nacht of in het weekend hoeven te hebben. Als containers zijn geconfigureerd in de modus voor automatisch testen, worden ze omlaag geschaald wanneer ze niet worden gebruikt.

* **Geplande werk belastingen/query's voor productie:** Wanneer u een reeks geplande aanvragen/bewerkingen/query's op één container hebt, en als er niet-actieve Peri Oden zijn waarin u een absolute lage door voer wilt uitvoeren, kunt u dit nu eenvoudig doen. Wanneer een geplande query/aanvraag wordt verzonden naar een container die in de automatische test modus is geconfigureerd, wordt deze zo veel automatisch omhoog geschaald en wordt de bewerking uitgevoerd.

Oplossingen voor de vorige problemen vereisen niet alleen een enorme hoeveelheid tijd in de implementatie, maar ze veroorzaken ook complexiteit in configuratie of uw code en vereisen vaak hand matige tussen komst om ze te verhelpen. In de automatische test modus worden de bovenstaande scenario's uit het vak ingeschakeld, zodat u zich geen zorgen meer hoeft te maken over deze problemen.

## <a name="comparison--containers-configured-in-manual-mode-vs-autopilot-mode"></a>Vergelijking: containers die zijn geconfigureerd in de hand matige modus versus automatische test modus

|  | Containers geconfigureerd in hand matige modus  | Containers die zijn geconfigureerd in de modus voor automatische prototype |
|---------|---------|---------|
| **Ingerichte door Voer** | Hand matig ingericht. | Automatisch en onmiddellijk geschaald op basis van de gebruiks patronen van de werk belasting. |
| **Frequentie waarmee aanvragen/bewerkingen worden beperkt (429)**  | Dit kan gebeuren als het verbruik de ingerichte capaciteit overschrijdt. | Treedt niet op als de verbruikte door Voer is binnen de maximale door Voer die u hebt gekozen met de automatische test modus.   |
| **Capaciteitsplanning** |  U moet een eerste capaciteits planning maken en de door Voer die u nodig hebt, inrichten. |    U hoeft zich geen zorgen te maken over capaciteits planning. Het systeem zorgt automatisch voor capaciteits planning en capaciteits beheer. |
| **Prijzen** | Hand matig ingerichte RU/s per uur. | Voor afzonderlijke accounts voor schrijf regio's betaalt u de door Voer die op elk uur wordt gebruikt, door gebruik te maken van het tarief van de auto pilot RU per uur. <br/><br/>Voor accounts met meerdere schrijf regio's worden er geen extra kosten in rekening gebracht voor auto pilot. U betaalt voor de door Voer die op elk uur wordt gebruikt met hetzelfde tarief van één of meerdere masters per uur. |
| **Geschikt voor typen werk belastingen** |  Voorspel bare en stabiele workloads|   Onvoorspelbare en variabele workloads  |

## <a name="create-a-database-or-a-container-with-autopilot-mode"></a>Een Data Base of container maken met de automatische test modus

U kunt automatische pilot configureren voor nieuwe data bases of containers bij het maken hiervan via de Azure Portal. Gebruik de volgende stappen om een nieuwe data base of container te maken, automatische pilot in te scha kelen en de maximale door Voer (RU/s) op te geven.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) of de [Azure Cosmos DB Explorer.](https://cosmos.azure.com/)

1. Navigeer naar uw Azure Cosmos DB-account en open het tabblad **Data Explorer** .

1. Selecteer **nieuwe container.** Voer een naam in voor uw data base, container en partitie sleutel. Selecteer onder **door Voer**de optie auto **pilot** en kies de maximale door Voer (ru/s) die de data base of container niet mag overschrijden bij het gebruik van de optie voor automatische pilot.

   ![Een container maken en de door Voer van automatische pilot configureren](./media/provision-throughput-autopilot/create-container-autopilot-mode.png)

1. Selecteer **OK**.

U kunt een gedeelde doorvoer database maken met de automatische test modus door de optie **doorvoer database inrichten** in te scha kelen.

## <a id="autopilot-limits"></a>Door Voer en opslag limieten voor auto pilot

In de volgende tabel ziet u het maximum aantal in-en opslag limieten voor verschillende opties in de modus voor automatische prototype:

|Maximale doorvoer limiet  |Maximale opslag limiet  |
|---------|---------|
|4000 RU/s  |   50 GB    |
|20.000 RU/s  |  200 GB  |
|100.000 RU/s    |  1 TB   |
|500.000 RU/s    |  5 TB  |

## <a name="next-steps"></a>Volgende stappen

* Lees de [Veelgestelde vragen over auto pilot](autopilot-faq.md).
* Meer informatie over [logische partities](partition-data.md).
* Meer informatie over het [inrichten van de door Voer voor een Azure Cosmos-container](how-to-provision-container-throughput.md).
* Meer informatie over het [inrichten van de door Voer voor een Azure Cosmos-data base](how-to-provision-database-throughput.md).
