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
ms.date: 02/12/2020
ms.author: rkarlin
ms.openlocfilehash: 709961fe63e5ed862a0e8dc3fa735d426dd02998
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371326"
---
# <a name="connect-data-from-office-365-logs"></a>Gegevens verbinden vanuit Office 365-logboeken



U kunt audit logboeken vanuit [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) streamen naar Azure Sentinel met één klik. U kunt audit logboeken vanuit uw Office 365 streamen naar uw Azure Sentinel-werk ruimte op dezelfde Tenant. De connector van het Office 365-activiteiten logboek biedt inzicht in voortdurende gebruikers activiteiten. U krijgt informatie over verschillende acties en gebeurtenissen van de gebruiker, het beheer, het systeem en het beleid van Office 365. Door Office 365-Logboeken in azure Sentinel te koppelen, kunt u deze gegevens gebruiken om Dash boards weer te geven, aangepaste waarschuwingen te maken en uw onderzoek proces te verbeteren.

> [!IMPORTANT]
> Als u een E3-licentie hebt, moet u, voordat u toegang kunt krijgen tot gegevens via de API voor Office 365-beheer activiteit, Unified audit logging inschakelen voor uw Office 365-organisatie. U doet dit door het Office 365-controle logboek in te scha kelen. Zie voor instructies het [Office 365-controle logboek zoeken in-of uitschakelen](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off). Zie de naslag informatie voor de [API voor Office 365-beheer activiteiten](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference).

## <a name="prerequisites"></a>Vereisten

- U moet een globale beheerder of beveiligings beheerder zijn op uw Tenant.
- Als uw Tenant geen Office 365 E3 of Office 365 E5-licentie heeft, moet u Unified auditing inschakelen voor uw Tenant met een van de volgende processen:
    - [Gebruik de cmdlet Set-AdminAuditLogConfig](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) en schakel de para meter ' UnifiedAuditLogIngestionEnabled ' in.
    - [Of met behulp van de beveiligings-en compliancecentrum-gebruikers interface](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin).

## <a name="connect-to-office-365"></a>Verbinding maken met Office 365

1. Selecteer in azure Sentinel **Data connectors** en klik vervolgens op de tegel **Office 365** .

2. Als u dit nog niet hebt ingeschakeld, kunt u dit doen door naar de Blade **Data connectors** te gaan en **Office 365** -connector te selecteren. Hier kunt u op de **pagina connector openen** klikken en onder configuratie sectie met de naam **configuratie** alle Office 365-activiteiten logboeken selecteren die u wilt verbinden met Azure Sentinel. 
   > [!NOTE]
   > Als u al meerdere tenants in een eerder ondersteunde versie van de Office 365-connector in azure Sentinel hebt verbonden, kunt u de logboeken bekijken en wijzigen die u van elke Tenant verzamelt. U kunt geen extra tenants toevoegen, maar u moet eerder toegevoegde tenants verwijderen.
3. Als u het relevante schema in Log Analytics voor de Office 365-logboeken wilt gebruiken, zoekt u naar **OfficeActivity**.


## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Office 365 kunt verbinden met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).

