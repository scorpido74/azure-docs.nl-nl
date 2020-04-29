---
title: 'Zelf studie: BlueJeans configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op BlueJeans.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fcb3fe009a6482c8e512899a952694beaed361a7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77058991"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Zelf studie: BlueJeans configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in BlueJeans en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen in BlueJeans.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u het volgende al hebt:

* Een Azure AD-Tenant
* Een BlueJeans-Tenant met het abonnement van [mijn bedrijf](https://www.BlueJeans.com/pricing) of beter ingeschakeld
* Een gebruikers account in BlueJeans met beheerders machtigingen

> [!NOTE]
> De integratie van Azure AD-inrichting is afhankelijk van de [BlueJeans-API](https://BlueJeans.github.io/developer), die beschikbaar is voor BlueJeans teams in het Standard-abonnement of beter.

## <a name="adding-bluejeans-from-the-gallery"></a>BlueJeans toevoegen vanuit de galerie

Voordat u BlueJeans configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u BlueJeans van de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om BlueJeans toe te voegen vanuit de Azure AD-toepassings galerie:**

1. Selecteer in de **[Azure Portal](https://portal.azure.com)** in het navigatie venster links **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **BlueJeans**in het zoekvak, selecteer **BlueJeans** in het deel venster resultaten en selecteer vervolgens de knop **toevoegen** om de toepassing toe te voegen.

    ![BlueJeans in de lijst met resultaten](common/search-new-app.png)

## <a name="assigning-users-to-bluejeans"></a>Gebruikers toewijzen aan BlueJeans

Azure Active Directory gebruikt een concept met de naam ' toewijzingen ' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers, worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot BlueJeans. Eenmaal besloten, kunt u deze gebruikers en/of groepen toewijzen aan BlueJeans door de volgende instructies te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>Belang rijke tips voor het toewijzen van gebruikers aan BlueJeans

* U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan BlueJeans om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan BlueJeans, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>Automatische gebruikers inrichting configureren voor BlueJeans

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in BlueJeans te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te scha kelen voor BlueJeans, gevolgd door de instructies in de [BlueJeans-zelf studie voor eenmalige aanmelding](bluejeans-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar behoeven.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor BlueJeans in azure AD:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en selecteer **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **BlueJeans**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **BlueJeans** in de lijst met toepassingen.

    ![De koppeling BlueJeans in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![BlueJeans-inrichting](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningTab.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![BlueJeans-inrichting](./media/bluejeans-provisioning-tutorial/Bluejeans1.png)

5. Voer in het gedeelte **beheerders referenties** de **gebruikers naam**van de beheerder en het **beheerders wachtwoord** van uw BlueJeans-account in. Voor beelden van deze waarden zijn:

   * Vul in het veld **Administrator-gebruikers** naam de gebruikers naam van het beheerders account in op uw BlueJeans-Tenant. Bijvoorbeeld: admin@contoso.com.

   * In het veld **beheerders wachtwoord** vult u het wacht woord in dat overeenkomt met de gebruikers naam van de beheerder.

6. Klik bij het invullen van de velden die worden weer gegeven in stap 5 op **verbinding testen** om te controleren of Azure AD verbinding kan maken met BlueJeans. Als de verbinding mislukt, zorg er dan voor dat uw BlueJeans-account beheerders machtigingen heeft en probeer het opnieuw.

    ![BlueJeans-inrichting](./media/bluejeans-provisioning-tutorial/BluejeansTestConnection.png)

7. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in om **een e-mail bericht te verzenden wanneer er een fout optreedt**.

    ![BlueJeans-inrichting](./media/bluejeans-provisioning-tutorial/BluejeansNotificationEmail.png)

8. Klik op **Opslaan**.

9. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met BlueJeans**.

    ![BlueJeans-inrichting](./media/bluejeans-provisioning-tutorial/BluejeansMapping.png)

10. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar BlueJeans in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in BlueJeans voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![BlueJeans-inrichting](./media/bluejeans-provisioning-tutorial/BluejeansUserMappingAtrributes.png)

11. Raadpleeg de volgende instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

12. Als u de Azure AD-inrichtings service voor **BlueJeans wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![BlueJeans-inrichting](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningStatus.png)

13. Definieer de gebruikers en/of groepen die u wilt inrichten voor BlueJeans door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![BlueJeans-inrichting](./media/bluejeans-provisioning-tutorial/UserGroupSelection.png)

14. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![BlueJeans-inrichting](./media/bluejeans-provisioning-tutorial/SaveProvisioning.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op BlueJeans.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="connector-limitations"></a>Connector beperkingen

* Met Bluejeans zijn geen gebruikers namen toegestaan die meer dan 30 tekens bevatten.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->

[1]: ./media/bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
