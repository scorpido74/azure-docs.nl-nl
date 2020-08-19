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
ms.openlocfilehash: a3424a42e86b47d4103dd41ca143b5bc7be3796a
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545923"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>Zelf studie: Zscaler configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in Zscaler en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen in Zscaler.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../active-directory-saas-app-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.
>

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u het volgende al hebt:

* Een Azure AD-tenant.
* Een Zscaler-Tenant.
* Een gebruikers account in Zscaler met beheerders machtigingen.

> [!NOTE]
> De integratie van Azure AD-inrichting is afhankelijk van de Zscaler SCIM-API, die beschikbaar is voor Zscaler-ontwikkel aars voor accounts met het ondernemings pakket.

## <a name="adding-zscaler-from-the-gallery"></a>Zscaler toevoegen uit de galerie

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

Azure Active Directory gebruikt een concept met de naam ' toewijzingen ' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers, worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot Zscaler. Eenmaal besloten, kunt u deze gebruikers en/of groepen toewijzen aan Zscaler door de volgende instructies te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>Belang rijke tips voor het toewijzen van gebruikers aan Zscaler

* U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan Zscaler om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan Zscaler, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Automatische gebruikers inrichting configureren voor Zscaler

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in Zscaler te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te scha kelen voor Zscaler, gevolgd door de instructies in de [Zscaler-zelf studie voor eenmalige aanmelding](zscaler-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar behoeven.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor Zscaler in azure AD:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en selecteer **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **Zscaler**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Zscaler**.

    ![De koppeling Zscaler in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Zscaler-inrichting](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Zscaler-inrichting](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. Voer in het gedeelte **beheerders referenties** de **Tenant-URL** en het **geheime token** van uw Zscaler-account in, zoals beschreven in stap 6.

6. Als u de **Tenant-URL** en het **geheime token**wilt ophalen, gaat u naar **beheer > authenticatie-instellingen** in de gebruikers interface van de Zscaler-Portal en klikt u op **SAML** onder **verificatie type**.

    ![Zscaler-inrichting](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    Klik op **SAML configureren** om SAML-opties voor **configuratie** te openen.

    ![Zscaler-inrichting](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    Selecteer **op scim gebaseerde inrichting inschakelen** om de **basis-URL** en **Bearer-token**op te halen en sla de instellingen vervolgens op. Kopieer de **basis-URL** naar de **Tenant-URL**en **Bearer-token**  naar een **geheim token** in de Azure Portal.

7. Klik bij het invullen van de velden die worden weer gegeven in stap 5 op **verbinding testen** om te controleren of Azure AD verbinding kan maken met Zscaler. Als de verbinding mislukt, zorg er dan voor dat uw Zscaler-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Zscaler-inrichting](./media/zscaler-provisioning-tutorial/test-connection.png)

8. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje **e-mail melding verzenden als er een fout optreedt**.

    ![Zscaler-inrichting](./media/zscaler-provisioning-tutorial/notification.png)

9. Klik op **Opslaan**.

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Zscaler**.

    ![Zscaler-inrichting](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Zscaler in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Zscaler voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Zscaler-inrichting](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren met Zscaler**.

    ![Zscaler-inrichting](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Zscaler in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de groepen in Zscaler te vergelijken voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Zscaler-inrichting](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. Raadpleeg de volgende instructies in de [zelf studie](./../active-directory-saas-scoping-filters.md)voor het filteren op bereik voor het configureren van bereik filters.

15. Als u de Azure AD-inrichtings service voor **Zscaler wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Zscaler-inrichting](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. Definieer de gebruikers en/of groepen die u wilt inrichten voor Zscaler door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Zscaler-inrichting](./media/zscaler-provisioning-tutorial/scoping.png)

17. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Zscaler-inrichting](./media/zscaler-provisioning-tutorial/save-provisioning.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op Zscaler.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../active-directory-saas-provisioning-reporting.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png
