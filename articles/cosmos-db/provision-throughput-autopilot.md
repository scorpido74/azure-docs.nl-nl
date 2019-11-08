---
title: Azure Cosmos-containers en-data bases maken in de automatische test modus.
description: Meer informatie over de voor delen, use cases en het inrichten van Azure Cosmos-data bases en-containers in de automatische test modus.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e0c3c88119c3d064326442881854920b411f5ed4
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748385"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autopilot-mode-preview"></a>Azure Cosmos-containers en-data bases maken in de automatische test modus (preview)

Met Azure Cosmos DB kunt u de door Voer voor uw containers inrichten in hand matig of in de modus Auto Pilot. In dit artikel worden de voor delen en het gebruik van de automatische test modus beschreven.

> [!NOTE]
> De automatische test modus is momenteel beschikbaar in de open bare preview. Als u auto pilot wilt inschakelen voor uw Azure Cosmos-account, raadpleegt u de sectie auto [pilot inschakelen](#enable-autopilot) van dit artikel. U kunt auto pilot alleen inschakelen voor nieuwe data bases en containers, maar dit is niet beschikbaar voor bestaande containers en data bases.

Naast het hand matig inrichten van de door Voer kunt u nu Azure Cosmos-containers configureren in de automatische test modus. Azure Cosmos-containers en-data bases die zijn geconfigureerd in de automatische test modus, kunnen **de ingerichte door Voer automatisch laten schalen op basis van de behoeften van uw toepassing zonder in te boeten voor de sla's.**

U hoeft de ingerichte door Voer niet meer hand matig te beheren of om problemen met de snelheids beperking te verhelpen. Azure Cosmos-containers die zijn geconfigureerd in de modus voor automatische prototype, kunnen direct worden geschaald als reactie op de werk belasting zonder dat dit van invloed is op de beschik baarheid, latentie, door Voer of de prestaties van de werk belasting wereld wijd. Onder hoog gebruik kunnen Azure Cosmos-containers die zijn geconfigureerd in de modus voor automatische prototype omhoog of omlaag worden geschaald zonder dat dit van invloed is op de actieve bewerkingen.

Bij het configureren van containers en data bases in de modus voor automatische prototypen, moet u de maximale door Voer opgeven `Tmax` niet worden overschreden. Containers kunnen vervolgens direct worden geschaald op basis van de behoeften van de werk belasting binnen het `0.1*Tmax < T < Tmax` bereik. Met andere woorden, containers en data bases worden direct geschaald op basis van de behoeften van de werk belasting, van slechts 10% van de maximale doorvoer waarde die u hebt geconfigureerd en tot de geconfigureerde maximale doorvoer waarde. U kunt de instelling voor de maximale door Voer (tmax) op de auto pilot-data base of container op elk gewenst moment wijzigen.

Tijdens de preview-versie van auto pilot, voor de opgegeven maximale door Voer voor de container of de data base, kan het systeem binnen de berekende opslag limiet werken. Als de opslag limiet wordt overschreden, wordt de maximale door Voer automatisch aangepast naar een hogere waarde. Wanneer u door Voer op database niveau gebruikt met de modus Automatische test, wordt het aantal containers dat in een Data Base is toegestaan berekend als: (0,001 * maximale door Voer). Als u bijvoorbeeld 20.000 auto pilot RU/s inricht, kan de data base 20 containers bevatten.

## <a name="benefits-of-autopilot-mode"></a>Voor delen van automatische pilot modus

Azure Cosmos-containers die zijn geconfigureerd in de automatische test modus, hebben de volgende voor delen:

* **Eenvoudig:** Containers in de automatische test modus verwijderen de complexiteit om ingerichte door Voer (RUs) en capaciteit hand matig te beheren voor verschillende containers.

* **Schaalbaar:** Containers in de automatische test modus kunnen de ingerichte doorvoer capaciteit naadloos schalen naar behoefte. Er zijn geen onderbrekingen voor client verbindingen, toepassingen en ze hebben geen invloed op bestaande Sla's.

* **Rendabel:** Wanneer u Azure Cosmos-containers gebruikt die zijn geconfigureerd in de modus voor automatische test doeleinden, betaalt u alleen voor de resources die de werk belasting per uur nodig heeft.

* **Maxi maal beschikbaar:** Azure Cosmos-containers in de modus automatische prototype gebruiken dezelfde wereld wijd gedistribueerde, fout tolerante, Maxi maal beschik bare back-end om gegevens duurzaamheid en hoge Beschik baarheid te garanderen.

## <a name="use-cases-of-autopilot-mode"></a>Gebruiks voorbeelden van de automatische test modus

De use cases voor Azure Cosmos-containers die zijn geconfigureerd in de automatische test modus zijn onder andere:

* **Variabele werk belastingen:** Wanneer u een intensief gebruikte toepassing gebruikt met een piek gebruik van 1 uur en enkele uren paar keer per dag, of verschillende tijdstippen. Voor beelden zijn toepassingen voor personeels zaken, budget tering en operationele rapportage. Voor dergelijke scenario's kan de containers die zijn geconfigureerd in de modus voor automatisch testen worden gebruikt. u hoeft niet langer hand matig in te richten op de piek of de gemiddelde capaciteit.

* **Onvoorspelbare workloads:** Wanneer u werk belastingen uitvoert waarbij de data base gedurende de hele dag wordt gebruikt, maar ook pieken in de activiteit die moeilijk te voors pellen zijn. Een voor beeld bevat een verkeers site die een piek activiteit ziet wanneer de weers verwachting verandert. In containers die zijn geconfigureerd in de modus voor automatisch testen, past u de capaciteit aan om te voldoen aan de behoeften van de piek belasting van de toepassing en maakt u deze terug wanneer de activiteit wordt overschreden.

* **Nieuwe toepassingen:** Als u een nieuwe toepassing implementeert en weet u niet zeker hoeveel ingerichte door Voer (dat wil zeggen, hoeveel RUs) u nodig hebt. Als containers zijn geconfigureerd in de modus voor automatisch testen, kunt u automatisch schalen naar de capaciteits behoeften en vereisten van uw toepassing.

* **Zelden gebruikte toepassingen:** Als u een toepassing hebt die slechts enkele uren per dag of week of maand wordt gebruikt, zoals een toepassing/Web/blog-site met weinig volume.

* **Ontwikkelings-en test databases:** Ontwikkel aars gebruiken de Azure Cosmos-accounts tijdens werk uren, maar hoeven ze niet te worden genachten of in het weekend. Als containers zijn geconfigureerd in de modus voor automatisch testen, worden ze omlaag geschaald wanneer ze niet worden gebruikt.

* **Geplande werk belastingen/query's voor productie:** Wanneer u een reeks geplande aanvragen/bewerkingen/query's op één container hebt, en als er niet-actieve Peri Oden zijn waarin u een absolute lage door voer wilt uitvoeren, kunt u dit nu eenvoudig doen. Wanneer een geplande query/aanvraag wordt verzonden naar een container die in de automatische test modus is geconfigureerd, wordt deze zo veel automatisch omhoog geschaald en wordt de bewerking uitgevoerd.

Oplossingen voor de vorige problemen vereisen niet alleen een enorme hoeveelheid tijd in de implementatie, maar ze veroorzaken ook complexiteit in configuratie of uw code en vereisen vaak hand matige tussen komst om ze te verhelpen. In de automatische test modus kunnen bovenstaande scenario's worden uitgevoerd, zodat u zich geen zorgen meer hoeft te maken over deze problemen.

## <a name="comparison--containers-configured-in-manual-mode-vs-autopilot-mode"></a>Vergelijking: containers die zijn geconfigureerd in de hand matige modus versus automatische test modus

|  | Containers geconfigureerd in hand matige modus  | Containers die zijn geconfigureerd in de modus voor automatische prototype |
|---------|---------|---------|
| **Ingerichte door Voer** | Hand matig ingericht | Automatisch en onmiddellijk geschaald op basis van de gebruiks patronen van de werk belasting. |
| **Frequentie waarmee aanvragen/bewerkingen worden beperkt (429)**  | Dit kan gebeuren als het verbruik de ingerichte capaciteit overschrijdt. | Treedt niet op als de verbruikte door Voer is binnen de maximale door Voer die u hebt gekozen met de automatische test modus.   |
| **Capaciteitsplanning** |  U moet een eerste capaciteits planning maken en de door Voer die u nodig hebt, inrichten. |    U hoeft zich geen zorgen te maken over capaciteits planning. Het systeem zorgt automatisch voor capaciteits planning en capaciteits beheer. |
| **Prijzen** | Hand matig ingerichte RU/s per uur. | Voor afzonderlijke accounts voor schrijf regio's betaalt u de door Voer die op elk uur wordt gebruikt, door gebruik te maken van het tarief van de auto pilot RU per uur. <br/><br/>Voor accounts met meerdere schrijf regio's worden er geen extra kosten in rekening gebracht voor auto pilot. U betaalt voor de door Voer die op elk uur wordt gebruikt met hetzelfde tarief van één of meerdere masters per uur. |
| **Geschikt voor typen werk belastingen** |  Voorspel bare en stabiele workloads|   Onvoorspelbare en variabele workloads  |

## <a name="a-idenable-autopilot-enable-autopilot-from-azure-portal"></a>Automatische test <a id="enable-autopilot"> inschakelen vanuit Azure Portal

U kunt automatische pilot in uw Azure Cosmos-accounts uitproberen door in te scha kelen in van Azure Portal. Gebruik de volgende stappen om de auto pilot-optie in te scha kelen:

1. Meld u aan bij de [Azure Portal.](https://portal.azure.com)

2. Ga naar uw Azure Cosmos-account en open het tabblad **nieuwe functies** . Selecteer **automatische pilot** en **Schrijf** u in zoals weer gegeven in de volgende scherm afbeelding:

![Een container maken in de automatische test modus](./media/provision-throughput-autopilot/enable-autopilot-azure-portal.png)

## <a name="create-a-database-or-a-container-with-autopilot-mode"></a>Een Data Base of container maken met de automatische test modus

U kunt automatische pilot configureren voor data bases of containers tijdens het maken hiervan. Gebruik de volgende stappen om een nieuwe data base of container te maken, automatische pilot in te scha kelen en de maximale door Voer op te geven.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) of [Azure Cosmos Explorer.](https://cosmos.azure.com/)

1. Ga naar uw Azure Cosmos-account en open het tabblad **Data Explorer** .

1. Selecteer **nieuwe container**, voer een naam in voor de container, een partitie sleutel. Selecteer de optie auto **pilot** en kies de maximale door Voer die de container niet mag overschrijden bij het gebruik van de optie Auto Pilot.

   ![Een container maken in de automatische test modus](./media/provision-throughput-autopilot/create-container-autopilot-mode.png)

1. Selecteer **OK**

Met vergelijk bare stappen kunt u ook een Data Base maken met een ingerichte door Voer in de automatische test modus.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [logische partities](partition-data.md).
* Meer informatie over het [inrichten van de door Voer voor een Azure Cosmos-container](how-to-provision-container-throughput.md).
* Meer informatie over het [inrichten van de door Voer voor een Azure Cosmos-data base](how-to-provision-database-throughput.md).
