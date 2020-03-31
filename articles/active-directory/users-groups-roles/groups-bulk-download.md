---
title: Een lijst met groepen downloaden in de Azure Active Directory-portal | Microsoft Documenten
description: Groepseigenschappen in bulk downloaden in het Azure-beheercentrum in Azure Active Directory.
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
ms.openlocfilehash: 4b08e807e179270b63ca81d3777c230c3e129c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517149"
---
# <a name="bulk-download-a-list-of-groups-preview-in-azure-active-directory"></a>Bulk download een lijst met groepen (voorbeeld) in Azure Active Directory

Met Azure Active Directory (Azure AD)-portal u de lijst met alle groepen in uw organisatie downloaden naar een CSV-bestand (comma-separate values).

## <a name="to-download-a-list-of-groups"></a>Een lijst met groepen downloaden

1. Meld u aan bij [de Azure-portal](https://portal.azure.com) met een beheerdersaccount in de organisatie.
1. Selecteer groepen downloaden **groepen** > **in**Azure AD .
1. Selecteer op de **downloadpagina Groepen** de optie **Start** om een CSV-bestand te ontvangen met uw groepen.

   ![De opdracht Downloadgroepen bevindt zich op de pagina Alle groepen](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Downloadstatus controleren

U de status van al uw in behandeling zijnde bulkaanvragen bekijken op de pagina **Bulk operation results (preview).**

   ![Op de pagina Resultaten bulkbewerkingen ziet u de status van bulkaanvragen](./media/groups-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Limieten voor bulkdownloadservice

Elke bulkactiviteit om een groepslijst te downloaden, kan maximaal een uur duren. Hiermee u een lijst van ten minste 300.000 groepen downloaden.

## <a name="next-steps"></a>Volgende stappen

- [Groepsleden in bulk verwijderen](groups-bulk-remove-members.md)
- [Leden van een groep downloaden](groups-bulk-download-members.md)
