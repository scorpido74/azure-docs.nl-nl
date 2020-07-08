---
title: 'Zelf studie: koppel configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts naar koppel.
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
ms.openlocfilehash: cd7aae05b064657c7b9072402f4bc4d4d7fef7a6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77057855"
---
# <a name="tutorial-configure-flock-for-automatic-user-provisioning"></a>Zelf studie: koppel configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is om te demonstreren welke stappen moeten worden uitgevoerd in koppel-en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen in koppel.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.
>
> Deze connector bevindt zich momenteel in de open bare preview. Zie [aanvullende gebruiksrecht overeenkomst voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de algemene Microsoft Azure gebruiksrecht overeenkomst voor preview-functies.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant.
* [Een koppel Tenant](https://flock.com/pricing/)
* Een gebruikers account in koppel met beheerders machtigingen.

## <a name="assigning-users-to-flock"></a>Gebruikers toewijzen aan koppel 

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot het koppel. Nadat u hebt besloten, kunt u deze gebruikers en/of groepen toewijzen aan het koppel door de volgende instructies te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-flock"></a>Belang rijke tips voor het toewijzen van gebruikers aan koppel 

* U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan een koppel om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan koppel, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

## <a name="setup-flock--for-provisioning"></a>Koppel instelling voor inrichting

Voordat u een koppel configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SCIM inrichten inschakelen op het koppel.

1. Meld u aan bij [koppel](https://web.flock.com/?). Klik op het **pictogram instellingen**  >  **Beheer uw team**.

    ![Flock](media/flock-provisioning-tutorial/icon.png)

2. Selecteer **verificatie en inrichting**.

    ![Flock](media/Flock-provisioning-tutorial/auth.png)

3. Kopieer het **API-token**. Deze waarden worden ingevoerd in het veld **geheime token** op het tabblad inrichten van uw koppel toepassing in de Azure Portal.

    ![Flock](media/Flock-provisioning-tutorial/provisioning.png)


## <a name="add-flock--from-the-gallery"></a>Koppel toevoegen vanuit de galerie

Als u een koppel wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u het koppel vanuit de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Als u een koppel wilt toevoegen uit de Azure AD-toepassings galerie, voert u de volgende stappen uit:**

1. Selecteer in de **[Azure Portal](https://portal.azure.com)** in het navigatie venster links **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak het veld **koppel**in, selecteer **koppel** in het paneel resultaten en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![Koppel in de lijst met resultaten](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-flock"></a>Automatische gebruikers inrichting configureren voor koppel  

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in koppel te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML voor koppel in te scha kelen, gevolgd door de instructies in de [zelf studie eenmalige aanmelding bij het koppel teken](Flock-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar in de compliment

### <a name="to-configure-automatic-user-provisioning-for-flock--in-azure-ad"></a>Automatische gebruikers inrichting configureren voor een koppel in azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen **Flock**.

    ![De koppeling koppelen in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. In het gedeelte beheerders referenties voert u de `https://api.flock-staging.com/v2/scim` token waarden voor en **API** in die respectievelijk eerder zijn opgehaald in de **Tenant-URL** en het **geheime token** . Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met het koppel. Als de verbinding mislukt, zorg er dan voor dat het koppel account beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in om **een e-mail bericht te verzenden wanneer er een fout optreedt**.

    ![E-mail melding](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met koppel**.

    ![Koppel gebruikers toewijzingen](media/flock-provisioning-tutorial/usermapping.png)

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar koppel in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt voor de gebruikers accounts in koppel voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Kenmerken van Koppel gebruikers](media/flock-provisioning-tutorial/userattribute.png)

11. Raadpleeg de volgende instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

12. Als u de Azure AD-inrichtings service voor koppel wilt inschakelen, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

13. Definieer de gebruikers en/of groepen die u wilt inrichten voor het koppel door de gewenste waarden in het **bereik** in het gedeelte **instellingen** te kiezen.

    ![Inrichtings bereik](common/provisioning-scope.png)

14. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan bij de volgende synchronisaties. Voor meer informatie over hoe lang het duurt voor het inrichten van gebruikers en/of groepen, raadpleegt u [hoe lang het duurt om gebruikers](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)in te richten.

U kunt de **huidige status** sectie gebruiken om de voortgang te controleren en koppelingen naar uw inrichtings activiteiten rapport te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op het koppel. Zie [de status van gebruikers inrichten controleren](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)voor meer informatie. Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor informatie over het vastleggen van Azure AD-inrichtings Logboeken.



## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)