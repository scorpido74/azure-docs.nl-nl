---
title: Niet meer vrijgeven Azure-Sentinel | Microsoft Docs
description: Uw Azure-Sentinel-exemplaar verwijderen.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2019
ms.author: rkarlin
ms.openlocfilehash: a45f273078a622de5e256457fc45b6cb6cae512f
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74464137"
---
# <a name="remove-azure-sentinel-from-your-tenant"></a>Azure-Sentinel van uw Tenant verwijderen

Als u de Azure-Sentinel niet meer wilt gebruiken, wordt in dit artikel uitgelegd hoe u het kunt verwijderen uit uw Tenant.

## <a name="how-to-delete-azure-sentinel"></a>Azure Sentinel verwijderen

Op de achtergrond, wanneer u Azure Sentinel installeert, wordt de **SecurityInsights** -oplossing geïnstalleerd op de geselecteerde werk ruimte. Het eerste wat u moet doen, is het verwijderen van de **SecurityInsights** -oplossing.

1.  Ga naar de **Azure-Sentinel**, gevolgd door de **configuratie**, gevolgd door de **werk ruimte-instellingen**en vervolgens **oplossingen**.

2.  Selecteer `SecurityInsights` en klik erop.

    ![De SecurityInsights-oplossing zoeken](media/offboard/find-solution.png)

3.  Selecteer **verwijderen**boven aan de pagina.

    > [!IMPORTANT]
    > Als u de werk ruimte verwijdert, kan dit van invloed zijn op andere oplossingen en gegevens bronnen die gebruikmaken van deze werk ruimte, met inbegrip van Azure Monitor. Zie [geïnstalleerde bewakings oplossingen](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions)weer geven om te controleren welke oplossingen gebruikmaken van deze werk ruimte. Zie [informatie over opgenomen gegevens volume](../azure-monitor/platform/manage-cost-storage.md#understanding-ingested-data-volume)als u wilt controleren welke oplossingen gegevens worden opgenomen in de werk ruimte.

    ![De SecurityInsights-oplossing verwijderen](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>Wat gebeurt er achter de schermen?

Wanneer u de oplossing verwijdert, neemt Azure Sentinel tot 48 uur de eerste fase van het verwijderings proces af.

Nadat de verbinding is vastgesteld, wordt het offboarding-proces gestart.

**De configuratie van deze connectors wordt verwijderd:**
-   Office 365

-   AWS

-   Beveiligings waarschuwingen van micro soft-Services (Azure ATP, Microsoft Cloud App Security inclusief Cloud Discovery Shadow IT Reporting, Azure AD Identity Protection, micro soft Defender ATP, Azure Security Center)

-   Bedreigingsinformatie

-   Veelvoorkomende beveiligings Logboeken (inclusief op CEF gebaseerde logboeken, Barracuda en syslog) (als u Azure Security Center hebt, worden deze logboeken nog steeds verzameld.)

-   Windows-beveiligings gebeurtenissen (als u Azure Security Center hebt, worden deze logboeken nog steeds verzameld.)

Binnen de eerste 48 uur zijn de gegevens-en waarschuwings regels (inclusief realtime-automatiserings configuratie) niet langer toegankelijk of kunnen ze niet meer worden opgevraagd in azure Sentinel.

**Na 30 dagen worden deze resources verwijderd:**

-   Incidenten (met inbegrip van onderzoek meta gegevens)

-   Waarschuwingsregels

-   wijzer

Uw playbooks, opgeslagen werkmappen, opgeslagen jacht-query's en notitie blokken worden niet verwijderd. **Sommige kunnen worden verbroken vanwege de verwijderde gegevens. U kunt deze hand matig verwijderen.**

Nadat u de service hebt verwijderd, is er een respijt periode van 30 dagen waarin u de oplossing opnieuw kunt inschakelen. uw gegevens en waarschuwings regels worden hersteld, maar de geconfigureerde connectors die zijn losgekoppeld, moeten opnieuw worden verbonden.

> [!NOTE]
> Als u de oplossing verwijdert, wordt uw abonnement nog steeds geregistreerd bij de Azure Sentinel resource provider. **U kunt deze hand matig verwijderen.**




## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u de Azure Sentinel-service kunt verwijderen. Als u van gedachten verandert en deze opnieuw wilt installeren:
- Aan de slag [met on-boarding van Azure-Sentinel](quickstart-onboard.md).

