---
title: Lijst met bulkgroepengroeplidmaatschap - Azure Active Directory-portal | Microsoft Documenten
description: Voeg gebruikers in bulk toe in het Azure-beheercentrum.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3faca8d1a2538ed03a917d6db8d54323fe626369
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533671"
---
# <a name="bulk-download-members-of-a-group-in-azure-active-directory"></a>Bulkdownloadleden van een groep in Azure Active Directory

Met Azure Active Directory (Azure AD)-portal u de leden van een groep in uw organisatie bulk downloaden naar een CSV-bestand (comma-separate values).

## <a name="to-bulk-download-group-membership"></a>Groepslidmaatschap voor bulk downloaden

1. Meld u aan bij [de Azure-portal](https://portal.azure.com) met een gebruikersbeheerdersaccount in de organisatie. Groepseigenaren kunnen ook massaal leden downloaden van groepen waarvan ze eigenaar zijn.
1. Selecteer in Azure AD **Alle** > **groepen groepen**.
1. Open de groep waarvan u het lidmaatschap wilt downloaden en selecteer **vervolgens Leden**.
1. Selecteer **op** de pagina Leden de optie **Leden downloaden** om een CSV-bestand met de groepsleden te downloaden.

   ![De opdracht Leden downloaden staat op de profielpagina voor de groep](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Downloadstatus controleren

U de status van al uw in behandeling zijnde bulkaanvragen bekijken op de pagina **Bulk-bewerkingsresultaten.**

[![](media/groups-bulk-download-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limieten voor bulkdownloadservice

Elke bulkactiviteit om een lijst met groepsleden te downloaden, kan maximaal een uur duren. Hiermee u een lijst van ten minste 500.000 leden downloaden.

## <a name="next-steps"></a>Volgende stappen

- [Leden van bulkimportgroepen](groups-bulk-import-members.md)
- [Groepsleden in bulk verwijderen](groups-bulk-download-members.md)
