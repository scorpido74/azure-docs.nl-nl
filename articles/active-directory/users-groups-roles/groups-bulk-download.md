---
title: Een lijst met groepen downloaden in de Azure Active Directory-portal | Microsoft Documenten
description: Groepseigenschappen in bulk downloaden in het Azure-beheercentrum in Azure Active Directory.
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
ms.openlocfilehash: 59983678c1b14d6aa87a7b500605e3abeb6a9b85
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533532"
---
# <a name="bulk-download-a-list-of-groups-in-azure-active-directory"></a>Bulk download een lijst met groepen in Azure Active Directory

Met Azure Active Directory (Azure AD)-portal u de lijst met alle groepen in uw organisatie downloaden naar een CSV-bestand (comma-separate values).

## <a name="to-download-a-list-of-groups"></a>Een lijst met groepen downloaden

1. Meld u aan bij [de Azure-portal](https://portal.azure.com) met een beheerdersaccount in de organisatie.
1. Selecteer groepen downloaden **groepen** > **in**Azure AD .
1. Selecteer op de **downloadpagina Groepen** de optie **Start** om een CSV-bestand te ontvangen met uw groepen.

   ![De opdracht Downloadgroepen bevindt zich op de pagina Alle groepen](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Downloadstatus controleren

U de status van al uw in behandeling zijnde bulkaanvragen bekijken op de pagina **Bulk-bewerkingsresultaten.**

[![](media/groups-bulk-download/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limieten voor bulkdownloadservice

Elke bulkactiviteit om een groepslijst te downloaden, kan maximaal een uur duren. Hiermee u een lijst van ten minste 300.000 groepen downloaden.

## <a name="next-steps"></a>Volgende stappen

- [Groepsleden in bulk verwijderen](groups-bulk-remove-members.md)
- [Leden van een groep downloaden](groups-bulk-download-members.md)
