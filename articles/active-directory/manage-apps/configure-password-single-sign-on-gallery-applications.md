---
title: Eenmalige aanmelding met een wacht woord configureren voor een toepassing in de Azure AD-galerie | Microsoft Docs
description: Een toepassing configureren voor een veilige, op wacht woord gebaseerde eenmalige aanmelding wanneer deze al wordt vermeld in de Azure AD-toepassings galerie
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
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: 9fb33c4110a590539c85364885da9a27853f6bd0
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70146890"
---
# <a name="configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Eenmalige aanmelding met een wacht woord configureren voor een toepassing in de Azure AD-galerie

Wanneer u een toepassing toevoegt vanuit de [Azure Active Directory (Azure AD)-toepassings galerie](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis), kunt u kiezen hoe u wilt dat uw gebruikers zich aanmelden bij die toepassing. U kunt deze keuze op elk gewenst moment configureren door **eenmalige aanmelding** te selecteren in een bedrijfs toepassing in de [Azure Portal](https://portal.azure.com/).

Een van de beschik bare opties voor eenmalige aanmelding (SSO) is een [eenmalige aanmelding op basis van wacht woorden](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). Dit is een uitstekende manier om snel toepassingen te integreren in azure AD. Hiermee schakelt u het volgende in:

-   Gebruikers namen en wacht woorden veilig opslaan en herhalen voor de toepassing die u hebt geïntegreerd met Azure AD

-   Biedt ondersteuning voor toepassingen waarvoor meerdere aanmeldings velden zijn vereist dan alleen de velden gebruikers naam en wacht woord

-   Hiermee kunt u de labels aanpassen van de gebruikers naam en het wacht woord die uw gebruikers zien op het [toegangs paneel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) van de toepassing wanneer ze hun referenties invoeren

-   Hiermee kunnen uw gebruikers hun eigen gebruikers namen en wacht woorden opgeven voor bestaande toepassings accounts die ze hand matig invoeren op het [toegangs paneel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) van de toepassing

-   Hiermee kan een lid van de bedrijfs groep de functie [selfservice toepassing](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) gebruiken om de gebruikers namen en wacht woorden op te geven die aan een gebruiker zijn toegewezen

-   Hiermee kan een beheerder de gebruikers namen en wacht woorden opgeven die aan een gebruiker zijn toegewezen met behulp van de functie referenties bijwerken wanneer ze [een gebruiker aan een toepassing toewijzen](#assign-a-user-to-an-application-directly)

-   Hiermee kan een beheerder de functie voor het bijwerken van referenties gebruiken om de gedeelde gebruikers naam of het wacht woord voor een groep personen op te geven wanneer ze [een groep aan een toepassing toewijzen](#assign-an-application-to-a-group-directly)

In de volgende sectie wordt beschreven hoe u [eenmalige aanmelding op basis van wacht woorden](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) kunt inschakelen voor een toepassing die zich al in de [Azure AD-toepassings galerie](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)bevindt.

## <a name="overview-of-required-steps"></a>Overzicht van de vereiste stappen
Als u een toepassing wilt configureren vanuit de Azure AD-galerie, moet u het volgende doen:

-   [Een toepassing toevoegen vanuit de Azure AD-galerie](#add-an-application-from-the-azure-ad-gallery)

-   [De toepassing configureren voor eenmalige aanmelding met een wacht woord](#configure-the-application-for-password-single-sign-on)

-   De toepassing toewijzen aan een gebruiker of een groep:

    -   [Een gebruiker rechtstreeks aan een toepassing toewijzen](#assign-a-user-to-an-application-directly)

    -   [Een toepassing rechtstreeks toewijzen aan een groep](#assign-an-application-to-a-group-directly)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Een toepassing toevoegen vanuit de Azure AD-galerie

Voer de volgende stappen uit om een toepassing toe te voegen vanuit de Azure AD-galerie:

1.  Open de [Azure Portal](https://portal.azure.com)en meld u aan als **globale beheerder** of **co-** beheerder.

2.  Open de **uitbrei ding Azure Active Directory** door **alle services** bovenaan het menu aan de linkerkant te selecteren.

3.  Voer **Azure Active Directory** in het zoekvak in en selecteer vervolgens het **Azure Active Directory** item.

4.  Selecteer **bedrijfs toepassingen** in het menu van Azure AD aan de linkerkant.

5.  Selecteer de knop **toevoegen** in de rechter bovenhoek van het deel venster **ondernemings toepassingen** .

6.  Voer de naam van de toepassing in in het vak **een naam invoeren** in de **Galerie toevoegen** .

7.  Selecteer de toepassing die u wilt configureren voor eenmalige aanmelding.

8.  Voordat u de toepassing toevoegt, kunt u de naam wijzigen in het vak **naam** .

9.  Selecteer **toevoegen** om de toepassing toe te voegen.

Na een korte periode kunt u het configuratie venster van de toepassing bekijken.

## <a name="configure-the-application-for-password-single-sign-on"></a>De toepassing configureren voor eenmalige aanmelding met een wacht woord

Voer de volgende stappen uit om eenmalige aanmelding voor een toepassing te configureren:

1. Open de [Azure Portal](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-** beheerder.

2. Open de **uitbrei ding Azure Active Directory** door **alle services** bovenaan het menu aan de linkerkant te selecteren.

3. Voer **Azure Active Directory** in het zoekvak in en selecteer vervolgens het **Azure Active Directory** item.

4. Selecteer **bedrijfs toepassingen** in het menu Azure Active Directory aan de linkerkant.

5. Selecteer **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   Als u de gewenste toepassing niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst toepassingen**en stelt u de optie voor het **weer geven** van **alle toepassingen**in.

6. Selecteer de toepassing die u wilt configureren voor eenmalige aanmelding.

7. Nadat de toepassing is geladen, selecteert u **eenmalige aanmelding** in het menu van de toepassing aan de linkerkant.

8. Selecteer **aanmeldings modus op basis van wacht woorden** .

9. [Gebruikers toewijzen aan de toepassing](#assign-a-user-to-an-application-directly).

10. U kunt ook referenties namens gebruikers opgeven door de rij van een gebruiker te selecteren, **referenties bijwerken**te selecteren en vervolgens de gebruikers naam en het wacht woord in te voeren. Anders wordt gebruikers gevraagd om hun referenties in te voeren wanneer ze de toepassing starten.

## <a name="assign-a-user-to-an-application-directly"></a>Een gebruiker rechtstreeks aan een toepassing toewijzen

Voer de volgende stappen uit om een of meer gebruikers rechtstreeks toe te wijzen aan een toepassing:

1. Open de [Azure Portal](https://portal.azure.com/) en meld u aan als **globale beheerder**.

2. Open de **uitbrei ding Azure Active Directory** door **alle services** bovenaan het menu aan de linkerkant te selecteren.

3. Voer **Azure Active Directory** in het zoekvak in en selecteer vervolgens het **Azure Active Directory** item.

4. Selecteer **bedrijfs toepassingen** in het menu Azure Active Directory aan de linkerkant.

5. Selecteer **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   Als u de gewenste toepassing niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst toepassingen**en stelt u de optie voor het **weer geven** van **alle toepassingen**in.

6. Selecteer de toepassing waaraan u een gebruiker wilt toewijzen.

7. Nadat de toepassing is geladen, selecteert u **gebruikers en groepen** in het menu van de toepassing aan de linkerkant.

8. Selecteer de knop **toevoegen** boven aan de lijst **gebruikers en groepen** om het deel venster **toewijzing toevoegen** te openen.

9. Selecteer **gebruikers en groepen** in het deel venster **toewijzing toevoegen** .

10. Voer de volledige naam of het e-mail adres van de gebruiker in het zoekvak **zoeken op naam of e-mail adres** in.

11. Beweeg de muis aanwijzer over de gebruiker in de lijst en schakel vervolgens het selectie vakje naast de foto of het logo van het profiel van de gebruiker in om ze toe te voegen aan de **geselecteerde** lijst.

12. Optioneel: Als u meer dan één gebruiker wilt toevoegen, voert u een volledige naam of e-mail adres in het vak **zoeken op naam of e-mail adres** in en schakelt u het selectie vakje voor die gebruiker in om deze aan de **geselecteerde** lijst toe te voegen.

13. Wanneer u klaar bent met het selecteren van gebruikers, gebruikt u de knop **selecteren** om ze toe te voegen aan de lijst met gebruikers en groepen die aan de toepassing moeten worden toegewezen.

14. Optioneel: Gebruik de opdracht **rol selecteren** in het deel venster **toewijzing toevoegen** om een rol te selecteren die u wilt toewijzen aan de gebruikers die u hebt geselecteerd.

15. Selecteer **toewijzen** om de toepassing toe te wijzen aan de geselecteerde gebruikers.

## <a name="assign-an-application-to-a-group-directly"></a>Een toepassing rechtstreeks toewijzen aan een groep

Voer de volgende stappen uit om een of meer groepen rechtstreeks toe te wijzen aan een toepassing:

1. Open de [Azure Portal](https://portal.azure.com/) en meld u aan als **globale beheerder**.

2. Open de **uitbrei ding Azure Active Directory** door **alle services** bovenaan het menu aan de linkerkant te selecteren.

3. Voer **Azure Active Directory** in het zoekvak in en selecteer vervolgens het **Azure Active Directory** item.

4. Selecteer **bedrijfs toepassingen** in het menu van Azure AD aan de linkerkant.

5. Selecteer **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   Als u de gewenste toepassing niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen**in.

6. Selecteer de toepassing waaraan u een gebruiker wilt toewijzen.

7. Nadat de toepassing is geladen, selecteert u **gebruikers en groepen** in het menu van de toepassing aan de linkerkant.

8. Selecteer de knop **toevoegen** boven aan de lijst **gebruikers en groepen** om het deel venster **toewijzing toevoegen** te openen.

9. Selecteer **gebruikers en groepen** in het deel venster **toewijzing toevoegen** .

10. Voer de volledige groeps naam in van de groep die u wilt toewijzen in het zoekvak **zoeken op naam of e-mail adres** .

11. Beweeg de muis aanwijzer over **groep** in de lijst en schakel vervolgens het selectie vakje naast het profiel foto of logo van de groep in om de groep toe te voegen aan de **geselecteerde** lijst.

12. Optioneel: Als u meer dan één groep wilt toevoegen, voert u een andere volledige groeps naam in het zoekvak **zoeken op naam of e-mail adres** in en schakelt u het bijbehorende selectie vakje in om deze groep aan de **geselecteerde** lijst toe te voegen.

13. Wanneer u klaar bent met het selecteren van groepen, gebruikt u de knop **selecteren** om ze toe te voegen aan de lijst met gebruikers en groepen die aan de toepassing moeten worden toegewezen.

14. Optioneel: Gebruik de opdracht **rol selecteren** in het deel venster **toewijzing toevoegen** om een rol te selecteren die u wilt toewijzen aan de groepen die u hebt geselecteerd.

15. Selecteer **toewijzen** om de toepassing toe te wijzen aan de geselecteerde groepen.

Na een korte periode moeten de gebruikers die u hebt geselecteerd deze toepassingen vanuit het toegangs venster kunnen starten.

## <a name="next-steps"></a>Volgende stappen
[Bied eenmalige aanmelding voor uw apps via toepassings proxy](application-proxy-configure-single-sign-on-with-kcd.md).
