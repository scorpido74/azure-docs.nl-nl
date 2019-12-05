---
title: Office 365-gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van Office 365-gegevens met Azure Sentinel.
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
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: df5aade7244f69e7264f901364ecc164351eec50
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815783"
---
# <a name="connect-data-from-office-365-logs"></a>Gegevens verbinden vanuit Office 365-logboeken



U kunt audit logboeken vanuit [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) streamen naar Azure Sentinel met één klik. U kunt audit logboeken van meerdere tenants streamen naar één werk ruimte in azure Sentinel. De connector van het Office 365-activiteiten logboek biedt inzicht in voortdurende gebruikers activiteiten. U krijgt informatie over verschillende acties en gebeurtenissen van de gebruiker, het beheer, het systeem en het beleid van Office 365. Door Office 365-Logboeken in azure Sentinel te koppelen, kunt u deze gegevens gebruiken om Dash boards weer te geven, aangepaste waarschuwingen te maken en uw onderzoek proces te verbeteren.

> [!IMPORTANT]
> Als u een E3-licentie hebt, moet u, voordat u toegang kunt krijgen tot gegevens via de API voor Office 365-beheer activiteit, Unified audit logging inschakelen voor uw Office 365-organisatie. U doet dit door het Office 365-controle logboek in te scha kelen. Zie voor instructies het [Office 365-controle logboek zoeken in-of uitschakelen](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off). Zie de naslag informatie voor de [API voor Office 365-beheer activiteiten](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference).

## <a name="prerequisites"></a>Vereisten

- U moet een globale beheerder of beveiligings beheerder zijn op uw Tenant
- Op uw computer, van waaruit u bent aangemeld bij Azure Sentinel om de verbinding tot stand te brengen, moet u ervoor zorgen dat poort 4433 is geopend voor webverkeer. Deze poort kan opnieuw worden gesloten nadat de verbinding tot stand is gebracht.
- Als uw Tenant geen Office 365 E3 of Office 365 E5-licentie heeft, moet u Unified auditing inschakelen voor uw Tenant met een van de volgende processen:
    - [Gebruik de cmdlet Set-AdminAuditLogConfig](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) en schakel de para meter ' UnifiedAuditLogIngestionEnabled ' in.
    - [Of met behulp van de beveiligings-en compliancecentrum-gebruikers interface](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin).

## <a name="connect-to-office-365"></a>Verbinding maken met Office 365

1. Selecteer in azure Sentinel **Data connectors** en klik vervolgens op de tegel **Office 365** .

2. Als u dit nog niet hebt ingeschakeld, kunt u dit doen door naar de Blade **Data connectors** te gaan en **Office 365** -connector te selecteren. Hier kunt u op de **pagina connector openen** klikken en onder configuratie sectie met de naam **de Office 365-oplossing inschakelen in uw werk ruimte** de knop **oplossing installeren** gebruiken om deze functie in te scha kelen. Als deze al is ingeschakeld, wordt dit in het scherm verbinding aangeduid als reeds ingeschakeld.
1. Met Office 365 kunt u gegevens van meerdere tenants naar Azure Sentinel streamen. Voor elke Tenant waarmee u verbinding wilt maken, voegt u de Tenant toe onder **tenants verbinden met Azure Sentinel**. 
1. Er wordt een Active Directory scherm geopend. U wordt gevraagd om u te verifiëren met een gebruiker van de globale beheerder op elke Tenant die u wilt verbinden met Azure Sentinel en machtigingen voor Azure Sentinel te geven om de logboeken te lezen. 
5. Onder de lijst met tenants ziet u de Azure AD-Directory-ID (Tenant-ID) en twee selectie vakjes voor Exchange-en share point-Logboeken. U kunt alle of alle vermelde services selecteren die u wilt opnemen in Sentinel. Op dit moment ondersteunt Azure Sentinel Exchange-en share point-logboeken binnen bestaande Office365-Services.

4. Zodra u de Services (Exchange, share point, enzovoort) hebt geselecteerd, kunt u op Opslaan klikken op het aanvoegings frame van de Tenant op de pagina. 

3. Als u het relevante schema in Log Analytics voor de Office 365-logboeken wilt gebruiken, zoekt u naar **OfficeActivity**.


## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Office 365 kunt verbinden met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).

