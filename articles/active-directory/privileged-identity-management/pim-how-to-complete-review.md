---
title: Toegangscontrole van Azure AD-rollen voltooien in PIM - Azure AD | Microsoft Documenten
description: Meer informatie over het voltooien van een toegangscontrole van Azure AD-rollen in Azure AD Privileged Identity Management (PIM) en de resultaten bekijken
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe2d85d605b9ee418a5709ddcdb448c56be1d918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022286"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Een toegangscontrole van Azure AD-rollen voltooien in Privileged Identity Management

Beheerders van bevoorrechte rollen kunnen bevoorrechte toegang controleren zodra een [toegangscontrole is gestart](pim-how-to-start-security-review.md).  Privileged Identity Management (PIM) stuurt automatisch een e-mail naar gebruikers in uw Azure Active Directory(Azure AD)-organisatie met het verzoek hun toegang te controleren. Als een gebruiker geen e-mail heeft ontvangen, u deze instructies sturen voor [het uitvoeren van een toegangscontrole.](pim-how-to-perform-security-review.md)

Nadat de periode voor toegangscontrole is afgelopen of alle gebruikers klaar zijn met hun zelfbeoordeling, volgt u de stappen in dit artikel om de beoordeling te beheren en de resultaten te bekijken.

## <a name="manage-access-reviews"></a>Toegangsbeoordelingen beheren

1. Ga naar de [Azure-portal](https://portal.azure.com/) en selecteer de Azure **AD Privileged Identity Management-service** op uw dashboard.
1. Selecteer het gedeelte **Toegangsrecensies** van het dashboard.
1. Selecteer de toegangscontrole die u wilt beheren.

Op het detailblad van de toegangscontrole zijn er een aantal opties voor het beheren van die beoordeling.

![Knoppen voor toegangsbeoordeling voor privileged Identity Management - schermafbeelding](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>Herinneren

Als een toegangscontrole is ingesteld zodat de gebruikers zichzelf kunnen controleren, stuurt de knop **Herinneren** een melding.

### <a name="stop"></a>Stoppen

Alle toegangsbeoordelingen hebben een einddatum, maar u de knop **Stoppen** gebruiken om deze eerder af te maken. Als gebruikers op dit moment nog niet zijn beoordeeld, kunnen ze dat niet meer nadat je de beoordeling hebt gestopt. U een beoordeling niet opnieuw starten nadat deze is gestopt.

### <a name="apply"></a>Toepassen

Nadat een toegangscontrole is voltooid, omdat u de einddatum hebt bereikt of handmatig hebt gestopt, implementeert de knop **Toepassen** de uitkomst van de beoordeling. Als de toegang van een gebruiker in de beoordeling is geweigerd, is dit de stap waarmee de roltoewijzing wordt verwijderd.  

### <a name="export"></a>Exporteren

Als u de resultaten van de toegangscontrole handmatig wilt toepassen, u de beoordeling exporteren. Met de knop **Exporteren** wordt een CSV-bestand gedownload. U de resultaten beheren in Excel of andere programma's die CSV-bestanden openen.

### <a name="delete"></a>Verwijderen

Als u niet geïnteresseerd bent in de beoordeling verder, verwijder het. Met de knop **Verwijderen** wordt de revisie verwijderd uit de service Bevoegde identiteitsbeheer.

> [!IMPORTANT]
> U hoeft deze destructieve wijziging niet te bevestigen, dus controleer of u die beoordeling wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

- [Een toegangscontrole voor Azure AD-rollen starten in Privileged Identity Management](pim-how-to-start-security-review.md)
- [Een toegangscontrole uitvoeren van mijn Azure AD-rollen in Privileged Identity Management](pim-how-to-perform-security-review.md)
