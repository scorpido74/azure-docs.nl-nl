---
title: Lijst met bulkgroepengroeplidmaatschap - Azure Active Directory-portal | Microsoft Documenten
description: Voeg gebruikers in bulk toe in het Azure-beheercentrum.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 09/11/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e29aacb1357509e2b000a9d05c5ced8f9a30dce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517154"
---
# <a name="bulk-download-members-of-a-group-preview-in-azure-active-directory"></a>Bulkdownloadleden van een groep (voorbeeld) in Azure Active Directory

Met Azure Active Directory (Azure AD)-portal u de leden van een groep in uw organisatie bulk downloaden naar een CSV-bestand (comma-separate values).

## <a name="to-bulk-download-group-membership"></a>Groepslidmaatschap voor bulk downloaden

1. Meld u aan bij [de Azure-portal](https://portal.azure.com) met een gebruikersbeheerdersaccount in de organisatie. Groepseigenaren kunnen ook massaal leden downloaden van groepen waarvan ze eigenaar zijn.
1. Selecteer in Azure AD **Alle** > **groepen groepen**.
1. Open de groep waarvan u het lidmaatschap wilt downloaden en selecteer **vervolgens Leden**.
1. Selecteer **op** de pagina Leden de optie **Leden downloaden** om een CSV-bestand met de groepsleden te downloaden.

   ![De opdracht Leden downloaden staat op de profielpagina voor de groep](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Downloadstatus controleren

U de status van al uw in behandeling zijnde bulkaanvragen bekijken op de pagina **Bulk operation results (preview).**

   ![Op de pagina Resultaten bulkbewerkingen ziet u de status van bulkaanvragen](./media/groups-bulk-download-members/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Limieten voor bulkdownloadservice

Elke bulkactiviteit om een lijst met groepsleden te downloaden, kan maximaal een uur duren. Hiermee u een lijst van ten minste 500.000 leden downloaden.

## <a name="next-steps"></a>Volgende stappen

- [Leden van bulkimportgroepen](groups-bulk-import-members.md)
- [Groepsleden in bulk verwijderen](groups-bulk-download-members.md)
