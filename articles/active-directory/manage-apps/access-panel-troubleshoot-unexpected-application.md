---
title: Hoe toepassingen worden weer gegeven in het toegangs venster | Microsoft Docs
description: Problemen oplossen bij het openen van een toepassing in het toegangs venster
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "65784417"
---
# <a name="how-applications-appear-on-the-access-panel"></a>Hoe toepassingen worden weer gegeven in het toegangs venster

Het toegangs venster is een webgebaseerde Portal, waarmee een gebruiker met een werk-of school account in Azure Active Directory (Azure AD) Cloud toepassingen kan weer geven en starten waartoe de Azure AD-beheerder hen toegang heeft verleend. Deze toepassingen worden geconfigureerd namens de gebruiker in de Azure AD-Portal. De beheerder kan de toepassing rechtstreeks inrichten voor de gebruiker of voor een groep waarvan een gebruiker deel uitmaakt van de toepassing die wordt weer gegeven in het toegangs venster van de gebruiker.

## <a name="general-issues-to-check-first"></a>Algemene problemen om eerst te controleren

-   Als een toepassing is verwijderd van een gebruiker of groep waarvan de gebruiker lid is, probeert u zich na een paar minuten opnieuw aan te melden en weer in te stellen in het toegangs venster van de gebruiker om te zien of de toepassing is verwijderd.

-   Als een licentie van een gebruiker of groep is verwijderd, kan de gebruiker enige tijd in beslag nemen, afhankelijk van de grootte en complexiteit van de groep voor wijzigingen die moeten worden aangebracht. Sta extra tijd toe voordat u zich aanmeldt bij het toegangs venster.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problemen met betrekking tot het toewijzen van toepassingen aan gebruikers

Een gebruiker ziet mogelijk een toepassing in het toegangs venster, omdat deze eerder is toegewezen. Hieronder vindt u enkele manieren om te controleren:

-   [Controleren of een gebruiker aan de toepassing is toegewezen](#check-if-a-user-is-assigned-to-the-application)

-   [Controleren of een gebruiker een licentie heeft die betrekking heeft op de toepassing](#check-if-a-user-is-under-a-license-related-to-the-application)


### <a name="check-if-a-user-is-assigned-to-the-application"></a>Controleren of een gebruiker aan de toepassing is toegewezen

Voer de volgende stappen uit om te controleren of een gebruiker aan de toepassing is toegewezen:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

6. **Zoek** de naam van de toepassing in kwestie.

7. Klik op **gebruikers en groepen**.

8. Controleer of de gebruiker is toegewezen aan de toepassing.

   * Als u de gebruiker uit de toepassing wilt verwijderen, **klikt u op de rij** van de gebruiker en selecteert u **verwijderen**.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Controleren of een gebruiker een licentie heeft die betrekking heeft op de toepassing

Voer de volgende stappen uit om de toegewezen licenties van een gebruiker te controleren:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **gebruikers en groepen** in het navigatie menu.

5. Klik op **alle gebruikers**.

6. **Zoek** naar de gebruiker die u wilt interesseren en **Klik op de rij om deze** te selecteren.

7. Klik op **licenties** om te zien welke licenties de gebruiker momenteel heeft toegewezen.

   * Als de gebruiker is toegewezen aan een Office-licentie, kunnen de Office-toepassingen van de eerste partij worden weer gegeven in het toegangs venster van de gebruiker.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problemen met betrekking tot het toewijzen van toepassingen aan groepen

Een gebruiker ziet mogelijk een toepassing in het toegangs venster, omdat deze deel uitmaakt van een groep waaraan de toepassing is toegewezen. Hieronder vindt u enkele manieren om te controleren:

-   [De groepslid maatschappen van een gebruiker controleren](#check-a-users-group-memberships)

-   [Controleren of een gebruiker lid is van een groep die is toegewezen aan een licentie](#check-if-a-user-is-a-member-of-a-group-assigned-to-a-license)

### <a name="check-a-users-group-memberships"></a>De groepslid maatschappen van een gebruiker controleren

Voer de volgende stappen uit om het lidmaatschap van een groep te controleren:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **gebruikers en groepen** in het navigatie menu.

5. Klik op **alle gebruikers**.

6. **Zoek** naar de gebruiker die u wilt interesseren en **Klik op de rij om deze** te selecteren.

7. Klik op **groepen.**

8. Controleer of uw gebruiker deel uitmaakt van een groep die is toegewezen aan de toepassing.

   * Als u de gebruiker uit de groep wilt verwijderen, **klikt u op de rij** van de groep en selecteert u verwijderen.

### <a name="check-if-a-user-is-a-member-of-a-group-assigned-to-a-license"></a>Controleren of een gebruiker lid is van een groep die is toegewezen aan een licentie

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **gebruikers en groepen** in het navigatie menu.

5. Klik op **alle gebruikers**.

6. **Zoek** naar de gebruiker die u wilt interesseren en **Klik op de rij om deze** te selecteren.

7. Klik op **groepen.**

8. Klik op de rij van een specifieke groep.

9. Klik op **licenties** om te zien welke licenties de groep hieraan heeft toegewezen.

   * Als de groep is toegewezen aan een Office-licentie, kunnen bepaalde Office-toepassingen van de eerste partij worden weer gegeven in het toegangs venster van de gebruiker.


## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Als deze stappen voor probleem oplossing niet het probleem oplossen

Open een ondersteunings ticket met de volgende informatie, indien beschikbaar:

-   ID correlatie fout

-   UPN (e-mail adres van gebruiker)

-   Tenant-id

-   Browsertype

-   Tijd zone en tijd/tijds duur tijdens fout

-   Fiddler traceringen

## <a name="next-steps"></a>Volgende stappen
[Toepassingen beheren met Azure Active Directory](what-is-application-management.md)
