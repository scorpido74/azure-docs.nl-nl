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
ms.openlocfilehash: 8ff4234d9fd28e655e868108e37b09c5afe2767c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517123"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Groeps leden bulksgewijs importeren (preview) in Azure Active Directory

Met Azure Active Directory-Portal (Azure AD) kunt u een groot aantal leden toevoegen aan een groep met behulp van een bestand met door komma's gescheiden waarden (CSV) voor het bulk importeren van groeps leden.

## <a name="to-bulk-import-group-members"></a>Groeps leden bulksgewijs importeren

1. Meld u aan bij [de Azure Portal](https://portal.azure.com) met een Administrator-account van de gebruiker in de organisatie. Groeps eigenaren kunnen ook leden van groepen waarvan ze eigenaar zijn, bulksgewijs importeren.
1. Selecteer in azure AD **groepen**  > **alle groepen**.
1. Open de groep waaraan u leden wilt toevoegen en selecteer vervolgens **leden**.
1. Selecteer **leden importeren**op de pagina **leden** .
1. Selecteer op de pagina **groeps leden bulksgewijs importeren (voor beeld)** de optie **downloaden** om de CSV-bestands sjabloon met de vereiste eigenschappen voor de groeps leden op te halen.

    ![De opdracht leden importeren bevindt zich op de profiel pagina voor de groep](./media/groups-bulk-import-members/import-panel.png)

1. Open het CSV-bestand en voeg een regel toe voor elk groepslid dat u wilt importeren in de groep (de vereiste waarden zijn een **Member object-id** of **User Principal name**). Sla het bestand op.

   ![Het CSV-bestand bevat de namen en Id's van de leden die moeten worden geïmporteerd](./media/groups-bulk-import-members/csv-file.png)

1. Blader op de pagina **groeps leden bulksgewijs importeren (preview)** onder **uw CSV-bestand uploaden**naar het bestand. Wanneer u het bestand selecteert, wordt de validatie van het CSV-bestand gestart.
1. Wanneer de bestands inhoud is gevalideerd, wordt het **bestand dat is geüpload**weer gegeven op de pagina Bulk Import. Als er fouten zijn, moet u deze oplossen voordat u de taak kunt indienen.
1. Wanneer de validatie van uw bestand wordt door gegeven, selecteert u **verzenden** om de Azure bulk bewerking te starten waarmee de groeps leden in de groep worden geïmporteerd.
1. Wanneer de import bewerking is voltooid, ziet u een melding dat de bulk bewerking is geslaagd.

## <a name="check-import-status"></a>Import status controleren

U kunt de status van al uw bulk aanvragen in behandeling bekijken op de pagina **resultaten van bulk bewerking (preview)** .

   ![Op de pagina resultaten van bulk bewerking wordt de status van de bulksgewijze aanvraag weer gegeven](./media/groups-bulk-import-members/bulk-center.png)

Selecteer de waarden onder de kolommen **# geslaagd**, **# fout**of **Totaal aantal aanvragen** voor meer informatie over elk regel item in de bulk bewerking. Als er fouten zijn opgetreden, worden de redenen voor de fout weer gegeven.

## <a name="bulk-import-service-limits"></a>Service limieten voor bulk import

Elke bulk activiteit voor het importeren van een lijst met groeps leden kan Maxi maal één uur worden uitgevoerd. Hiermee kan een lijst van ten minste 40.000 leden worden ingevoerd.

## <a name="next-steps"></a>Volgende stappen

- [Groeps leden bulksgewijs verwijderen](groups-bulk-remove-members.md)
- [Leden van een groep downloaden](groups-bulk-download-members.md)
- [Een lijst met alle groepen downloaden](groups-bulk-download.md)
