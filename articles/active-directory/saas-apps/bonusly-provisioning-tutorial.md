---
title: 'Zelf studie: een bonus configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts tot een bonus.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 879b0ee9-042a-441b-90a7-8c364d62426a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7694e441a59680a9b9544d3479100c1f779964ff
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77058872"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>Zelf studie: een bonus configureren voor het automatisch inrichten van gebruikers

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in een bonus en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen tot een bonus.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u het volgende al hebt:

* Een Azure AD-Tenant
* Een [bonus Tenant](https://bonus.ly/pricing)
* Een gebruikers account in een bonus met beheerders machtigingen

> [!NOTE]
> De integratie van Azure AD-inrichting is afhankelijk van de [bonus rest API](https://konghq.com/solutions/gateway/), die beschikbaar is voor een bonus ontwikkelaar.

## <a name="adding-bonusly-from-the-gallery"></a>Een bonus toevoegen vanuit de galerie

Voordat u een bonus configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u een bonus van de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Als u een bonus wilt toevoegen vanuit de Azure AD-toepassings galerie, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak een **bonus**, selecteer een **bonus** uit het paneel resultaten en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![Bonus in de lijst met resultaten](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>Gebruikers toewijzen aan een bonus

Azure Active Directory gebruikt een concept met de naam ' toewijzingen ' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers, worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd. 

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD zich op een bonus hebben moeten aanmelden. Nadat u hebt besloten, kunt u deze gebruikers en/of groepen aan een bonus toewijzen door de volgende instructies te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>Belang rijke tips voor het toewijzen van gebruikers aan een bonus

* Het is raadzaam om een enkele Azure AD-gebruiker toe te wijzen om de configuratie van automatische gebruikers inrichting in een bonus te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan een bonus, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>Automatische gebruikers inrichting configureren voor een bonus

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in een bonus te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

> [!TIP]
> U kunt ook op SAML gebaseerde eenmalige aanmelding inschakelen voor een bonus, volgens de instructies in de [zelf studie voor eenmalige aanmelding](bonus-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar behoeven.

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>Automatische gebruikers inrichting voor een bonus configureren in azure AD:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en selecteer **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **bonus**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **bonus**.

    ![De bonus koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Gratificatie inrichten](./media/bonusly-provisioning-tutorial/ProvisioningTab.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Gratificatie inrichten](./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png)

5. Voer in het gedeelte **beheerders referenties** het **geheime token** van uw bonus account in, zoals beschreven in stap 6.

    ![Gratificatie inrichten](./media/bonusly-provisioning-tutorial/secrettoken.png)

6. Het **geheime token** voor uw bonus account bevindt zich in de **beheer > bedrijfs > integraties**. In de sectie **Als u code wilt** gebruiken, klikt u op **API > nieuw token voor API-toegang maken** om een nieuw geheim token te maken.

    ![Gratificatie inrichten](./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png)

    ![Gratificatie inrichten](./media/bonusly-provisioning-tutorial/BonsulyRestApi.png)

    ![Gratificatie inrichten](./media/bonusly-provisioning-tutorial/CreateToken.png)

7. Typ in het volgende scherm een naam voor het toegangs token in het tekstvak en druk vervolgens op API- **sleutel maken**. Het nieuwe toegangs token wordt een paar seconden weer gegeven in een pop-up.

    ![Gratificatie inrichten](./media/bonusly-provisioning-tutorial/Token01.png)

    ![Gratificatie inrichten](./media/bonusly-provisioning-tutorial/Token02.png)

8. Klik bij het invullen van de velden die worden weer gegeven in stap 5 op **verbinding testen** om ervoor te zorgen dat Azure AD op een bonus verbinding kan maken. Als de verbinding mislukt, zorg er dan voor dat uw bonus account beheerders machtigingen heeft en probeer het opnieuw.

    ![Gratificatie inrichten](./media/bonusly-provisioning-tutorial/TestConnection.png)

9. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje **e-mail melding verzenden als er een fout optreedt**.

    ![Gratificatie inrichten](./media/bonusly-provisioning-tutorial/EmailNotification.png)

10. Klik op **Opslaan**.

11. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers op een bonus te synchroniseren**.

    ![Gratificatie inrichten](./media/bonusly-provisioning-tutorial/UserMappings.png)

12. Controleer de gebruikers kenmerken die vanuit Azure AD worden gesynchroniseerd naar een bonus in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om de gebruikers accounts in een bonus te vergelijken voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Gratificatie inrichten](./media/bonusly-provisioning-tutorial/UserAttributeMapping.png)

13. Raadpleeg de volgende instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

14. Als u de Azure AD-inrichtings service voor een **bonus wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Gratificatie inrichten](./media/bonusly-provisioning-tutorial/ProvisioningStatus.png)

15. Definieer de gebruikers en/of groepen die u wilt inrichten op een bonus door de gewenste waarden in het **bereik** in de sectie **instellingen** te kiezen.

    ![Gratificatie inrichten](./media/bonusly-provisioning-tutorial/ScopeSync.png)

16. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Gratificatie inrichten](./media/bonusly-provisioning-tutorial/SaveProvisioning.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen. hierin worden alle acties beschreven die door de Azure AD Provisioning-Service op een bonus worden uitgevoerd.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png
