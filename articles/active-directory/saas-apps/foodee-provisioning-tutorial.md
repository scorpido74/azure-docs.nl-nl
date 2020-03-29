---
title: 'Zelfstudie: Foodee configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te deprovisioneneren aan Foodee.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 2195056ec66550063aba5ce5e2b977b51a6dc5e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057803"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>Zelfstudie: Foodee configureren voor automatische gebruikersinrichting

In dit artikel ziet u hoe u Azure Active Directory (Azure AD) configureert in Foodee en Azure AD om gebruikers of groepen automatisch in te richten of te deprovisioneren aan Foodee.

> [!NOTE]
> In het artikel wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning-service. Zie [Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor](../app-provisioning/user-provisioning.md)meer informatie over wat deze service doet en hoe deze werkt en om antwoorden te krijgen op veelgestelde vragen.
>
> Deze connector is momenteel in preview. Ga voor meer informatie over de azure-gebruiksvoorwaarden voor preview-functies naar [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Vereisten

In deze zelfstudie wordt ervan uitgegaan dat u aan de volgende voorwaarden hebt voldaan:

* Een Azure AD-tenant
* [Een Foodee huurder](https://www.food.ee/about/)
* Een gebruikersaccount in Foodee met beheerdersmachtigingen

## <a name="assign-users-to-foodee"></a>Gebruikers toewijzen aan Foodee 

Azure AD gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van automatische gebruikersinrichting worden alleen de gebruikers of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers of groepen in Azure AD toegang tot Foodee nodig hebben. Nadat u deze bepaling hebt gemaakt, u deze gebruikers of groepen aan Foodee toewijzen door de instructies te volgen in [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-foodee"></a>Belangrijke tips voor het toewijzen van gebruikers aan Foodee 

Houd bij het toewijzen van gebruikers rekening met de volgende tips:

* We raden u aan slechts één Azure AD-gebruiker aan Foodee toe te wijzen om de configuratie van automatische gebruikersinrichting te testen. U later extra gebruikers of groepen toewijzen.

* Wanneer u een gebruiker aan Foodee toeschrijft, selecteert u een geldige toepassingsspecifieke rol, als deze beschikbaar is, in het deelvenster **Toewijzing.** Gebruikers met de *functie Standaardtoegang* zijn uitgesloten van inrichten.

## <a name="set-up-foodee-for-provisioning"></a>Foodee instellen voor de inrichting

Voordat u Foodee configureert voor automatische gebruikersinrichting met Azure AD, moet u System for Cross-domain Identity Management (SCIM) inFoodee inschakelen.

1. Meld u aan bij [Foodee](https://www.food.ee/login/)en selecteer vervolgens uw tenant-id.

    ![Foodee](media/Foodee-provisioning-tutorial/tenant.png)

1. Selecteer onder **Enterprise-portal**de optie **Eén aan- en aan- en.(Under**Enterprise portal) selecteer Single Sign On .

    ![Het menu van de linkerdeelvenster van foodee Enterprise Portal](media/Foodee-provisioning-tutorial/scim.png)

1. Kopieer de waarde in het vak **API-token** voor later gebruik. U voert het in het vak **Secret Token** in het tabblad **Inrichten** van uw Foodee-toepassing in de Azure-portal.

    ![Foodee](media/Foodee-provisioning-tutorial/token.png)

## <a name="add-foodee-from-the-gallery"></a>Voeg Foodee toe vanuit de galerie

Als u Foodee wilt configureren voor automatische gebruikersinrichting met Azure AD, moet u Foodee vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

Ga als volgt te werk om Foodee toe te voegen vanuit de Azure AD-toepassingsgalerie:

1. In de [Azure-portal](https://portal.azure.com), selecteert u in het linkerdeelvenster **Azure Active Directory**.

    ![De opdracht Azure Active Directory](common/select-azuread.png)

1. Selecteer **Enterprise-toepassingen** > **Alle toepassingen**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

1. Voer in het zoekvak **Foodee**in, selecteer **Foodee** in het resultatenvenster en selecteer **Toevoegen** om de toepassing toe te voegen.

    ![Foodee in de resultatenlijst](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>Automatische gebruikersvoorziening configureren voor Foodee 

In deze sectie configureert u de Azure AD-inrichtingsservice om gebruikers of groepen in Foodee te maken, bij te werken en uit te schakelen op basis van gebruikers- of groepstoewijzingen in Azure AD.

> [!TIP]
> U saml-gebaseerde aanmelding voor Foodee ook inschakelen door de instructies in de [foodee-zelfsigne-zelfstudie te volgen.](Foodee-tutorial.md) U eenmalige aanmelding configureren, onafhankelijk van automatische gebruikersinrichting, hoewel deze twee functies elkaar aanvullen.

Configureer de automatische gebruikersinrichting voor Foodee in Azure AD door het volgende te doen:

1. Selecteer Enterprise Applications **All-toepassingen** > **All applications**in de [Azure-portal.](https://portal.azure.com)

    ![Deelvenster Ondernemingstoepassingen](common/enterprise-applications.png)

1. Selecteer **Foodee**in de lijst **Toepassingen** .

    ![De Foodee-link in de lijst Toepassingen](common/all-applications.png)

1. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

1. Selecteer **Automatisch**in de vervolgkeuzelijst **Inprovisioning Mode** .

    ![Tabblad Inrichten](common/provisioning-automatic.png)

1. Ga als volgt te werk onder **Beheerdersreferenties:**

   a. Voer **in** het vak Url van tenant de **https in:\/concierge.food.ee/scim/v2** waarde die u eerder hebt opgehaald.

   b. Voer in het vak **Secret Token** de **API-tokenwaarde** in die u eerder hebt opgehaald.
   
   c. Als u wilt weten of Azure AD verbinding kan maken met Foodee, selecteert u **Testverbinding**. Als de verbinding mislukt, moet u ervoor zorgen dat uw Foodee-account beheerdersmachtigingen heeft en het vervolgens opnieuw proberen.

    ![De koppeling Testverbinding](common/provisioning-testconnection-tenanturltoken.png)

1. Voer in het vak **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fout moet ontvangen en schakel het selectievakje **Een e-mailmelding verzenden wanneer er een fout optreedt** in.

    ![Het tekstvak Berichte-mail](common/provisioning-notification-email.png)

1. Selecteer **Opslaan**.

1. Selecteer **onder Toewijzingen**de optie **Azure Active Directory Users synchroniseren met Foodee**.

    ![Foodee gebruikerskaarten](media/Foodee-provisioning-tutorial/usermapping.png)

1. Controleer **onder Kenmerktoewijzingen**de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Foodee. De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Foodee te *matchen* voor updatebewerkingen. 

    ![Foodee gebruikerskaarten](media/Foodee-provisioning-tutorial/userattribute.png)

1. Als u uw wijzigingen wilt vastleggen, selecteert u **Opslaan**.
1. Selecteer **onder Toewijzingen**de optie **Azure Active Directory Groups synchroniseren met Foodee**.

    ![Foodee gebruikerskaarten](media/Foodee-provisioning-tutorial/groupmapping.png)

1. Controleer **onder Kenmerktoewijzingen**de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Foodee. De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de groepsaccounts in Foodee te *matchen* voor updatebewerkingen.

    ![Foodee gebruikerskaarten](media/Foodee-provisioning-tutorial/groupattribute.png)

1. Als u uw wijzigingen wilt vastleggen, selecteert u **Opslaan**.
1. Configureer de scopingfilters. Raadpleeg de instructies in de [zelfstudie voor het filter Scoping](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor meer informatie.

1. Als u de Azure AD-inrichtingsservice voor Foodee wilt inschakelen, wijzigt u in de sectie **Instellingen** de **instelstatus** in **Aan**.

    ![De inrichtenstatusschakelaar](common/provisioning-toggle-on.png)

1. Definieer **onder Instellingen**in de vervolgkeuzelijst **Scope** de gebruikers of groepen die u aan Foodee wilt inrichten.

    ![De vervolgkeuzelijst Scope provisioning scope](common/provisioning-scope.png)

1. Wanneer u klaar bent voor inlevering, selecteert u **Opslaan**.

    ![De knop Configuratie opslaan inrichten](common/provisioning-configuration-save.png)

Met de vorige bewerking wordt de eerste synchronisatie gestart van de gebruikers of groepen die u hebt gedefinieerd in de vervolgkeuzelijst **Bereik.** De eerste synchronisatie duurt langer om uit te voeren dan latere synchronisaties. Zie [Hoe lang duurt het voor gebruikers?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

U de sectie **Huidige status** gebruiken om de voortgang te volgen en koppelingen naar uw rapportagerapport voor inrichtenactiviteiten te volgen. In het rapport worden alle acties beschreven die worden uitgevoerd door de Azure AD-inrichtingsservice op Foodee. Zie [De status van gebruikersinrichting controleren](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)voor meer informatie . Zie [Rapportage over automatische gebruikersaccountinrichting](../app-provisioning/check-status-user-account-provisioning.md)voor het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountinrichting voor bedrijfsapps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)
