---
title: Verkeerde set gebruikers is ingericht in een Azure AD Gallery-app
description: Meer informatie over hoe u weten waarom een andere groep gebruikers wordt ingericht voor een toepassing dan u zou verwachten
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c94388011605da73666e82011bb8ef56d2af8d30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522778"
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Verkeerde set gebruikers wordt ingericht voor een Azure AD Gallery-toepassing

Welke gebruikers zijn ingericht op de app wordt voornamelijk aangestuurd door welke gebruikers en groepen zijn **toegewezen aan** de toepassing.

Gebruik de volgende bronnen om te zien hoe u controleren welke gebruikers en groepen zijn toegewezen aan een toepassing in Azure Active Directory.

## <a name="assign-a-user-directly-as-an-administrator"></a>Een gebruiker rechtstreeks als beheerder toewijzen

Voer de volgende stappen uit om een of meer gebruikers rechtstreeks aan een toepassing toe te wijzen:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5. klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de gewenste toepassing hier niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen.**

6. Selecteer de toepassing waaraan u een gebruiker wilt toewijzen uit de lijst.

7. Zodra de toepassing wordt geladen, klikt u op **Gebruikers en groepen** in het linkernavigatiemenu van de toepassing.

8. Als u het deelvenster Toewijzing toevoegen wilt **openen,** klikt u op de knop **Toevoegen** boven aan de lijst **Gebruikers en groepen.**

9. klik op de selectie **voor gebruikers en groepen** in het deelvenster Toewijzing **toevoegen.**

10. Typ de **volledige naam** of het volledige **e-mailadres** van de gebruiker die u wilt toewijzen aan het zoekvak Zoeken op naam of **e-mailadres.**

11. Plaats de **plaats van** de gebruiker in de lijst om een **selectievakje**te onthullen. Klik op het selectievakje naast de profielfoto of het logo van de gebruiker om uw gebruiker toe te voegen aan de **lijst Geselecteerde.**

12. **Optioneel:** Als u meer dan één gebruiker wilt **toevoegen,** typt u een andere **volledige naam** of **e-mailadres** in het zoekvak **Zoeken op naam of e-mailadres** en klikt u op het selectievakje om deze gebruiker toe te voegen aan de **lijst Geselecteerde.**

13. Wanneer u klaar bent met het selecteren van gebruikers, klikt u op de knop **Selecteren** om deze toe te voegen aan de lijst met gebruikers en groepen die aan de toepassing moeten worden toegewezen.

14. **Optioneel:** klik op de rolkiezer **selecteren** in het deelvenster **Toewijzing toevoegen** om een rol te selecteren die u wilt toewijzen aan de gebruikers die u hebt geselecteerd.

15. Klik **op** de knop Toewijzen om de toepassing toe te wijzen aan de geselecteerde gebruikers.

Als de inrichting is geconfigureerd en al wordt uitgevoerd voor een app, moeten nieuwe gebruikers in ongeveer 10 minuten worden ingericht op een toepassing. Controleer de **controlelogboeken** voor meer informatie.

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Een groep rechtstreeks toewijzen aan een toepassing als beheerder

Voer de volgende stappen uit om een of meer groepen rechtstreeks aan een toepassing toe te wijzen:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5. klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de gewenste toepassing hier niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen.**

6. Selecteer de toepassing waaraan u een gebruiker wilt toewijzen uit de lijst.

7. Zodra de toepassing wordt geladen, klikt u op **Gebruikers en groepen** in het linkernavigatiemenu van de toepassing.

8. Als u het deelvenster Toewijzing toevoegen wilt **openen,** klikt u op de knop **Toevoegen** boven aan de lijst **Gebruikers en groepen.**

9. klik op de selectie **voor gebruikers en groepen** in het deelvenster Toewijzing **toevoegen.**

10. Typ de **volledige groepsnaam** van de groep die u wilt toewijzen aan het zoekvak **Zoeken op naam of e-mailadres.**

11. Plaats de **plaats in** de groep in de lijst om een **selectievakje**te onthullen. Klik op het selectievakje naast de profielfoto of het logo van de groep om uw gebruiker toe te voegen aan de **lijst Geselecteerd.**

12. **Optioneel:** Als u meer dan één groep wilt **toevoegen,** typt u een andere **volledige groepsnaam** in het zoekvak **Zoeken op naam of e-mailadres** en klikt u op het selectievakje om deze groep toe te voegen aan de lijst **Geselecteerd.**

13. Wanneer u klaar bent met het selecteren van groepen, klikt u op de knop **Selecteren** om ze toe te voegen aan de lijst met gebruikers en groepen die aan de toepassing moeten worden toegewezen.

14. **Optioneel:** klik op de rolkiezer **selecteren** in het deelvenster **Toewijzing toevoegen** om een rol te selecteren die u wilt toewijzen aan de groepen die u hebt geselecteerd.

15. Klik **op** de knop Toewijzen om de toepassing toe te wijzen aan de geselecteerde groepen.

Als de inrichting is geconfigureerd en al wordt uitgevoerd voor een app, moeten nieuwe gebruikers in de groep in ongeveer 10 minuten worden ingericht op een toepassing. Controleer de **controlelogboeken** voor meer informatie.

>[!IMPORTANT]
>Inrichten van de groepsnaam en groepsgegevens, naast de leden, indien ondersteund voor sommige toepassingen. U deze functionaliteit in- of uitschakelen door de **toewijzing** voor groepsobjecten in te schakelen of uit te schakelen die worden weergegeven op het tabblad **Inrichten.** 
>
>

Als inrichtingsgroepen is ingeschakeld, moet u de kenmerktoewijzingen controleren om ervoor te zorgen dat een geschikt veld wordt gebruikt voor de 'overeenkomende id'. Deze overeenkomende ID kan de weergavenaam of e-mailalias zijn. De groep en haar leden zijn niet ingericht als de overeenkomende eigenschap leeg is of niet wordt ingevuld voor een groep in Azure AD.

## <a name="next-steps"></a>Volgende stappen
[Automate User Provisioning and Deprovisioning to SaaS Applications with Azure Active Directory](user-provisioning.md) (Automatisch gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory)
