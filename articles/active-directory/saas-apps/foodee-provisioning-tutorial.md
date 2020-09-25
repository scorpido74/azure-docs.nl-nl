---
title: 'Zelf studie: de levens gebruiker configureren voor het automatisch inrichten van gebruikers met behulp van Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts voor de levens gebruiker.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 1d3ea609cda37c3bb0de28d8eb1a19944809980d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91331155"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>Zelf studie: de levens gebruiker configureren voor het automatisch inrichten van gebruikers

In dit artikel wordt beschreven hoe u Azure Active Directory (Azure AD) configureert in de levens groep en Azure AD, zodat gebruikers of groepen automatisch worden ingericht of ongedaan worden gemaakt voor de levens groep.

> [!NOTE]
> In dit artikel wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md)voor meer informatie over de werking van deze service en hoe deze werkt, en om antwoorden te krijgen op veelgestelde vragen.
>
> Deze connector is momenteel beschikbaar als preview-versie. Voor meer informatie over de functie Azure-gebruiks voorwaarden voor preview-functies gaat u naar [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In deze zelf studie wordt ervan uitgegaan dat u aan de volgende vereisten voldoet:

* Een Azure AD-Tenant
* [Een Tenant van een Food](https://www.food.ee/about/)
* Een gebruikers account in de levens groep met beheerders machtigingen

## <a name="assign-users-to-foodee"></a>Gebruikers toewijzen aan de levens gebruiker 

Azure AD gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers of groepen in azure AD toegang nodig hebben tot de levens groep. Nadat u deze bepaling hebt uitgevoerd, kunt u deze gebruikers of groepen toewijzen aan de levens groep door de instructies in [een gebruiker of groep toewijzen aan een bedrijfs-app te](../manage-apps/assign-user-or-group-access-portal.md)volgen.

## <a name="important-tips-for-assigning-users-to-foodee"></a>Belang rijke tips voor het toewijzen van gebruikers aan de levens gebruiker 

Houd rekening met de volgende tips wanneer u gebruikers toewijst:

* We raden u aan één Azure AD-gebruiker toe te wijzen aan de levens eenheid om de configuratie van automatische gebruikers inrichting te testen. U kunt later aanvullende gebruikers of groepen toewijzen.

* Wanneer u een gebruiker toewijst aan de groep, selecteert u een geldige toepassingsspecifieke rol, als deze beschikbaar is, in het deel venster **toewijzing** . Gebruikers met de *standaard toegangs* functie worden uitgesloten van het inrichten.

## <a name="set-up-foodee-for-provisioning"></a>De levens groep instellen voor inrichting

Voordat u een levens gebruiker configureert voor het automatisch inrichten van gebruikers met behulp van Azure AD, moet u het systeem inschakelen voor SCIM-inrichting (Cross-Domain Identity Management) in de levens werk.

1. Meld u aan bij de [levens](https://www.food.ee/login/)bekiezen en selecteer vervolgens uw Tenant-id.

    ![Foodee](media/Foodee-provisioning-tutorial/tenant.png)

1. Selecteer in **Enter prise Portal**de optie **eenmalige aanmelding**.

    ![Het menu Enterprise Portal van de levensmiddelen rechter](media/Foodee-provisioning-tutorial/scim.png)

1. Kopieer de waarde in het vak **API-token** voor later gebruik. U voert deze in het vak **geheim** op het tabblad **inrichten** van uw toepassing in de levens middelen in de Azure Portal.

    ![Foodee](media/Foodee-provisioning-tutorial/token.png)

## <a name="add-foodee-from-the-gallery"></a>Levens middelen toevoegen vanuit de galerie

Als u een levens gebruiker wilt configureren voor het automatisch inrichten van gebruikers met behulp van Azure AD, moet u de levens middelen van de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

Ga als volgt te werk om een levens middel toe te voegen vanuit de Azure AD-toepassings galerie:

1. In de [Azure-portal](https://portal.azure.com), selecteert u in het linkerdeelvenster **Azure Active Directory**.

    ![De Azure Active Directory opdracht](common/select-azuread.png)

1. Selecteer **Bedrijfstoepassingen** > **Alle toepassingen**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

1. Voer in het zoekvak een **voeding**in, selecteer **levens middelen** in het resultaten venster en selecteer **toevoegen** om de toepassing toe te voegen.

    ![De levens middelen in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>Automatische gebruikers inrichting voor de levens gebruiker configureren 

In deze sectie configureert u de Azure AD-inrichtings service om gebruikers of groepen in de levens groep te maken, bij te werken en uit te scha kelen op basis van de toewijzingen van gebruikers of groepen in azure AD.

> [!TIP]
> U kunt ook eenmalige aanmelding op basis van SAML inschakelen voor de levens eenheid door de instructies in de [zelf studie voor eenmalige aanmelding](Foodee-tutorial.md)in de levens werk te volgen. U kunt eenmalige aanmelding configureren, onafhankelijk van het automatisch inrichten van gebruikers, hoewel deze twee functies elkaar aanvullen.

Configureer de automatische gebruikers inrichting voor de levensmiddelen gebruiker in azure AD door de volgende handelingen uit te voeren:

1. Selecteer in het [Azure Portal](https://portal.azure.com)alle toepassingen in **bedrijfs toepassingen**  >  **All applications**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Selecteer in de lijst **toepassingen** de optie **eten**.

    ![De koppeling van de levens middelen in de lijst met toepassingen](common/all-applications.png)

1. Selecteer het tabblad **Inrichten**.

    ![Scherm opname van de opties voor beheer met de inrichtings optie.](common/provisioning.png)

1. Selecteer in de vervolg keuzelijst **inrichtings modus** de optie **automatisch**.

    ![Scherm afbeelding van de vervolg keuzelijst voor de inrichtings modus met de automatische optie aangeroepen.](common/provisioning-automatic.png)

1. Ga als volgt te werk onder **beheerders referenties**:

   a. Voer in het vak **Tenant-URL** de **https: \/ /Concierge.Food.ee/scim/v2-** waarde in die u eerder hebt opgehaald.

   b. Voer in het vak **geheim token** de waarde van het **API-token** in die u eerder hebt opgehaald.
   
   c. Selecteer **verbinding testen**om ervoor te zorgen dat Azure AD verbinding kan maken met de bemiddelende. Als de verbinding mislukt, zorg er dan voor dat uw levensmiddelen account beheerders machtigingen heeft en probeer het opnieuw.

    ![De koppeling test verbinding](common/provisioning-testconnection-tenanturltoken.png)

1. Voer in het vak **e-mail bericht** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel vervolgens het selectie vakje **e-mail melding verzenden wanneer een fout optreedt** in.

    ![Het tekstvak voor de meldings-e-mail](common/provisioning-notification-email.png)

1. Selecteer **Opslaan**.

1. Selecteer onder **toewijzingen**de optie **Azure Active Directory gebruikers synchroniseren met de levens gebruiker**.

    ![Gebruikers toewijzingen van een gebruiker](media/Foodee-provisioning-tutorial/usermapping.png)

1. Controleer onder **kenmerk toewijzingen**de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar de levens gebruiker. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt voor de *gebruikers accounts* in de levens werk voor bijwerk bewerkingen. 

    ![Gebruikers toewijzingen van een gebruiker](media/Foodee-provisioning-tutorial/userattribute.png)

1. Selecteer **Opslaan**om uw wijzigingen door te voeren.
1. Selecteer onder **toewijzingen**de optie **Azure Active Directory groepen synchroniseren met de levens groep**.

    ![Gebruikers toewijzingen van een gebruiker](media/Foodee-provisioning-tutorial/groupmapping.png)

1. Controleer onder **kenmerk toewijzingen**de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar de levens gebruiker. De kenmerken die als **overeenkomende** eigenschappen zijn geselecteerd, worden gebruikt om te voldoen aan de *groeps accounts* in de levens werk voor bijwerk bewerkingen.

    ![Gebruikers toewijzingen van een gebruiker](media/Foodee-provisioning-tutorial/groupattribute.png)

1. Selecteer **Opslaan**om uw wijzigingen door te voeren.
1. De bereik filters configureren. Raadpleeg de instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor meer informatie.

1. Als u de Azure AD-inrichtings service voor de levens middelen wilt inschakelen, wijzigt u de **inrichtings status** in het gedeelte **instellingen** in **op aan**.

    ![De inrichtings status schakelaar](common/provisioning-toggle-on.png)

1. Definieer onder **instellingen**in de vervolg keuzelijst **bereik** de gebruikers of groepen die u wilt inrichten voor de levens groep.

    ![De vervolg keuzelijst voor het inrichtings bereik](common/provisioning-scope.png)

1. Wanneer u klaar bent om in te richten, selecteert u **Opslaan**.

    ![De knop voor het inrichten van de configuratie](common/provisioning-configuration-save.png)

Met de voor gaande bewerking wordt de eerste synchronisatie gestart van de gebruikers of groepen die u hebt gedefinieerd in de vervolg keuzelijst **bereik** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan bij de volgende synchronisaties. Zie [hoe lang duurt het voor het inrichten van gebruikers?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)voor meer informatie.

U kunt de **huidige status** sectie gebruiken om de voortgang te bewaken en koppelingen naar uw inrichtings activiteiten rapport te volgen. In het rapport worden alle acties beschreven die worden uitgevoerd door de Azure AD-inrichtings service op de levens plaats. Zie [de status van gebruikers inrichten controleren](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)voor meer informatie. Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor informatie over het vastleggen van Azure AD-inrichtings Logboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Het inrichten van gebruikers accounts beheren voor zakelijke apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)
