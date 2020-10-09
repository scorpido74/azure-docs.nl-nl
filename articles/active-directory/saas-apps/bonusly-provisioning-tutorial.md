---
title: 'Zelf studie: een bonus configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts tot een bonus.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 1db1ef3a8fa7de557444c25650410b4e84422be4
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91849281"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>Zelf studie: een bonus configureren voor het automatisch inrichten van gebruikers

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in een bonus en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen tot een bonus.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u het volgende al hebt:

* Een Azure AD-Tenant
* Een [bonus Tenant](https://bonus.ly/pricing)
* Een gebruikers account in een bonus met beheerders machtigingen

> [!NOTE]
> De integratie van Azure AD-inrichting is afhankelijk van de [bonus rest API](https://konghq.com/solutions/gateway/), die beschikbaar is voor een bonus ontwikkelaar.

## <a name="adding-bonusly-from-the-gallery"></a>Bonusly toevoegen vanuit de galerie

Voordat u een bonus configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u een bonus van de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Als u een bonus wilt toevoegen vanuit de Azure AD-toepassings galerie, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Bonusly** in het zoekvak, selecteer **Bonusly** in het venster met resultaten en klik op **Toevoegen** om de toepassing toe te voegen.

    ![Bonusly in de lijst met resultaten](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>Gebruikers toewijzen aan een bonus

Azure Active Directory gebruikt een concept met de naam 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD. 

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD zich op een bonus hebben moeten aanmelden. Nadat u hebt besloten, kunt u deze gebruikers en/of groepen aan een bonus toewijzen door de volgende instructies te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>Belang rijke tips voor het toewijzen van gebruikers aan een bonus

* Het is raadzaam om een enkele Azure AD-gebruiker toe te wijzen om de configuratie van automatische gebruikers inrichting in een bonus te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan een bonus, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **Standaardtoegang** worden uitgesloten van het inrichten.

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>Automatische gebruikers inrichting configureren voor een bonus

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in een bonus te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

> [!TIP]
> U kunt ook op SAML gebaseerde eenmalige aanmelding inschakelen voor een bonus, volgens de instructies in de [zelf studie voor eenmalige aanmelding](bonus-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar behoeven.

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>Automatische gebruikers inrichting voor een bonus configureren in azure AD:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en selecteer **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **bonus**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Bonusly** in de lijst met toepassingen.

    ![De koppeling voor Bonusly in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningTab.png" alt-text="Scherm afbeelding van het tabblad voor het inrichten van een bonus. Onder beheren wordt inrichting gemarkeerd." border="false":::

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png" alt-text="Scherm afbeelding van het tabblad voor het inrichten van een bonus. Onder beheren wordt inrichting gemarkeerd." border="false":::

5. Voer in het gedeelte **beheerders referenties** het **geheime token** van uw bonus account in, zoals beschreven in stap 6.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/secrettoken.png" alt-text="Scherm afbeelding van het tabblad voor het inrichten van een bonus. Onder beheren wordt inrichting gemarkeerd." border="false":::

6. Het **geheime token** voor uw bonus account bevindt zich in de **beheer > bedrijfs > integraties**. In de sectie **Als u code wilt** gebruiken, klikt u op **API > nieuw token voor API-toegang maken** om een nieuw geheim token te maken.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png" alt-text="Scherm afbeelding van het tabblad voor het inrichten van een bonus. Onder beheren wordt inrichting gemarkeerd." border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/BonsulyRestApi.png" alt-text="Scherm afbeelding van het tabblad voor het inrichten van een bonus. Onder beheren wordt inrichting gemarkeerd." border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/CreateToken.png" alt-text="Scherm afbeelding van het tabblad voor het inrichten van een bonus. Onder beheren wordt inrichting gemarkeerd." border="false":::

7. Typ in het volgende scherm een naam voor het toegangs token in het tekstvak en druk vervolgens op API- **sleutel maken**. Het nieuwe toegangs token wordt een paar seconden weer gegeven in een pop-up.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/Token01.png" alt-text="Scherm afbeelding van het tabblad voor het inrichten van een bonus. Onder beheren wordt inrichting gemarkeerd." border="false":::

    :::image type="content" source="./media/bonusly-provisioning-tutorial/Token02.png" alt-text="Scherm afbeelding van het tabblad voor het inrichten van een bonus. Onder beheren wordt inrichting gemarkeerd." border="false":::

8. Klik bij het invullen van de velden die worden weer gegeven in stap 5 op **verbinding testen** om ervoor te zorgen dat Azure AD op een bonus verbinding kan maken. Als de verbinding mislukt, zorg er dan voor dat uw bonus account beheerders machtigingen heeft en probeer het opnieuw.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/TestConnection.png" alt-text="Scherm afbeelding van het tabblad voor het inrichten van een bonus. Onder beheren wordt inrichting gemarkeerd." border="false":::

9. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje **e-mail melding verzenden als er een fout optreedt**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/EmailNotification.png" alt-text="Scherm afbeelding van het tabblad voor het inrichten van een bonus. Onder beheren wordt inrichting gemarkeerd." border="false":::

10. Klik op **Opslaan**.

11. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers op een bonus te synchroniseren**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/UserMappings.png" alt-text="Scherm afbeelding van het tabblad voor het inrichten van een bonus. Onder beheren wordt inrichting gemarkeerd." border="false":::

12. Controleer de gebruikers kenmerken die vanuit Azure AD worden gesynchroniseerd naar een bonus in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om de gebruikers accounts in een bonus te vergelijken voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/UserAttributeMapping.png" alt-text="Scherm afbeelding van het tabblad voor het inrichten van een bonus. Onder beheren wordt inrichting gemarkeerd." border="false":::

13. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Als u de Azure AD-inrichtings service voor een **bonus wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ProvisioningStatus.png" alt-text="Scherm afbeelding van het tabblad voor het inrichten van een bonus. Onder beheren wordt inrichting gemarkeerd." border="false":::

15. Definieer de gebruikers en/of groepen die u wilt inrichten op een bonus door de gewenste waarden in het **bereik** in de sectie **instellingen** te kiezen.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/ScopeSync.png" alt-text="Scherm afbeelding van het tabblad voor het inrichten van een bonus. Onder beheren wordt inrichting gemarkeerd." border="false":::

16. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    :::image type="content" source="./media/bonusly-provisioning-tutorial/SaveProvisioning.png" alt-text="Scherm afbeelding van het tabblad voor het inrichten van een bonus. Onder beheren wordt inrichting gemarkeerd." border="false":::

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen. hierin worden alle acties beschreven die door de Azure AD Provisioning-Service op een bonus worden uitgevoerd.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png
