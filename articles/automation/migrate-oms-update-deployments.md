---
title: Update-implementaties van Azure Monitor-logboeken migreren naar Azure Portal
description: In dit artikel wordt beschreven hoe u Azure Monitor update-implementaties migreert naar Azure Portal.
services: automation
ms.subservice: update-management
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 9bd6a7ff943b5f3750ce8aaeada32010b88272c2
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745626"
---
# <a name="migrate-azure-monitor-logs-update-deployments-to-azure-portal"></a>Update-implementaties van Azure Monitor-logboeken migreren naar Azure Portal

De portal van operations management suite (OMS) wordt [afgeschaft](../azure-monitor/platform/oms-portal-transition.md). Alle functionaliteit die beschikbaar was in de OMS-portal voor Updatebeheer is beschikbaar in het Azure Portal via Azure Monitor Logboeken. In dit artikel vindt u de informatie die u nodig hebt om naar het Azure Portal te migreren.

## <a name="key-information"></a>Belang rijke informatie

* Bestaande implementaties blijven werken. Wanneer u de implementatie opnieuw hebt gemaakt in azure, kunt u de oude implementatie verwijderen.
* Alle bestaande functies die u in OMS had, zijn beschikbaar in Azure. Zie [updatebeheer Overview](automation-update-management.md)voor meer informatie over updatebeheer.

## <a name="access-the-azure-portal"></a>Naar Azure Portal gaan

1. Klik in uw werk ruimte op **openen in azure**. 

    ![Openen in azure-Log Analytics](media/migrate-oms-update-deployments/link-to-azure-portal.png)

2. Klik in het Azure Portal op **Automation-account**

    ![Azure Monitor-logboeken](media/migrate-oms-update-deployments/log-analytics.png)

3. Klik op **updatebeheer**in uw Automation-account.

    ![Updatebeheer](media/migrate-oms-update-deployments/azure-automation.png)

4. Selecteer in de Azure Portal **Automation-accounts** onder **alle services**. 

5. Onder **beheer hulpprogramma's**selecteert u het juiste Automation-account en klikt u op **updatebeheer**.

## <a name="recreate-existing-deployments"></a>Bestaande implementaties opnieuw maken

Alle update-implementaties die in de OMS-Portal zijn gemaakt, hebben een [opgeslagen zoek opdracht](../azure-monitor/platform/computer-groups.md) ook wel een computer groep genoemd, met dezelfde naam als de update-implementatie die bestaat. De opgeslagen zoek actie bevat de lijst met computers die zijn gepland in de update-implementatie.

![Updatebeheer](media/migrate-oms-update-deployments/oms-deployment.png)

Voer de volgende stappen uit om deze bestaande opgeslagen zoek opdracht te gebruiken:

1. Als u een nieuwe update-implementatie wilt maken, gaat u naar de Azure Portal, selecteert u het Automation-account dat wordt gebruikt en klikt u op **updatebeheer**. Klik op **Update-implementatie plannen**.

    ![Update-implementatie plannen](media/migrate-oms-update-deployments/schedule-update-deployment.png)

2. Het deel venster nieuwe update-implementatie wordt geopend. Voer waarden in voor de eigenschappen die in de volgende tabel worden beschreven en klik vervolgens op **maken**:

3. **Als u wilt dat machines worden bijgewerkt**, selecteert u de opgeslagen zoek opdracht die wordt gebruikt door de OMS-implementatie.

    | Eigenschap | Beschrijving |
    | --- | --- |
    |Naam |Unieke naam voor het identificeren van de update-implementatie. |
    |Besturingssysteem| Selecteer **Linux** of **Windows**.|
    |Machines die moeten worden bijgewerkt |Selecteer een opgeslagen zoek opdracht, geïmporteerde groep of machine kiezen in de vervolg keuzelijst en selecteer afzonderlijke machines. Als u **Computers** selecteert, wordt de gereedheid van de computer weergegeven in de kolom **GEREEDHEID VOOR UPDATE-AGENT**.</br> Zie [computer groepen in azure monitor-logboeken](../azure-monitor/platform/computer-groups.md) voor meer informatie over de verschillende methoden voor het maken van computer groepen in azure monitor-Logboeken. |
    |Update classifications|Selecteer alle update classificaties die u nodig hebt. CentOS biedt geen ondersteuning voor dit out-of-Box.|
    |Updates die moeten worden uitgesloten|Voer de updates in die moeten worden uitgesloten. Voer voor Windows het KB-artikel in zonder het voor voegsel **KB** . Voor Linux voert u de pakket naam in of gebruikt u een Joker teken.  |
    |Schema-instellingen|Selecteer het tijdstip waarop u wilt beginnen en selecteer vervolgens **een of meer keren of** **terugkerend** voor het terugkeer patroon. | 
    | Onderhouds venster |Aantal minuten dat is ingesteld voor updates. De waarde mag niet minder dan 30 minuten of langer dan 6 uur zijn. |
    | Besturings element opnieuw opstarten| Hiermee wordt bepaald hoe opnieuw opstarten moet worden afgehandeld.</br>De volgende opties zijn beschikbaar:</br>Opnieuw opstarten indien nodig (standaard)</br>Altijd opnieuw opstarten</br>Nooit opnieuw opstarten</br>Alleen opnieuw opstarten - updates worden niet geïnstalleerd|

4. Klik op **geplande update-implementaties** om de status van de zojuist gemaakte update-implementatie weer te geven.

    ![nieuwe update-implementatie](media/migrate-oms-update-deployments/new-update-deployment.png)

5. Zoals eerder vermeld, kunt u, nadat de nieuwe implementaties zijn geconfigureerd via de Azure Portal, de bestaande implementaties uit de Azure Portal verwijderen.

## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van updatebeheer](automation-update-management.md)voor meer informatie over Updatebeheer in azure Automation.
