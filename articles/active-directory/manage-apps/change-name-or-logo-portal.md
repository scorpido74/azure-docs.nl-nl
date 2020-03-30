---
title: De naam of het logo van een bedrijfstoepassing wijzigen in Azure AD
description: De naam of het logo voor een aangepaste bedrijfstoepassing wijzigen in Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d23849df75d1ab239eb269b462abb21df196e7e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79138498"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>De naam of het logo van een bedrijfstoepassing wijzigen in Azure Active Directory

Het is eenvoudig om de naam of het logo voor een aangepaste bedrijfstoepassing te wijzigen in Azure Active Directory (Azure AD). U moet over de juiste machtigingen beschikken om deze wijzigingen aan te brengen en u moet de maker van de aangepaste toepassing zijn.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>Hoe wijzig ik de naam of het logo van een bedrijfstoepassing?

1. Meld u aan bij de [Azure Active Directory-portal](https://aad.portal.azure.com/) met een account dat een globale beheerder is voor de directory. De pagina **Azure Active Directory-beheercentrum** wordt weergegeven.
2. Selecteer in het linkerdeelvenster **Enterprise-toepassingen**. De lijst met uw bedrijfstoepassingen wordt weergegeven.
3. Selecteer een toepassing. De pagina met toepassingsoverzicht wordt weergegeven.
4. Selecteer **Eigenschappen**in het deelvenster toepassingsoverzicht onder de kop **Beheren** . De pagina **Eigenschappen** wordt weergegeven.
5. Als u de naam wilt wijzigen, selecteert u het vak **Naam,** typt u de nieuwe naam en drukt u op **Enter**.
6. Als u het logo wilt wijzigen, zoekt u het veld **Logo** en selecteert u het mappictogram naast het vak **Een bestand selecteren,** dat onder de huidige logoafbeelding van de toepassing staat.

   ![De eigenschappen selecteren, opdracht](./media/change-name-or-logo-portal/change-logo.png)

   Als u het logo niet wijzigt, gaat u naar stap 8.
7. Selecteer in de bestandenkiezer het gewenste bestand als het nieuwe logo. De naam van het bestand wordt weergegeven in het vak onder de huidige logoafbeelding.

   > [!NOTE]
   > Azure vereist dat de logoafbeelding een PNG-bestand is en het hanteert beperkingen op breedte, hoogte en bestandsgrootte. Aangepaste logo's moeten &times; precies 215 215 pixels groot zijn en in PNG-formaat zijn. We raden u aan een effen kleurachtergrond te gebruiken zonder transparantie in uw toepassingslogo om het beste voor gebruikers te lijken.
8. Selecteer **Opslaan**. Als u een nieuw logo hebt gekozen, wordt **de** afbeelding van het logoveld gewijzigd om het nieuwe logobestand weer te geven.

## <a name="next-steps"></a>Volgende stappen

* [Snelstartgids: Groepen en groepsleden van uw organisatie weergeven in Azure Active Directory](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Een gebruiker of groep toewijzen aan een bedrijfsapp](assign-user-or-group-access-portal.md)
* [Een gebruiker of groepstoewijzing verwijderen uit een bedrijfsapp](remove-user-or-group-access-portal.md)
* [Aanmeldingen van gebruikers voor een bedrijfsapp uitschakelen](disable-user-sign-in-portal.md)
