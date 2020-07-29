---
title: 'Zelf studie: Elium configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op Elium.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77058460"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>Zelf studie: Elium configureren voor automatische gebruikers inrichting

In deze zelf studie leert u hoe u Elium en Azure Active Directory (Azure AD) configureert om automatisch gebruikers of groepen in te richten en te deactiveren naar Elium.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie Gebruikers inrichten en de inrichting ongedaan maken voor [SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md)voor belang rijke informatie over de werking van deze service en hoe deze werkt, en voor veelgestelde vragen.
>
> Deze connector is momenteel beschikbaar als preview-versie. Zie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor de algemene gebruiks voorwaarden voor Azure-functies in preview.

## <a name="prerequisites"></a>Vereisten

In deze zelf studie wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-Tenant
* [Een Elium-Tenant](https://www.elium.com/pricing/)
* Een gebruikers account in Elium met beheerders machtigingen

## <a name="assigning-users-to-elium"></a>Gebruikers toewijzen aan Elium

Azure AD gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u bepalen welke gebruikers en groepen in azure AD toegang nodig hebben tot Elium. Wijs deze gebruikers en groepen vervolgens toe aan Elium door de stappen in [een gebruiker of groep toewijzen aan een bedrijfs-app te](../manage-apps/assign-user-or-group-access-portal.md)volgen.

## <a name="important-tips-for-assigning-users-to-elium"></a>Belang rijke tips voor het toewijzen van gebruikers aan Elium 

We raden u aan één Azure AD-gebruiker toe te wijzen aan Elium om de automatische inrichtings configuratie van de gebruiker te testen. U kunt later meer gebruikers en groepen toewijzen.

Wanneer u een gebruiker toewijst aan Elium, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de **standaard toegangs** functie worden uitgesloten van het inrichten.

## <a name="set-up-elium-for-provisioning"></a>Elium instellen voor inrichting

Voordat u Elium configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u systeem inschakelen voor SCIM-inrichting (Cross-Domain Identity Management) op Elium. Volg deze stappen:

1. Meld u aan bij Elium en ga naar **Mijn profiel**  >  **instellingen**.

    ![Menu-item instellingen in Elium](media/Elium-provisioning-tutorial/setting.png)

1. Klik in de linkerbenedenhoek onder **Geavanceerd**op **beveiliging**.

    ![Beveiligings koppeling in Elium](media/Elium-provisioning-tutorial/security.png)

1. Kopieer de **Tenant-URL** en **geheime token** waarden. U gebruikt deze waarden later in de bijbehorende velden op het tabblad **inrichten** van uw Elium-toepassing in de Azure Portal.

    ![De velden Tenant-URL en geheim token in Elium](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>Elium toevoegen vanuit de galerie

Als u Elium wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u Elium ook toevoegen vanuit de Azure AD-toepassings galerie aan uw lijst met beheerde SaaS-toepassingen (Software-as-a-Service). Volg deze stappen:

1. Selecteer in de [Azure Portal](https://portal.azure.com)in het navigatie venster links **Azure Active Directory**.

    ![Menu-item Azure Active Directory](common/select-azuread.png)

1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

     ![Blade Azure AD-zakelijke toepassingen](common/enterprise-applications.png)

1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **nieuwe toepassing** boven aan het deel venster.

    ![Koppeling naar nieuwe toepassing](common/add-new-app.png)

1. Typ **Elium**in het zoekvak, selecteer **Elium** in de lijst met resultaten en selecteer **toevoegen** om de toepassing toe te voegen.

    ![Zoekvak voor galerie](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>Automatische gebruikers inrichting configureren voor Elium

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en groepen in Elium te maken, bij te werken en uit te scha kelen, op basis van gebruikers-en groeps toewijzingen in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding in te scha kelen voor Elium op basis van Security Assertion Markup Language (SAML) door de instructies in de [Elium-zelf studie voor eenmalige aanmelding](Elium-tutorial.md)te volgen. U kunt eenmalige aanmelding onafhankelijk van automatische gebruikers inrichting configureren, hoewel de twee functies elkaar aanvullen.

Voer de volgende stappen uit om de automatische gebruikers inrichting voor Elium in azure AD te configureren:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com), selecteer **zakelijke toepassingen**en selecteer vervolgens **alle toepassingen**.

    ![Blade Azure AD-zakelijke toepassingen](common/enterprise-applications.png)

1. Selecteer **Elium** in de lijst met toepassingen.

    ![Lijst met toepassingen op de Blade bedrijfs toepassingen](common/all-applications.png)

1. Selecteer het tabblad **inrichten** .

    ![Het tabblad inrichten op de Blade bedrijfs toepassingen](common/provisioning.png)

1. Stel de **inrichtings modus** in op **automatisch**.

    ![Automatische instelling voor inrichtings modus](common/provisioning-automatic.png)

1. Typ in de sectie **beheerders referenties** ** \<tenantURL\> /scim/v2** in het veld **Tenant-URL** . (De **tenantURL** is de waarde die eerder is opgehaald uit de Elium-beheer console.) Typ ook de waarde van het Elium **Secret-token** in het veld **geheime token** . Selecteer tot slot **verbinding testen** om te controleren of Azure AD verbinding kan maken met Elium. Als de verbinding mislukt, zorgt u ervoor dat uw Elium-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL en geheime token velden in beheerders referenties](common/provisioning-testconnection-tenanturltoken.png)

1. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen ontvangt. Schakel vervolgens het selectie vakje **e-mail melding verzenden wanneer een fout optreedt** in.

    ![E-mail melding](common/provisioning-notification-email.png)

1. Klik op **Opslaan**.

1. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Elium**.

    ![Koppeling synchroniseren voor het toewijzen van Azure AD-gebruikers aan Elium](media/Elium-provisioning-tutorial/usermapping.png)

1. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Elium in de sectie **kenmerk toewijzingen** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Elium voor bijwerk bewerkingen. Selecteer **Opslaan** om eventuele wijzigingen door te voeren.

    ![Kenmerk toewijzingen tussen Azure AD en Elium](media/Elium-provisioning-tutorial/userattribute.png)

1. Als u bereik filters wilt configureren, volgt u de instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik.

1. Als u de Azure AD-inrichtings service voor **Elium wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![De inrichtings status is ingesteld op op](common/provisioning-toggle-on.png)

1. Definieer de gebruikers en groepen die u wilt inrichten voor Elium door de gewenste waarden te selecteren in de vervolg keuzelijst **bereik** van het gedeelte **instellingen** .

    ![Keuze lijst voor het inrichten van het bereik](common/provisioning-scope.png)

1. Wanneer u klaar bent om in te richten, selecteert u **Opslaan**.

    ![De knop Opslaan voor het inrichten van de configuratie](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Dit initiële synchronisatie proces duurt langer dan latere synchronisaties. Zie [hoe lang het duurt om gebruikers in te richten](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)voor meer informatie over de tijd die nodig is voor het inrichten van de inrichting?.

Gebruik de sectie **huidige status** om de voortgang te bewaken en koppelingen naar uw inrichtings activiteiten rapport te volgen. In het rapport inrichtings activiteit worden alle acties beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op Elium. Zie [de status van gebruikers inrichten controleren](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)voor meer informatie. Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor informatie over het vastleggen van Azure AD-inrichtings Logboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* Het [inrichten van een gebruikers account voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)
