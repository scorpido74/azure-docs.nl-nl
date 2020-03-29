---
title: Hoe toepassingen worden weergegeven in het toegangspaneel | Microsoft Documenten
description: Problemen oplossen waarom een toepassing wordt weergegeven in het access-paneel
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.reviewr: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fa8ea75cc7fda05326c802c25a91d025b66b5ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65784417"
---
# <a name="how-applications-appear-on-the-access-panel"></a>Hoe toepassingen worden weergegeven in het toegangspaneel

Het Access-paneel is een webportal waarmee een gebruiker met een werk- of schoolaccount in Azure Active Directory (Azure AD) cloudtoepassingen kan weergeven en starten waartoe de Azure AD-beheerder hem toegang heeft verleend. Deze toepassingen zijn geconfigureerd namens de gebruiker in de Azure AD-portal. De beheerder kan de toepassing rechtstreeks aan de gebruiker inrichten of aan een groep waar een gebruiker deel van uitmaakt, waardoor de toepassing wordt weergegeven in het toegangspaneel van de gebruiker.

## <a name="general-issues-to-check-first"></a>Algemene problemen om eerst te controleren

-   Als een toepassing is verwijderd uit een gebruiker of groep waarvan de gebruiker lid is, probeert u zich na enkele minuten opnieuw aan- en uit te loggen in het toegangspaneel van de gebruiker om te zien of de toepassing is verwijderd.

-   Als een licentie is verwijderd uit een gebruiker of groep de gebruiker is een lid van dit kan lang duren, afhankelijk van de grootte en complexiteit van de groep voor wijzigingen worden aangebracht. Geef extra tijd voor het aanmelden bij het toegangspaneel.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problemen met betrekking tot het toewijzen van toepassingen aan gebruikers

Een gebruiker kan een toepassing in het Access-paneel zien omdat deze eerder was toegewezen. Hieronder volgen enkele manieren om te controleren:

-   [Controleren of een gebruiker aan de toepassing is toegewezen](#check-if-a-user-is-assigned-to-the-application)

-   [Controleren of een gebruiker een licentie heeft met betrekking tot de toepassing](#check-if-a-user-is-under-a-license-related-to-the-application)


### <a name="check-if-a-user-is-assigned-to-the-application"></a>Controleren of een gebruiker aan de toepassing is toegewezen

Voer de volgende stappen uit om te controleren of een gebruiker aan de toepassing is toegewezen:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5. klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

6. **Zoek naar** de naam van de betreffende toepassing.

7. klik op **Gebruikers en groepen**.

8. Controleer of uw gebruiker aan de toepassing is toegewezen.

   * Als u de gebruiker uit de toepassing wilt verwijderen, **klikt u op de rij** van de gebruiker en selecteert u **verwijderen**.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Controleren of een gebruiker een licentie heeft met betrekking tot de toepassing

Voer de volgende stappen uit om de toegewezen licenties van een gebruiker te controleren:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Gebruikers en groepen** in het navigatiemenu.

5. klik op **Alle gebruikers**.

6. **Zoek naar** de gebruiker waarin u geïnteresseerd bent en **klik op de rij** om te selecteren.

7. klik op **Licenties** om te zien welke licenties de gebruiker momenteel heeft toegewezen.

   * Als de gebruiker is toegewezen aan een Office-licentie, kunnen First Party Office-toepassingen worden weergegeven in het Toegangspaneel van de gebruiker.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problemen met betrekking tot het toewijzen van toepassingen aan groepen

Een gebruiker kan een toepassing in het Access-paneel zien omdat hij deel uitmaakt van een groep aan de toepassing toegewezen. Hieronder volgen enkele manieren om te controleren:

-   [Groepslidmaatschappen van een gebruiker controleren](#check-a-users-group-memberships)

-   [Controleren of een gebruiker lid is van een groep die is toegewezen aan een licentie](#check-if-a-user-is-a-member-of-a-group-assigned-to-a-license)

### <a name="check-a-users-group-memberships"></a>Groepslidmaatschappen van een gebruiker controleren

Voer de volgende stappen uit om het lidmaatschap van een groep te controleren:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Gebruikers en groepen** in het navigatiemenu.

5. klik op **Alle gebruikers**.

6. **Zoek naar** de gebruiker waarin u geïnteresseerd bent en **klik op de rij** om te selecteren.

7. klik op **Groepen.**

8. Controleer of uw gebruiker deel uitmaakt van een groep die aan de toepassing is toegewezen.

   * Als u de gebruiker uit de groep wilt verwijderen, **klikt u op de rij** van de groep en selecteert u verwijderen.

### <a name="check-if-a-user-is-a-member-of-a-group-assigned-to-a-license"></a>Controleren of een gebruiker lid is van een groep die is toegewezen aan een licentie

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Gebruikers en groepen** in het navigatiemenu.

5. klik op **Alle gebruikers**.

6. **Zoek naar** de gebruiker waarin u geïnteresseerd bent en **klik op de rij** om te selecteren.

7. klik op **Groepen.**

8. klik op de rij van een specifieke groep.

9. Klik op **Licenties** om te zien welke licenties de groep eraan heeft toegewezen.

   * Als de groep is toegewezen aan een Office-licentie, kan dit bepaalde First Party Office-toepassingen in staat stellen om te worden weergegeven in het Toegangspaneel van de gebruiker.


## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Als deze stappen voor het oplossen van problemen het probleem niet oplossen

open een support ticket met de volgende informatie indien beschikbaar:

-   Correlatiefout-id

-   UPN (e-mailadres van de gebruiker)

-   Tenant-id

-   Browsertype

-   Tijdzone en tijd/tijdsbestek tijdens fout treedt op

-   Fiddler sporen

## <a name="next-steps"></a>Volgende stappen
[Toepassingen beheren met Azure Active Directory](what-is-application-management.md)
