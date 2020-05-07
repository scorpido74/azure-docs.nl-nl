---
title: Azure Cosmos-containers en-data bases maken in ingerichte door Voer voor automatisch schalen.
description: Meer informatie over de voor delen, use cases en instructies voor het inrichten van Azure Cosmos-data bases en-containers in ingerichte door Voer voor automatisch schalen.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 81a13dcb7955a7d46f485416bf9b7e4e7be4d9ac
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791711"
---
# <a name="create-azure-cosmos-containers-and-databases-with-autoscale-provisioned-throughput"></a>Azure Cosmos-containers en-data bases maken met door Voer ingericht voor automatisch schalen

Met Azure Cosmos DB kunt u uw containers configureren met een standaard (hand matig) ingerichte door Voer of door Voer ingericht voor automatisch schalen. In dit artikel worden de voor delen en het gebruik van automatisch schalen beschreven.

> [!NOTE]
> U kunt [automatisch schalen inschakelen voor nieuwe data bases en containers](#create-db-container-autoscale) . De functie is niet beschikbaar voor bestaande containers en data bases.

Naast de standaard inrichting van de door Voer kunt u nu Azure Cosmos-containers configureren met de door Voer ingericht voor automatisch schalen. In containers en data bases die zijn geconfigureerd in de door Voer van automatisch schalen, wordt de ingerichte door Voer van de voorziene doorvoer gegevens op **basis van de behoeften van uw toepassing, zonder dat dit van invloed is op de beschik baarheid, de latentie, de door Voer of de prestaties**

Bij het configureren van containers en data bases in automatisch schalen, moet u de `Tmax` maximale door Voer opgeven die niet moet worden overschreden. Containers kunnen vervolgens hun door Voer zodanig schalen `0.1*Tmax < T < Tmax`dat. Met andere woorden, containers en data bases worden direct geschaald op basis van de behoeften van de werk belasting, van slechts 10% van de maximale doorvoer waarde die u hebt geconfigureerd tot de geconfigureerde waarde voor maximale door voer. Nadat u automatisch schalen hebt geconfigureerd, kunt u de instelling voor`Tmax`de maximale door Voer () op een Data Base of container op elk gewenst moment wijzigen. Met de optie voor automatisch schalen is de minimale door Voer van 400 RU/s per container of Data Base niet meer van toepassing.

Voor de opgegeven maximale door Voer voor de container of de data base, kan het systeem binnen de berekende opslag limiet werken. Als de opslag limiet wordt overschreden, wordt de maximale door Voer automatisch aangepast naar een hogere waarde. Wanneer u door Voer op database niveau gebruikt met automatisch schalen, wordt het aantal containers dat in een Data Base `0.001*TMax`is toegestaan berekend als:. Als u bijvoorbeeld 20.000 automatisch schalen van RU/s inricht, kan de data base 20 containers bevatten.

## <a name="benefits-of-autoscale-provisioned-throughput"></a><a id="autoscale-benefits"></a>Voor delen van ingerichte door Voer voor automatisch schalen

Azure Cosmos-containers die zijn geconfigureerd met automatisch schalen, hebben de volgende voor delen:

* **Eenvoudig:** Containers met automatisch schalen verwijderen de complexiteit om ingerichte door Voer (RUs) en capaciteit hand matig te beheren voor verschillende containers.

* **Schaalbaar:** Containers met automatisch schalen kunnen de ingerichte doorvoer capaciteit naadloos schalen als dat nodig is. Er zijn geen onderbrekingen voor client verbindingen, toepassingen en ze hebben geen invloed op bestaande Sla's.

* **Rendabel:** Wanneer u containers gebruikt die zijn geconfigureerd met automatisch schalen, betaalt u alleen voor de resources die de werk belasting per uur nodig heeft.

* **Maxi maal beschikbaar:** Containers met automatisch schalen gebruiken dezelfde wereld wijd gedistribueerde, fout tolerante, Maxi maal beschik bare back-end om gegevens duurzaamheid en hoge Beschik baarheid te garanderen.

## <a name="use-cases-of-autoscale-provisioned-throughput"></a><a id="autoscale-usecases"></a>Gebruiks voorbeelden van ingerichte door Voer voor automatisch schalen

De use cases voor Azure Cosmos-containers die zijn geconfigureerd met automatisch schalen, zijn onder andere:

* **Variabele werk belastingen:** Wanneer u een intensief gebruikte toepassing gebruikt met een piek gebruik van 1 uur en enkele uren een paar keer per dag. Voor beelden zijn toepassingen voor personeels zaken, budget tering en operationele rapportage. Voor dergelijke scenario's kunnen containers die zijn geconfigureerd met automatisch schalen, worden gebruikt. u hoeft niet langer hand matig in te richten op de piek of de gemiddelde capaciteit.

* **Onvoorspelbare workloads:** Wanneer u werk belastingen uitvoert waarbij de data base gedurende de hele dag wordt gebruikt, maar ook pieken in de activiteit die moeilijk te voors pellen zijn. Een voor beeld bevat een verkeers site die een piek activiteit ziet wanneer de weers verwachting verandert. Containers die zijn geconfigureerd met automatisch schalen, passen de capaciteit aan om te voldoen aan de behoeften van de piek belasting van de toepassing en worden teruggedraaid wanneer de piek van de activiteit is overschreden.

* **Nieuwe toepassingen:** Als u een nieuwe toepassing implementeert en weet u niet zeker hoeveel ingerichte door Voer (dat wil zeggen, hoeveel RUs) u nodig hebt. Als containers met automatisch schalen zijn geconfigureerd, kunt u automatisch schalen naar de capaciteits behoeften en vereisten van uw toepassing.

* **Zelden gebruikte toepassingen:** Als u een toepassing hebt die slechts enkele uren per dag of week of maand wordt gebruikt, zoals een toepassing/Web/blog-site met weinig volume.

* **Ontwikkelings-en test databases:** Als u ontwikkel aars hebt die containers gebruiken tijdens werk uren, maar ze niet op nacht of in het weekend hoeven te hebben. Bij containers die zijn geconfigureerd met automatisch schalen, worden ze omlaag geschaald wanneer ze niet worden gebruikt.

* **Geplande werk belastingen/query's voor productie:** Wanneer u een reeks geplande aanvragen/bewerkingen/query's op één container hebt, en als er niet-actieve Peri Oden zijn waarin u een absolute lage door voer wilt uitvoeren, kunt u dit nu eenvoudig doen. Wanneer een geplande query/aanvraag wordt verzonden naar een container die is geconfigureerd met automatisch schalen, wordt deze automatisch omhoog geschaald en wordt de bewerking uitgevoerd.

Oplossingen voor de vorige problemen vereisen niet alleen een enorme hoeveelheid tijd in de implementatie, maar ze veroorzaken ook complexiteit in configuratie of uw code en vereisen vaak hand matige tussen komst om ze te verhelpen. Met automatisch schalen kunt u de bovenstaande scenario's uit het vak inschakelen, zodat u zich geen zorgen meer hoeft te maken over deze problemen.

## <a name="comparison--standard-manual-vs-autoscale-provisioned-throughput"></a>Comparison: Standard (hand matig) versus automatisch schalen ingerichte door Voer

|  | Containers die zijn geconfigureerd met een standaard ingerichte door Voer  | Containers die zijn geconfigureerd met automatisch schalen ingerichte door Voer |
|---------|---------|---------|
| **Ingerichte doorvoer** | Hand matig ingericht. | Automatisch en onmiddellijk geschaald op basis van de gebruiks patronen van de werk belasting. |
| **Frequentie waarmee aanvragen/bewerkingen worden beperkt (429)**  | Dit kan gebeuren als het verbruik de ingerichte capaciteit overschrijdt. | Treedt niet op als de verbruikte door Voer is binnen de maximale door Voer die u kiest voor automatisch schalen.   |
| **Capaciteitsplanning** |  U moet een eerste capaciteits planning maken en de door Voer die u nodig hebt, inrichten. |    U hoeft zich geen zorgen te maken over capaciteits planning. Het systeem zorgt automatisch voor capaciteits planning en capaciteits beheer. |
| **Prijzen** | Hand matig ingerichte RU/s per uur. | Voor afzonderlijke accounts voor schrijf regio's betaalt u de door Voer die op elk uur wordt gebruikt, met behulp van het tarief voor automatisch schalen van RU/s per uur. <br/><br/>Voor accounts met meerdere schrijf regio's zijn er geen extra kosten voor automatisch schalen. U betaalt voor de door Voer die op elk uur wordt gebruikt met hetzelfde tarief van één of meerdere masters per uur. |
| **Geschikt voor typen werk belastingen** |  Voorspel bare en stabiele workloads|   Onvoorspelbare en variabele workloads  |

## <a name="create-a-database-or-a-container-with-autoscale"></a><a id="create-db-container-autoscale"></a>Een Data Base of container maken met automatisch schalen

U kunt automatisch schalen configureren voor nieuwe data bases of containers bij het maken hiervan via de Azure Portal. Gebruik de volgende stappen om een nieuwe data base of container te maken, automatisch schalen in te scha kelen en de maximale door Voer (RU/s) op te geven.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) of de [Azure Cosmos DB Explorer.](https://cosmos.azure.com/)

1. Navigeer naar uw Azure Cosmos DB-account en open het tabblad **Data Explorer** .

1. Selecteer **nieuwe container.** Voer een naam in voor uw data base, container en partitie sleutel. Selecteer onder **door Voer**de optie voor **automatisch schalen** en kies de maximale door Voer (ru/s) die de data base of container niet mag overschrijden bij het gebruik van de optie voor automatisch schalen.

   ![Een container maken en de door Voer voor automatisch schalen configureren](./media/provision-throughput-autoscale/create-container-autoscale-mode.png)

1. Selecteer **OK**.

U kunt een gedeelde doorvoer database maken met automatisch schalen door de optie **doorvoer database inrichten** in te scha kelen.

## <a name="throughput-and-storage-limits-for-autoscale"></a><a id="autoscale-limits"></a>Doorvoer-en opslag limieten voor automatisch schalen

In de volgende tabel ziet u het maximum aantal in-en opslag limieten voor verschillende opties in automatisch schalen:

|Maximale doorvoer limiet  |Maximale opslag limiet  |
|---------|---------|
|4000 RU/s  |   50 GB    |
|20.000 RU/s  |  200 GB  |
|100.000 RU/s    |  1 TB   |
|500.000 RU/s    |  5 TB  |

## <a name="next-steps"></a>Volgende stappen

* Bekijk de [Veelgestelde vragen over automatisch schalen](autoscale-faq.md).
* Meer informatie over [logische partities](partition-data.md).
* Meer informatie over het [inrichten van de door Voer voor een Azure Cosmos-container](how-to-provision-container-throughput.md).
* Meer informatie over het [inrichten van de door Voer voor een Azure Cosmos-data base](how-to-provision-database-throughput.md).
