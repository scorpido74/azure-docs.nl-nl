---
title: 'Zelf studie: Zscaler configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op Zscaler.
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
ms.openlocfilehash: 52c18f8d51f18b9bc167a99fbafda2365824dfc9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91312088"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>Zelf studie: Zscaler configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in Zscaler en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen in Zscaler.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../active-directory-saas-app-provisioning.md).
>

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u het volgende al hebt:

* Een Azure AD-tenant.
* Een Zscaler-Tenant.
* Een gebruikers account in Zscaler met beheerders machtigingen.

> [!NOTE]
> De integratie van Azure AD-inrichting is afhankelijk van de Zscaler SCIM-API, die beschikbaar is voor Zscaler-ontwikkel aars voor accounts met het ondernemings pakket.

## <a name="adding-zscaler-from-the-gallery"></a>Zscaler toevoegen vanuit de galerie

Voordat u Zscaler configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u Zscaler van de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Zscaler toe te voegen vanuit de Azure AD-toepassings galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Zscaler**in het zoekvak, selecteer **Zscaler** in het resultaten paneel en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![Zscaler in de lijst met resultaten](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>Gebruikers toewijzen aan Zscaler

Azure Active Directory gebruikt een concept met de naam 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen gesynchroniseerd die zijn toegewezen aan een toepassing in Azure AD.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot Zscaler. Eenmaal besloten, kunt u deze gebruikers en/of groepen toewijzen aan Zscaler door de volgende instructies te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>Belang rijke tips voor het toewijzen van gebruikers aan Zscaler

* U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan Zscaler om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan Zscaler, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **Standaard toegang** worden uitgesloten van het inrichten.

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Automatische gebruikers inrichting configureren voor Zscaler

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in Zscaler te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te scha kelen voor Zscaler, gevolgd door de instructies in de [Zscaler-zelf studie voor eenmalige aanmelding](zscaler-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar behoeven.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor Zscaler in azure AD:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en selecteer **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **Zscaler**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Zscaler** in de lijst met toepassingen:

    ![De koppeling naar Zscaler in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Scherm opname van de Zscaler van de Enter prise-toepassing met de inrichtings optie gemarkeerd.](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Scherm afbeelding van de inrichtings pagina waarvoor de inrichtings modus is ingesteld op automatisch.](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. Voer in het gedeelte **beheerders referenties** de **Tenant-URL** en het **geheime token** van uw Zscaler-account in, zoals beschreven in stap 6.

6. Als u de **Tenant-URL** en het **geheime token**wilt ophalen, gaat u naar **beheer > authenticatie-instellingen** in de gebruikers interface van de Zscaler-Portal en klikt u op **SAML** onder **verificatie type**.

    ![Scherm afbeelding van de pagina verificatie-instellingen.](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    Klik op **SAML configureren** om SAML-opties voor **configuratie** te openen.

    ![Scherm afbeelding van het dialoog venster S A M L configureren met de tekst vakken basis U R L en Bearer-token.](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    Selecteer **op scim gebaseerde inrichting inschakelen** om de **basis-URL** en **Bearer-token**op te halen en sla de instellingen vervolgens op. Kopieer de **basis-URL** naar de **Tenant-URL**en **Bearer-token**  naar een **geheim token** in de Azure Portal.

7. Klik bij het invullen van de velden die worden weer gegeven in stap 5 op **verbinding testen** om te controleren of Azure AD verbinding kan maken met Zscaler. Als de verbinding mislukt, zorg er dan voor dat uw Zscaler-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Scherm afbeelding van de sectie beheerders referenties met de optie voor het testen van de verbinding.](./media/zscaler-provisioning-tutorial/test-connection.png)

8. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje **e-mail melding verzenden als er een fout optreedt**.

    ![Scherm afbeelding van het tekstvak voor de meldings-e-mail.](./media/zscaler-provisioning-tutorial/notification.png)

9. Klik op **Opslaan**.

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Zscaler**.

    ![Scherm afbeelding van de sectie toewijzingen met de optie Azure Active Directory gebruikers synchroniseren met Zscaler gemarkeerd.](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Zscaler in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Zscaler voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Scherm afbeelding van de sectie kenmerk toewijzingen met zeven toewijzingen weer gegeven.](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren met Zscaler**.

    ![Scherm afbeelding van de sectie toewijzingen met de optie Azure Active Directory groepen synchroniseren met Zscaler gemarkeerd.](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Zscaler in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de groepen in Zscaler te vergelijken voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Scherm afbeelding van de sectie kenmerk toewijzingen waarbij drie toewijzingen worden weer gegeven.](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](./../active-directory-saas-scoping-filters.md).

15. Als u de Azure AD-inrichtings service voor **Zscaler wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Scherm afbeelding van de optie voor de inrichtings status is ingesteld op aan.](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. Definieer de gebruikers en/of groepen die u wilt inrichten voor Zscaler door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Scherm afbeelding van de bereik instelling met de optie alleen toegewezen gebruikers en groepen synchroniseren gemarkeerd.](./media/zscaler-provisioning-tutorial/scoping.png)

17. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Scherm opname van de Zscaler van de Enter prise-toepassing met de optie voor het opslaan van de naam.](./media/zscaler-provisioning-tutorial/save-provisioning.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op Zscaler.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../active-directory-saas-provisioning-reporting.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png
