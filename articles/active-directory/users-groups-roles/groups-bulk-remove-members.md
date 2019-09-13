---
title: Groeps leden bulksgewijs verwijderen door een CSV-bestand te uploaden-Azure Active Directory | Microsoft Docs
description: Voeg gebruikers bulksgewijs toe in het beheer centrum van Azure.
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
ms.openlocfilehash: b153db3570f10ad5ad130dedd0bd20fe22776ed6
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910956"
---
# <a name="bulk-remove-group-members-preview-in-azure-active-directory"></a>Groeps leden bulksgewijs verwijderen (preview) in Azure Active Directory

Met Azure Active Directory-Portal (Azure AD) kunt u een groot aantal leden uit een groep verwijderen door een bestand met door komma's gescheiden waarden (CSV) te gebruiken om groeps leden bulksgewijs te verwijderen.

> [!NOTE]
> Bulk bewerkingen van Azure AD zijn een open bare preview-functie van Azure AD en zijn beschikbaar in elk betaald licentie abonnement voor Azure AD. Zie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de voor waarden voor Preview-versies.

## <a name="bulk-removal-service-limits"></a>Limieten voor bulksgewijs verwijderen van service

Elke bulk activiteit voor het verwijderen van een lijst met groeps leden van kan Maxi maal één uur worden uitgevoerd. Hierdoor kan een lijst van ten minste 40.000 leden worden verwijderd.

## <a name="to-bulk-remove-group-members"></a>Groeps leden bulksgewijs verwijderen

1. Meld u aan bij [de Azure Portal](https://portal.azure.com) met een Administrator-account van de gebruiker in de organisatie. Groeps eigenaren kunnen ook leden verwijderen van de groepen waarvan ze eigenaar zijn.
1. Selecteer **groepen** > **alle groepen**in azure AD.
1. Open de groep waaruit u leden wilt verwijderen en selecteer vervolgens **leden**.
1. Op de pagina **leden** selecteert u **leden verwijderen** om een CSV-bestand te downloaden, bij te werken en te uploaden met een lijst met de leden die u uit de groep wilt verwijderen.

   ![De opdracht leden verwijderen bevindt zich op de profiel pagina voor de groep](./media/groups-bulk-remove-members/remove-panel.png)

## <a name="check-removal-status"></a>Verwijderings status controleren

U kunt de status van al uw bulk aanvragen in behandeling bekijken op de pagina **resultaten van bulk bewerking (preview)** .

   ![Op de pagina resultaten van bulk bewerking wordt de status van de bulksgewijze aanvraag weer gegeven](./media/groups-bulk-remove-members/bulk-center.png)

## <a name="next-steps"></a>Volgende stappen

- [Groeps leden bulksgewijs importeren](groups-bulk-import-members.md)
- [Leden van een groep downloaden](groups-bulk-download-members.md)
