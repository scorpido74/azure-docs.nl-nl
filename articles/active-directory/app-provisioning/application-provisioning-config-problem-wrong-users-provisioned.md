---
title: Er is een onjuiste set gebruikers ingericht voor een Azure AD Gallery-app
description: Meer informatie over hoe u kunt nagaan waarom een andere set gebruikers wordt ingericht voor een toepassing dan de toepassingen die u verwacht
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/20/2018
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 5a109f1a06e7ee8aff3e455c009217ff670d2781
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/15/2020
ms.locfileid: "84782208"
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Er wordt een verkeerde set gebruikers ingericht voor een Azure AD-galerie toepassing

Welke gebruikers worden ingericht voor de app, worden voornamelijk bestuurd door de gebruikers en groepen die zijn **toegewezen** aan de toepassing.

Gebruik de volgende bronnen om te leren hoe u kunt controleren welke gebruikers en groepen zijn toegewezen aan een toepassing in Azure Active Directory.

## <a name="assign-a-user-directly-as-an-administrator"></a>Een gebruiker rechtstreeks toewijzen als beheerder

Voer de volgende stappen uit om een of meer gebruikers rechtstreeks toe te wijzen aan een toepassing:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6. Selecteer in de lijst de toepassing waaraan u een gebruiker wilt toewijzen.

7. Zodra de toepassing is geladen, klikt u op **gebruikers en groepen** in het navigatie menu aan de linkerkant van de toepassing.

8. Als u het deel venster **toewijzing toevoegen** wilt openen, klikt u op de knop **toevoegen** boven aan de lijst **gebruikers en groepen** .

9. Klik op de selector **gebruikers en groepen** in het deel venster **toewijzing toevoegen** .

10. Typ de **volledige naam** of het **e-mail adres** van de gebruiker die u wilt toewijzen in het zoekvak **zoeken op naam of e-mail adres** .

11. Beweeg de muis aanwijzer over de **gebruiker** in de lijst om een **selectie vakje**weer te geven. Klik op het selectie vakje naast de foto of het logo van het profiel van de gebruiker om uw gebruiker toe te voegen aan de **geselecteerde** lijst.

12. **Optioneel:** Als u **meer dan één gebruiker wilt toevoegen**, typt u een andere **volledige naam** of een ander **e-mail adres** in het zoekvak **zoeken op naam of e-mail adres** en klikt u op het selectie vakje om deze gebruiker aan de **geselecteerde** lijst toe te voegen.

13. Wanneer u klaar bent met het selecteren van gebruikers, klikt u op de knop **selecteren** om ze toe te voegen aan de lijst met gebruikers en groepen die aan de toepassing moeten worden toegewezen.

14. **Optioneel:** Klik op het selectie vakje **rol** selecteren in het deel venster **toewijzing toevoegen** om een rol te selecteren die u wilt toewijzen aan de gebruikers die u hebt geselecteerd.

15. Klik op de knop **toewijzen** om de toepassing aan de geselecteerde gebruikers toe te wijzen.

Als inrichten is geconfigureerd en al wordt uitgevoerd voor een app, moeten nieuwe gebruikers in ongeveer 10 minuten worden ingericht voor een toepassing. Raadpleeg de **controle logboeken** voor meer informatie.

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Een groep rechtstreeks aan een toepassing toewijzen als beheerder

Voer de volgende stappen uit om een of meer groepen rechtstreeks toe te wijzen aan een toepassing:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6. Selecteer in de lijst de toepassing waaraan u een gebruiker wilt toewijzen.

7. Zodra de toepassing is geladen, klikt u op **gebruikers en groepen** in het navigatie menu aan de linkerkant van de toepassing.

8. Als u het deel venster **toewijzing toevoegen** wilt openen, klikt u op de knop **toevoegen** boven aan de lijst **gebruikers en groepen** .

9. Klik op de selector **gebruikers en groepen** in het deel venster **toewijzing toevoegen** .

10. Typ de **volledige groeps naam** van de groep die u wilt toewijzen in het zoekvak **zoeken op naam of e-mail adres** .

11. Beweeg de muis aanwijzer over de **groep** in de lijst om een **selectie vakje**weer te geven. Klik op het selectie vakje naast het profiel foto of logo van de groep om uw gebruiker toe te voegen aan de **geselecteerde** lijst.

12. **Optioneel:** Als u **meer dan één groep wilt toevoegen**, typt u een andere **volledige groeps naam** in het zoekvak **zoeken op naam of e-mail adres** en klikt u op het selectie vakje om deze groep aan de **geselecteerde** lijst toe te voegen.

13. Wanneer u klaar bent met het selecteren van groepen, klikt u op de knop **selecteren** om ze toe te voegen aan de lijst met gebruikers en groepen die aan de toepassing moeten worden toegewezen.

14. **Optioneel:** Klik op het selectie vakje **rol** selecteren in het deel venster **toewijzing toevoegen** om een rol te selecteren die u wilt toewijzen aan de groepen die u hebt geselecteerd.

15. Klik op de knop **toewijzen** om de toepassing toe te wijzen aan de geselecteerde groepen.

Als inrichten is geconfigureerd en al wordt uitgevoerd voor een app, moeten nieuwe gebruikers in de groep ongeveer 10 minuten worden ingericht voor een toepassing. Raadpleeg de **controle logboeken** voor meer informatie.

>[!IMPORTANT]
>Het inrichten van de groeps naam en groeps gegevens, naast de leden, als deze worden ondersteund voor sommige toepassingen. U kunt deze functie in-of uitschakelen door de **toewijzing** voor groeps objecten die op het tabblad **inrichten** wordt weer gegeven in of uit te scha kelen. 
>
>

Als inrichtings groepen is ingeschakeld, moet u de kenmerk toewijzingen controleren om ervoor te zorgen dat er een geschikt veld wordt gebruikt voor de ' overeenkomende ID '. Deze overeenkomende ID kan de weergave naam of e-mail alias zijn. De groep en de bijbehorende leden worden niet ingericht als de overeenkomende eigenschap leeg is of niet is ingevuld voor een groep in azure AD.

## <a name="next-steps"></a>Volgende stappen
[Automate User Provisioning and Deprovisioning to SaaS Applications with Azure Active Directory](user-provisioning.md) (Automatisch gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory)
