---
title: Uw OMS-updateimplementaties migreren naar Azure
description: In dit artikel wordt beschreven hoe u uw bestaande OMS Update-implementaties migreert naar Azure
services: automation
ms.subservice: update-management
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 910f284eedbf50be5b58b6c18f02e50adda35e9a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680007"
---
# <a name="migrate-your-oms-update-deployments-to-azure"></a>Uw OMS-update-implementaties migreren naar Azure

De Operations Management Suite (OMS)-portal wordt [afgeschaft.](../azure-monitor/platform/oms-portal-transition.md) Alle functionaliteit die beschikbaar was in de OMS-portal voor Updatebeheer is beschikbaar in de Azure-portal, via Azure Monitor-logboeken. In dit artikel vindt u de informatie die u nodig hebt om te migreren naar de Azure-portal.

## <a name="key-information"></a>Belangrijkste informatie

* Bestaande implementaties blijven werken. Nadat u de implementatie in Azure opnieuw hebt gemaakt, u uw oude implementatie verwijderen uit OMS.
* Alle bestaande functies die u in OMS had, zijn beschikbaar in Azure, zie Overzicht van updatebeheer voor meer informatie over [Updatebeheer.](automation-update-management.md)

## <a name="access-the-azure-portal"></a>Naar Azure Portal gaan

Klik in uw OMS-werkruimte op **Openen in Azure.** Met deze selectie navigeert u naar de werkruimte Log Analytics die OMS heeft gebruikt.

![Openen in Azure - OMS-portal](media/migrate-oms-update-deployments/link-to-azure-portal.png)

Klik in de Azure-portal op **Automatiseringsaccount**

![Azure Monitor-logboeken](media/migrate-oms-update-deployments/log-analytics.png)

Klik in uw automatiseringsaccount op **Beheer bijwerken**.

![Updatebeheer](media/migrate-oms-update-deployments/azure-automation.png)

Selecteer in de Azure-portal **Automatiseringsaccounts** onder **Alle services**. 

Selecteer **onder Beheerhulpprogramma's**het juiste automatiseringsaccount en klik op **Beheer bijwerken**.

## <a name="recreate-existing-deployments"></a>Bestaande implementaties opnieuw maken

Alle update-implementaties die zijn gemaakt in de OMS-portal hebben een [opgeslagen zoekopdracht](../azure-monitor/platform/computer-groups.md) die ook wel een computergroep wordt genoemd, met dezelfde naam als de update-implementatie die bestaat. De opgeslagen zoekopdracht bevat de lijst met machines die zijn gepland in de update-implementatie.

![Updatebeheer](media/migrate-oms-update-deployments/oms-deployment.png)

Voer de volgende stappen uit om deze bestaande opgeslagen zoekopdracht te gebruiken:

Als u een nieuwe update-implementatie wilt maken, gaat u naar de Azure-portal, selecteert u het automatiseringsaccount dat wordt gebruikt en klikt u op **Beheer bijwerken**. Klik **op Implementatie bijwerken plannen**.

![Update-implementatie plannen](media/migrate-oms-update-deployments/schedule-update-deployment.png)

Het deelvenster Nieuwe update-implementatie wordt geopend. Voer waarden in voor de eigenschappen die in de volgende tabel zijn beschreven en klik op **Maken:**

Als **u Machines wilt bijwerken,** selecteert u de opgeslagen zoekopdracht die wordt gebruikt door de bestaande OMS-implementatie.

| Eigenschap | Beschrijving |
| --- | --- |
|Naam |Unieke naam voor het identificeren van de update-implementatie. |
|Besturingssysteem| Selecteer **Linux** of **Windows**.|
|Machines om bij te werken |selecteer een opgeslagen zoekopdracht of geïmporteerde groep, of kies Computer in de vervolgkeuzelijst en selecteer de afzonderlijke computers. Als u **Computers** selecteert, wordt de gereedheid van de computer weergegeven in de kolom **GEREEDHEID VOOR UPDATE-AGENT**.</br> Zie [Computergroepen in Azure Monitorlogboeken](../azure-monitor/platform/computer-groups.md) voor meer informatie over de verschillende manieren waarop u computergroepen kunt maken in Azure Monitor-logboeken |
|Update classifications|Selecteer alle updateclassificaties die u nodig hebt. CentOS ondersteunt dit niet out of the box.|
|Updates uit te sluiten|Voer de updates in om uit te sluiten. Voer voor Windows het KB-artikel in zonder het KB-voorvoegsel. **KB** Voer voor Linux de naam van het pakket in of gebruik een jokerteken.  |
|Instellingen plannen|Selecteer de tijd die u wilt starten en selecteer **Eenmaal** of **Terugkerend** voor de herhaling. | 
| Onderhoudsvenster |Aantal minuten ingesteld voor updates. De waarde mag niet minder dan 30 minuten of meer dan 6 uur zijn. |
| Besturingselement opnieuw opstarten| Hiermee bepaalt u hoe opnieuw opstarten moet worden afgehandeld.</br>De volgende opties zijn beschikbaar:</br>Opnieuw opstarten indien nodig (standaard)</br>Altijd opnieuw opstarten</br>Nooit opnieuw opstarten</br>Alleen opnieuw opstarten - updates worden niet geïnstalleerd|

Klik **op Implementaties voor geplande updates** om de status van de nieuw gemaakte update-implementatie weer te geven.

![nieuwe update-implementatie](media/migrate-oms-update-deployments/new-update-deployment.png)

Zoals eerder vermeld, kunnen de bestaande implementaties worden verwijderd uit de OMS-portal zodra uw nieuwe implementaties zijn geconfigureerd via de Azure-portal.

## <a name="next-steps"></a>Volgende stappen

Zie [Updatebeheer](automation-update-management.md)voor meer informatie over Updatebeheer in Azure.
