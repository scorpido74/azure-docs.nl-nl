---
title: 'Zelfstudie: Elium configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Elium.
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
ms.date: 08/19/2019
ms.author: Zhchia
ms.openlocfilehash: a3e1c936d46e0d9b0ad3b98dafeda7df0b4a23db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058460"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>Zelfstudie: Elium configureren voor automatische gebruikersinrichting

In deze zelfstudie ziet u hoe u Elium en Azure Active Directory (Azure AD) configureert om gebruikers of groepen automatisch in te richten en te de-provisionen voor Elium.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD-service voor het inrichten van gebruikers. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke informatie over wat deze service doet en hoe deze werkt, en voor veelgestelde vragen, [voor het automatiseren van gebruikersinrichting en deprovisioning voor SaaS-toepassingen met Azure Active Directory.](../app-provisioning/user-provisioning.md)
>
> Deze connector is momenteel in preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)de algemene gebruiksvoorwaarden voor Azure-functies in preview.

## <a name="prerequisites"></a>Vereisten

In deze zelfstudie wordt ervan uitgegaan dat u al de volgende vereisten hebt:

* Een Azure AD-tenant
* [Een Elium huurder](https://www.elium.com/pricing/)
* Een gebruikersaccount in Elium, met beheerdersmachtigingen

## <a name="assigning-users-to-elium"></a>Gebruikers toewijzen aan Elium

Azure AD gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang krijgen tot geselecteerde apps. In de context van automatische gebruikersinrichting worden alleen de gebruikers en groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, bepaalt u welke gebruikers en groepen in Azure AD toegang tot Elium nodig hebben. Wijs deze gebruikers en groepen vervolgens toe aan Elium door de stappen te volgen in [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-elium"></a>Belangrijke tips voor het toewijzen van gebruikers aan Elium 

We raden u aan één Azure AD-gebruiker aan Elium toe te wijzen om de automatische configuratie voor het inrichten van gebruikers te testen. Meer gebruikers en groepen kunnen later worden toegewezen.

Wanneer u een gebruiker aan Elium toewijst, moet u een geldige, toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoogvenster Toewijzing. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="set-up-elium-for-provisioning"></a>Elium instellen voor de inrichting

Voordat u Elium configureert voor automatische gebruikersvoorziening met Azure AD, moet u System for Cross-domain Identity Management (SCIM) inschakelen op Elium. Volg deze stappen:

1. Meld u aan bij Elium en ga naar **Mijn profielinstellingen** > **Settings**.

    ![Menu-item Instellingen in Elium](media/Elium-provisioning-tutorial/setting.png)

1. Selecteer **Beveiliging**in de linkerbenedenhoek onder **ADVANCED**.

    ![Beveiligingsverbinding in Elium](media/Elium-provisioning-tutorial/security.png)

1. Kopieer de **URL van tenant** en geheime **tokenwaarden.** U gebruikt deze waarden later in overeenkomstige velden op het tabblad **Inrichten** van uw Elium-toepassing in de Azure-portal.

    ![URL van tenant en geheime tokenvelden in Elium](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>Elium toevoegen vanuit de galerie

Als u Elium wilt configureren voor automatische gebruikersvoorziening met Azure AD, moet u ook Elium uit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen (Managed Software-as-a-Service). Volg deze stappen:

1. Selecteer **Azure Active Directory**in de [Azure-portal](https://portal.azure.com)in het linkernavigatiedeelvenster .

    ![Menu-item azure Active Directory](common/select-azuread.png)

1. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

     ![Het blade van Azure AD Enterprise-toepassingen](common/enterprise-applications.png)

1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** boven aan het deelvenster.

    ![Nieuwe toepassingskoppeling](common/add-new-app.png)

1. Typ **Elium**in het zoekvak en selecteer **Elium** in de lijst met resultaten en selecteer **Toevoegen** om de toepassing toe te voegen.

    ![Zoekvak galerie](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>Automatische gebruikersvoorziening configureren voor Elium

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en groepen in Elium te maken, bij te werken en uit te schakelen op basis van gebruikers- en groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om eenmalige aanmelding voor Elium in te schakelen op basis van Security Assertion Markup Language (SAML) door de instructies in de [elium-zelfsigneerzelfstudie te volgen.](Elium-tutorial.md) U eenmalige aanmelding onafhankelijk van automatische gebruikersinrichting configureren, hoewel de twee functies elkaar aanvullen.

Voer de volgende stappen uit om automatische gebruikersinrichting voor Elium in Azure AD te configureren:

1. Meld u aan bij de [Azure-portal,](https://portal.azure.com)selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![Het blade van Azure AD Enterprise-toepassingen](common/enterprise-applications.png)

1. Selecteer **Elium** in de lijst met toepassingen.

    ![Lijst met toepassingen in het bedrijfsmes](common/all-applications.png)

1. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichting in het blade van Enterprise-toepassingen](common/provisioning.png)

1. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Automatische instelling voor inrichtingsmodus](common/provisioning-automatic.png)

1. **Typ \<tenantURL\>/scim/v2** in de sectie **Beheerdersreferenties** in het veld **Tenant-URL.** (De **tenantURL** is de waarde die eerder is opgehaald uit de Elium-beheerconsole.) Typ ook de **eliumgeheime tokenwaarde** in het veld **Secret Token.** Selecteer ten slotte **Testverbinding** om te controleren of Azure AD verbinding kan maken met Elium. Als de verbinding mislukt, moet u ervoor zorgen dat uw Elium-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![URL-tenant en geheime tokenvelden in beheerdersreferenties](common/provisioning-testconnection-tenanturltoken.png)

1. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten ontvangt. Schakel vervolgens het selectievakje **Een e-mailmelding verzenden wanneer er een fout optreedt** in.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

1. Klik op **Opslaan**.

1. Selecteer Azure **Active Directory-gebruikers synchroniseren met Elium**in de sectie **Toewijzingen** .

    ![Koppeling synchroniseren voor het toewijzen van Azure AD-gebruikers aan Elium](media/Elium-provisioning-tutorial/usermapping.png)

1. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Elium in de sectie **Toewijzingen van kenmerken.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Elium te matchen voor updatebewerkingen. Selecteer **Opslaan** om wijzigingen door te voeren.

    ![Toewijzingen tussen Azure AD en Elium](media/Elium-provisioning-tutorial/userattribute.png)

1. Als u scopingfilters wilt configureren, volgt u de instructies in de zelfstudie van het [filter Scoping](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Als u de Azure AD-inrichtingsservice voor Elium wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen.**

    ![Inrichten status ingesteld op Aan](common/provisioning-toggle-on.png)

1. Definieer de gebruikers en groepen die u wilt inrichten in Elium door de gewenste waarden te selecteren in het vervolgkeuzelijst **Scope** in de sectie **Instellingen.**

    ![Keuzelijst voorziening](common/provisioning-scope.png)

1. Wanneer u klaar bent voor inlevering, selecteert u **Opslaan**.

    ![Knop Opslaan voor inrichtingsconfiguratie](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** Dit eerste synchronisatieproces duurt langer dan later wordt gesynchroniseerd. Zie Hoe lang duurt het voor [de voorziening smaken van gebruikers voor](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)meer informatie over de tijd die nodig is voor het inrichten?

Gebruik de sectie **Huidige status** om de voortgang te volgen en koppelingen naar uw rapportagerapport voor inrichtenactiviteiten te volgen. In het rapport voor het inrichten van activiteiten worden alle acties beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Elium. Zie [De status van gebruikersinrichting controleren](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)voor meer informatie . Zie [Rapportage over automatische gebruikersaccountinrichting](../app-provisioning/check-status-user-account-provisioning.md)voor het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)
