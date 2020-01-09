---
title: Uw OMS-update-implementaties migreren naar Azure
description: In dit artikel wordt beschreven hoe u uw bestaande OMS-update-implementaties naar Azure migreert
services: automation
ms.subservice: update-management
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 2660e4a348d2ffd71f912ff80c36a5a9a3c9fe88
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75417782"
---
# <a name="migrate-your-oms-update-deployments-to-azure"></a>Uw OMS-update-implementaties migreren naar Azure

De portal van operations management suite (OMS) wordt [afgeschaft](../azure-monitor/platform/oms-portal-transition.md). Alle functies die beschikbaar waren in de OMS-portal voor Updatebeheer, zijn beschikbaar in de Azure Portal. In dit artikel vindt u de informatie die u nodig hebt om naar het Azure Portal te migreren.

## <a name="key-information"></a>Belangrijke informatie

* Bestaande implementaties blijven werken. Wanneer u de implementatie opnieuw hebt gemaakt in azure, kunt u uw oude implementatie verwijderen uit OMS.
* Alle bestaande functies die u in OMS had, zijn beschikbaar in Azure. Zie [updatebeheer Overview](automation-update-management.md)voor meer informatie over updatebeheer.

## <a name="access-the-azure-portal"></a>Naar Azure Portal gaan

Klik in uw OMS-werk ruimte op **openen in azure**. Hiermee gaat u naar de Log Analytics-werk ruimte die door OMS is gebruikt.

![Openen in de Azure-OMS-Portal](media/migrate-oms-update-deployments/link-to-azure-portal.png)

Klik in het Azure Portal op **Automation-account**

![Azure Monitor-logboeken](media/migrate-oms-update-deployments/log-analytics.png)

Klik in uw Automation-account op **updatebeheer** om updatebeheer te openen.

![Updatebeheer](media/migrate-oms-update-deployments/azure-automation.png)

In de toekomst gaat u rechtstreeks naar de Azure Portal, onder **alle services**, selecteert u **Automation-accounts** onder **beheer hulpprogramma's**, selecteert u het juiste Automation-account en klikt u op **updatebeheer**.

## <a name="recreate-existing-deployments"></a>Bestaande implementaties opnieuw maken

Alle update-implementaties die in de OMS-Portal zijn gemaakt, hebben een [opgeslagen zoek opdracht](../azure-monitor/platform/computer-groups.md) ook wel een computer groep genoemd, met dezelfde naam als de update-implementatie die bestaat. De opgeslagen zoek actie bevat de lijst met computers die zijn gepland in de update-implementatie.

![Updatebeheer](media/migrate-oms-update-deployments/oms-deployment.png)

Voer de volgende stappen uit om deze bestaande opgeslagen zoek opdracht te gebruiken:

Als u een nieuwe update-implementatie wilt maken, gaat u naar de Azure Portal, selecteert u het Automation-account dat wordt gebruikt en klikt u op **updatebeheer**. Klik op **Update-implementatie plannen**.

![Implementaties van de update plannen](media/migrate-oms-update-deployments/schedule-update-deployment.png)

Het deel venster **nieuwe update-implementatie** wordt geopend. Voer waarden in voor de eigenschappen die in de volgende tabel worden beschreven en klik vervolgens op **maken**:

Als u wilt dat machines worden bijgewerkt, selecteert u de opgeslagen zoek opdracht die wordt gebruikt door de bestaande OMS-implementatie.

| Eigenschap | Beschrijving |
| --- | --- |
|Name |Unieke naam voor het identificeren van de update-implementatie. |
|Besturingssysteem| Selecteer **Linux** of **Windows**.|
|Machines die moeten worden bijgewerkt |selecteer een opgeslagen zoekopdracht of geïmporteerde groep, of kies Computer in de vervolgkeuzelijst en selecteer de afzonderlijke computers. Als u **Computers** selecteert, wordt de gereedheid van de computer weergegeven in de kolom **GEREEDHEID VOOR UPDATE-AGENT**.</br> Zie [Computergroepen in Azure Monitorlogboeken](../azure-monitor/platform/computer-groups.md) voor meer informatie over de verschillende manieren waarop u computergroepen kunt maken in Azure Monitor-logboeken |
|Updateclassificaties|Selecteer alle update classificaties die u nodig hebt. CentOS biedt geen ondersteuning voor dit out-of-Box.|
|Updates die moeten worden uitgesloten|Voer de updates in die moeten worden uitgesloten. Voer voor Windows het KB-artikel in zonder het voor voegsel **KB** . Voor Linux voert u de pakket naam in of gebruikt u een Joker teken.  |
|Schema-instellingen|Selecteer het tijdstip waarop u wilt beginnen en selecteer vervolgens **een of meer keren of** **terugkerend** voor het terugkeer patroon. | 
| Onderhouds venster |Aantal minuten dat is ingesteld voor updates. De waarde mag niet minder dan 30 minuten of langer dan 6 uur zijn. |
| Besturings element opnieuw opstarten| Hiermee wordt bepaald hoe opnieuw opstarten moet worden afgehandeld.</br>De volgende opties zijn beschikbaar:</br>Opnieuw opstarten indien nodig (standaard)</br>Altijd opnieuw opstarten</br>Nooit opnieuw opstarten</br>Alleen opnieuw opstarten - updates worden niet geïnstalleerd|

Klik op **geplande update-implementaties** om de status van de zojuist gemaakte update-implementatie weer te geven.

![nieuwe update-implementatie](media/migrate-oms-update-deployments/new-update-deployment.png)

Zoals eerder vermeld, kunnen de bestaande implementaties worden verwijderd uit de OMS-Portal zodra de nieuwe implementaties zijn geconfigureerd via de Azure Portal.

## <a name="next-steps"></a>Volgende stappen

Zie [updatebeheer](automation-update-management.md) voor meer informatie over updatebeheer in Azure.
