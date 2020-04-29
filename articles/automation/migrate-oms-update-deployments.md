---
title: Uw OMS-update-implementaties migreren naar Azure
description: In dit artikel wordt beschreven hoe u uw bestaande OMS-update-implementaties naar Azure migreert
services: automation
ms.subservice: update-management
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 910f284eedbf50be5b58b6c18f02e50adda35e9a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680007"
---
# <a name="migrate-your-oms-update-deployments-to-azure"></a>Uw OMS-update-implementaties migreren naar Azure

De portal van operations management suite (OMS) wordt [afgeschaft](../azure-monitor/platform/oms-portal-transition.md). Alle functionaliteit die beschikbaar was in de OMS-portal voor Updatebeheer is beschikbaar in het Azure Portal via Azure Monitor Logboeken. In dit artikel vindt u de informatie die u nodig hebt om naar het Azure Portal te migreren.

## <a name="key-information"></a>Belang rijke informatie

* Bestaande implementaties blijven werken. Wanneer u de implementatie opnieuw hebt gemaakt in azure, kunt u uw oude implementatie verwijderen uit OMS.
* Alle bestaande functies die u in OMS had, zijn beschikbaar in Azure. Zie [updatebeheer Overview](automation-update-management.md)voor meer informatie over updatebeheer.

## <a name="access-the-azure-portal"></a>Naar Azure Portal gaan

Klik in uw OMS-werk ruimte op **openen in azure**. Deze selectie navigeert naar de Log Analytics werk ruimte die OMS heeft gebruikt.

![Openen in de Azure-OMS-Portal](media/migrate-oms-update-deployments/link-to-azure-portal.png)

Klik in het Azure Portal op **Automation-account**

![Azure Monitor-logboeken](media/migrate-oms-update-deployments/log-analytics.png)

Klik op **updatebeheer**in uw Automation-account.

![Updatebeheer](media/migrate-oms-update-deployments/azure-automation.png)

Selecteer in de Azure Portal **Automation-accounts** onder **alle services**. 

Onder **beheer hulpprogramma's**selecteert u het juiste Automation-account en klikt u op **updatebeheer**.

## <a name="recreate-existing-deployments"></a>Bestaande implementaties opnieuw maken

Alle update-implementaties die in de OMS-Portal zijn gemaakt, hebben een [opgeslagen zoek opdracht](../azure-monitor/platform/computer-groups.md) ook wel een computer groep genoemd, met dezelfde naam als de update-implementatie die bestaat. De opgeslagen zoek actie bevat de lijst met computers die zijn gepland in de update-implementatie.

![Updatebeheer](media/migrate-oms-update-deployments/oms-deployment.png)

Voer de volgende stappen uit om deze bestaande opgeslagen zoek opdracht te gebruiken:

Als u een nieuwe update-implementatie wilt maken, gaat u naar de Azure Portal, selecteert u het Automation-account dat wordt gebruikt en klikt u op **updatebeheer**. Klik op **Update-implementatie plannen**.

![Update-implementatie plannen](media/migrate-oms-update-deployments/schedule-update-deployment.png)

Het deel venster nieuwe update-implementatie wordt geopend. Voer waarden in voor de eigenschappen die in de volgende tabel worden beschreven en klik vervolgens op **maken**:

**Als u wilt dat machines worden bijgewerkt**, selecteert u de opgeslagen zoek opdracht die wordt gebruikt door de bestaande OMS-implementatie.

| Eigenschap | Beschrijving |
| --- | --- |
|Naam |Unieke naam voor het identificeren van de update-implementatie. |
|Besturingssysteem| Selecteer **Linux** of **Windows**.|
|Machines die moeten worden bijgewerkt |selecteer een opgeslagen zoekopdracht of geïmporteerde groep, of kies Computer in de vervolgkeuzelijst en selecteer de afzonderlijke computers. Als u **Computers** selecteert, wordt de gereedheid van de computer weergegeven in de kolom **GEREEDHEID VOOR UPDATE-AGENT**.</br> Zie [Computergroepen in Azure Monitorlogboeken](../azure-monitor/platform/computer-groups.md) voor meer informatie over de verschillende manieren waarop u computergroepen kunt maken in Azure Monitor-logboeken |
|Update classifications|Selecteer alle update classificaties die u nodig hebt. CentOS biedt geen ondersteuning voor dit out-of-Box.|
|Updates die moeten worden uitgesloten|Voer de updates in die moeten worden uitgesloten. Voer voor Windows het KB-artikel in zonder het voor voegsel **KB** . Voor Linux voert u de pakket naam in of gebruikt u een Joker teken.  |
|Schema-instellingen|Selecteer het tijdstip waarop u wilt beginnen en selecteer vervolgens **een of meer keren of** **terugkerend** voor het terugkeer patroon. | 
| Onderhouds venster |Aantal minuten dat is ingesteld voor updates. De waarde mag niet minder dan 30 minuten of langer dan 6 uur zijn. |
| Besturings element opnieuw opstarten| Hiermee wordt bepaald hoe opnieuw opstarten moet worden afgehandeld.</br>De volgende opties zijn beschikbaar:</br>Opnieuw opstarten indien nodig (standaard)</br>Altijd opnieuw opstarten</br>Nooit opnieuw opstarten</br>Alleen opnieuw opstarten - updates worden niet geïnstalleerd|

Klik op **geplande update-implementaties** om de status van de zojuist gemaakte update-implementatie weer te geven.

![nieuwe update-implementatie](media/migrate-oms-update-deployments/new-update-deployment.png)

Zoals eerder vermeld, kunnen de bestaande implementaties worden verwijderd uit de OMS-Portal zodra de nieuwe implementaties zijn geconfigureerd via de Azure Portal.

## <a name="next-steps"></a>Volgende stappen

Zie [updatebeheer](automation-update-management.md)voor meer informatie over updatebeheer in Azure.
