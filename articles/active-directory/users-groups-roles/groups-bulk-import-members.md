---
title: Uploads bulksgewijs importeren om leden toe te voegen aan een groep-Azure Active Directory | Microsoft Docs
description: Voeg groeps leden in bulk toe in het Azure Active Directory-beheer centrum.
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
ms.openlocfilehash: 0335522b398c22fb395305b33bac5a56ba565ee2
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910931"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Groeps leden bulksgewijs importeren (preview) in Azure Active Directory

Met Azure Active Directory-Portal (Azure AD) kunt u een groot aantal leden toevoegen aan een groep met behulp van een bestand met door komma's gescheiden waarden (CSV) voor het bulk importeren van groeps leden.

> [!NOTE]
> Bulk bewerkingen van Azure AD zijn een open bare preview-functie van Azure AD en zijn beschikbaar in elk betaald licentie abonnement voor Azure AD. Zie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de voor waarden voor Preview-versies.

## <a name="bulk-import-service-limits"></a>Service limieten voor bulk import

Elke bulk activiteit voor het importeren van een lijst met groeps leden kan Maxi maal één uur worden uitgevoerd. Hiermee kan een lijst van ten minste 40.000 leden worden ingevoerd.

## <a name="to-bulk-import-group-members"></a>Groeps leden bulksgewijs importeren

1. Meld u aan bij [de Azure Portal](https://portal.azure.com) met een Administrator-account van de gebruiker in de organisatie. Groeps eigenaren kunnen ook leden van groepen waarvan ze eigenaar zijn, bulksgewijs importeren.
1. Selecteer **groepen** > **alle groepen**in azure AD.
1. Open de groep waaraan u leden wilt toevoegen en selecteer vervolgens **leden**.
1. Selecteer op de pagina **leden** de optie **leden importeren** om een CSV-bestand te downloaden, bij te werken en te uploaden met een lijst met de leden die u wilt importeren in de groep.

   ![De opdracht leden importeren bevindt zich op de profiel pagina voor de groep](./media/groups-bulk-import-members/import-panel.png)

## <a name="check-import-status"></a>Import status controleren

U kunt de status van al uw bulk aanvragen in behandeling bekijken op de pagina **resultaten van bulk bewerking (preview)** .

   ![Op de pagina resultaten van bulk bewerking wordt de status van de bulksgewijze aanvraag weer gegeven](./media/groups-bulk-import-members/bulk-center.png)

## <a name="next-steps"></a>Volgende stappen

- [Groeps leden bulksgewijs verwijderen](groups-bulk-remove-members.md)
- [Leden van een groep downloaden](groups-bulk-download-members.md)
