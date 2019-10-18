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
ms.openlocfilehash: 9d384ea4749e2d0bc7edf8df7ac0508566f2f76b
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517106"
---
# <a name="bulk-remove-group-members-preview-in-azure-active-directory"></a>Groeps leden bulksgewijs verwijderen (preview) in Azure Active Directory

Met Azure Active Directory-Portal (Azure AD) kunt u een groot aantal leden uit een groep verwijderen door een bestand met door komma's gescheiden waarden (CSV) te gebruiken om groeps leden bulksgewijs te verwijderen.

## <a name="to-bulk-remove-group-members"></a>Groeps leden bulksgewijs verwijderen

1. Meld u aan bij [de Azure Portal](https://portal.azure.com) met een Administrator-account van de gebruiker in de organisatie. Groeps eigenaren kunnen ook leden verwijderen van de groepen waarvan ze eigenaar zijn.
1. Selecteer in azure AD **groepen**  > **alle groepen**.
1. Open de groep waaruit u leden wilt verwijderen en selecteer vervolgens **leden**.
1. Selecteer **leden verwijderen**op de pagina **leden** .
1. Selecteer op de pagina **groeps leden bulksgewijs verwijderen (voor beeld)** de optie **downloaden** om de CSV-bestands sjabloon met de vereiste eigenschappen voor de groeps leden op te halen.

   ![De opdracht leden verwijderen bevindt zich op de profiel pagina voor de groep](./media/groups-bulk-remove-members/remove-panel.png)

1. Open het CSV-bestand en voeg een regel toe voor elk groepslid dat u wilt verwijderen uit de groep (de vereiste waarden zijn lid van de object-ID of User Principal Name). Sla het bestand op.

   ![Het CSV-bestand bevat de namen en Id's van de leden die u wilt verwijderen](./media/groups-bulk-remove-members/csv-file.png)

1. Blader op de pagina **groeps leden bulksgewijs verwijderen (preview)** onder **uw CSV-bestand uploaden**naar het bestand. Wanneer u het bestand selecteert, wordt de validatie van het CSV-bestand gestart.
1. Wanneer de bestands inhoud is gevalideerd, wordt het **bestand dat is geüpload**weer gegeven op de pagina Bulk Import. Als er fouten zijn, moet u deze oplossen voordat u de taak kunt indienen.
1. Wanneer de validatie van uw bestand wordt door gegeven, selecteert u **verzenden** om de Azure-bulk bewerking te starten waarmee de groeps leden uit de groep worden verwijderd.
1. Wanneer het verwijderen is voltooid, ziet u een melding dat de bulk bewerking is geslaagd.

## <a name="check-removal-status"></a>Verwijderings status controleren

U kunt de status van al uw bulk aanvragen in behandeling bekijken op de pagina **resultaten van bulk bewerking (preview)** .

   ![Op de pagina resultaten van bulk bewerking wordt de status van de bulksgewijze aanvraag weer gegeven](./media/groups-bulk-remove-members/bulk-center.png)

Selecteer de waarden onder de kolommen **# geslaagd**, **# fout**of **Totaal aantal aanvragen** voor meer informatie over elk regel item in de bulk bewerking. Als er fouten zijn opgetreden, worden de redenen voor de fout weer gegeven.

## <a name="bulk-removal-service-limits"></a>Limieten voor bulksgewijs verwijderen van service

Elke bulk activiteit voor het verwijderen van een lijst met groeps leden van kan Maxi maal één uur worden uitgevoerd. Hierdoor kan een lijst van ten minste 40.000 leden worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

- [Groeps leden bulksgewijs importeren](groups-bulk-import-members.md)
- [Leden van een groep downloaden](groups-bulk-download-members.md)
- [Een lijst met alle groepen downloaden](groups-bulk-download.md)
