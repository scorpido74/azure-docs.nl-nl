---
title: Office 365-gegevens verbinden met Azure Sentinel| Microsoft Documenten
description: Meer informatie over het verbinden van Office 365-gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/12/2020
ms.author: yelevin
ms.openlocfilehash: 43eba727b1dc724aae6eea3ec77de1363c5db73f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252520"
---
# <a name="connect-data-from-office-365-logs"></a>Gegevens verbinden vanuit Office 365-logboeken



U met één klik controlelogboeken van [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) naar Azure Sentinel streamen. U controlelogboeken van uw Office 365 streamen naar uw Azure Sentinel-werkruimte op dezelfde tenant. De Office 365-activiteitslogboekconnector biedt inzicht in lopende gebruikersactiviteiten. U krijgt informatie over verschillende acties en gebeurtenissen van gebruikers, beheerders, systemen en beleidsregels van Office 365. Door Office 365-logboeken aan te sluiten op Azure Sentinel u deze gegevens gebruiken om dashboards te bekijken, aangepaste waarschuwingen te maken en uw onderzoeksproces te verbeteren.

> [!IMPORTANT]
> Als u een E3-licentie hebt, moet u, voordat u toegang hebt tot gegevens via de Office 365 Management Activity API, uniforme controlelogboekregistratie inschakelen voor uw Office 365-organisatie. U doet dit door het controlelogboek van Office 365 in te schakelen. Zie Office [365-controlelogboekzoeken in- of uitschakelen](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off)voor instructies. Zie referentie van [de Office 365-beheeractiviteitsAPI voor](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)meer informatie.

## <a name="prerequisites"></a>Vereisten

- U moet een globale beheerder of beveiligingsbeheerder op uw tenant zijn.
- Uw tenant moet uniforme controle hebben ingeschakeld. Tenants met Office 365 E3- of E5-licenties hebben standaard uniforme controle ingeschakeld. <br>Als uw tenant niet over een van deze licenties beschikt, moet u uniforme controle op uw tenant inschakelen met behulp van een van de volgende methoden:
    - [Met behulp van de cmdlet Set-AdminAuditConFig](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) en schakel de parameter "UnifiedAuditLogIngestionEnabled" in).
    - [Het gebruikersinterfacecentrum voor beveiligings- & compliancecentrum gebruiken.](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin)

## <a name="connect-to-office-365"></a>Verbinding maken met Office 365

1. Selecteer in Azure Sentinel **gegevensconnectors** en klik vervolgens op de **tegel Office 365.**

2. Als u dit nog niet hebt ingeschakeld, u dit doen door naar het blade **van Gegevensconnectoren** te gaan en Office **365-connector** te selecteren. Klik hier op de **pagina Connector openen** en selecteer onder configuratiesectie met het label **Configuratie** alle Office 365-activiteitslogboeken die u met Azure Sentinel wilt verbinden. 
   > [!NOTE]
   > Als u al meerdere tenants hebt aangesloten in een eerder ondersteunde versie van de Office 365-connector in Azure Sentinel, u bekijken en wijzigen welke logboeken u van elke tenant verzamelt. U geen extra tenants toevoegen, maar u eerder toegevoegde tenants verwijderen.
3. Als u het relevante schema wilt gebruiken in Logboekanalyse voor de Office 365-logboeken, zoekt u naar **OfficeActiviteit**.


## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Office 365 verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats-built-in.md)

