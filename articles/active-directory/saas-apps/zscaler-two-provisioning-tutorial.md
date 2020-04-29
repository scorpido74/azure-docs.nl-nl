---
title: 'Zelf studie: Zscaler twee configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: In deze zelf studie leert u hoe u Azure Active Directory kunt configureren voor het automatisch inrichten en inrichten van gebruikers accounts op Zscaler twee.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0a250fcd-6ca1-47c2-a780-7a6278186a69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 364b106e7c1f01269ac02b0c2851f8824ea0f58c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77062690"
---
# <a name="tutorial-configure-zscaler-two-for-automatic-user-provisioning"></a>Zelf studie: Zscaler twee configureren voor automatische gebruikers inrichting

In deze zelf studie leert u hoe u Azure Active Directory (Azure AD) configureert om gebruikers en/of groepen automatisch in te richten en in te stellen op Zscaler twee.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die is gebaseerd op de Azure AD User Provisioning-Service. Zie automatisering van gebruikers inrichten en ongedaan maken van de [inrichting van SaaS-toepassingen met Azure Active Directory](../active-directory-saas-app-provisioning.md)voor belang rijke informatie over de werking van deze service en hoe deze werkt, en antwoorden op veelgestelde vragen.

## <a name="prerequisites"></a>Vereisten

Voor het uitvoeren van de stappen die in deze zelf studie worden beschreven, hebt u het volgende nodig:

* Een Azure AD-tenant.
* Een Zscaler twee tenants.
* Een gebruikers account in Zscaler twee met beheerders machtigingen.

> [!NOTE]
> De integratie van Azure AD-inrichting is afhankelijk van de Zscaler twee SCIM-API, die beschikbaar is voor ondernemings accounts.

## <a name="add-zscaler-two-from-the-gallery"></a>Zscaler twee toevoegen vanuit de galerie

Voordat u Zscaler 2 configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u Zscaler twee van de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

Selecteer **Azure Active Directory**in het linkerdeel venster van de [Azure Portal](https://portal.azure.com):

![Selecteer Azure Active Directory](common/select-azuread.png)

Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**:

![Bedrijfstoepassingen](common/enterprise-applications.png)

Als u een toepassing wilt toevoegen, selecteert u **nieuwe toepassing** boven aan het venster:

![Nieuwe toepassing selecteren](common/add-new-app.png)

Voer in het zoekvak **Zscaler twee**in. Selecteer **Zscaler twee** in de resultaten en selecteer vervolgens **toevoegen**.

![Lijst met resultaten](common/search-new-app.png)

## <a name="assign-users-to-zscaler-two"></a>Gebruikers toewijzen aan Zscaler twee

Azure AD-gebruikers moeten toegang krijgen tot de geselecteerde apps voordat ze ze kunnen gebruiken. In de context van het automatisch inrichten van gebruikers worden alleen gebruikers of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot Zscaler twee. Nadat u hebt besloten dat, kunt u deze gebruikers en groepen toewijzen aan Zscaler twee door de instructies in [een gebruiker of groep toewijzen aan een bedrijfs-app te](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)volgen.

### <a name="important-tips-for-assigning-users-to-zscaler-two"></a>Belang rijke tips voor het toewijzen van gebruikers aan Zscaler twee

* We raden u aan eerst één Azure AD-gebruiker toe te wijzen aan Zscaler twee om de configuratie van automatische gebruikers inrichting te testen. U kunt later meer gebruikers en groepen toewijzen.

* Wanneer u een gebruiker toewijst aan Zscaler twee, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

## <a name="set-up-automatic-user-provisioning"></a>Automatische gebruikers inrichting instellen

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD Provisioning-Service om gebruikers en groepen in Zscaler twee te maken, bij te werken en uit te scha kelen op basis van gebruikers-en groeps toewijzingen in azure AD.

> [!TIP]
> Mogelijk wilt u ook eenmalige aanmelding op basis van SAML inschakelen voor Zscaler twee. Als u dit doet, volgt u de instructies in de [zelf studie Zscaler twee eenmalige aanmelding](zscaler-two-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, maar de twee functies vormen een aanvulling op elkaar.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en **Selecteer bedrijfs toepassingen** > **alle toepassingen** > **Zscaler twee**:

    ![Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Zscaler twee**:

    ![Lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** :

    ![Zscaler twee inrichten](./media/zscaler-two-provisioning-tutorial/provisioning-tab.png)

4. Stel de **inrichtings modus** in op **automatisch**:

    ![De inrichtings modus instellen](./media/zscaler-two-provisioning-tutorial/provisioning-credentials.png)

5. Voer in de sectie **beheerders referenties** de **Tenant-URL** en het **geheime token** van uw Zscaler-account in, zoals wordt beschreven in de volgende stap.

6. Als u de **Tenant-URL** en het **geheime token**wilt ophalen, gaat u naar **beheer** > **verificatie-instellingen** in de Zscaler twee portal en selecteert u **SAML** onder **verificatie type**:

    ![Zscaler twee verificatie-instellingen](./media/zscaler-two-provisioning-tutorial/secret-token-1.png)

    Selecteer **SAML configureren** om het venster **SAML configureren** te openen:

    ![SAML-venster configureren](./media/zscaler-two-provisioning-tutorial/secret-token-2.png)

    Selecteer **op scim gebaseerde inrichting inschakelen** en kopieer de **basis-URL** en **Bearer-token**en sla de instellingen op. Plak in het Azure Portal de **basis-URL** in het vak **Tenant-URL** en het **Bearer-token** in het vak geheim van het **token** .

7. Nadat u de waarden in de vakken **Tenant-URL** en **geheim token** hebt opgegeven, selecteert u **verbinding testen** om te controleren of Azure AD verbinding kan maken met Zscaler twee. Als de verbinding mislukt, moet u ervoor zorgen dat uw Zscaler twee accounts beheerders machtigingen hebben en probeer het opnieuw.

    ![De verbinding testen](./media/zscaler-two-provisioning-tutorial/test-connection.png)

8. Voer in het vak **e-mail bericht** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen. Selecteer **een e-mail melding verzenden wanneer er een fout optreedt**:

    ![E-mail melding instellen](./media/zscaler-two-provisioning-tutorial/notification.png)

9. Selecteer **Opslaan**.

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met ZscalerTwo**:

    ![Azure AD-gebruikers synchroniseren](./media/zscaler-two-provisioning-tutorial/user-mappings.png)

11. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Zscaler twee in de sectie **kenmerk toewijzingen** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Zscaler twee voor bijwerk bewerkingen. Selecteer **Opslaan** om eventuele wijzigingen door te voeren.

    ![Kenmerk toewijzingen](./media/zscaler-two-provisioning-tutorial/user-attribute-mappings.png)

12. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren met ZscalerTwo**:

    ![Azure AD-groepen synchroniseren](./media/zscaler-two-provisioning-tutorial/group-mappings.png)

13. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Zscaler twee in de sectie **kenmerk toewijzingen** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om de groepen in Zscaler twee te vergelijken voor bijwerk bewerkingen. Selecteer **Opslaan** om eventuele wijzigingen door te voeren.

    ![Kenmerk toewijzingen](./media/zscaler-two-provisioning-tutorial/group-attribute-mappings.png)

14. Raadpleeg de instructies in de hand leiding voor het filteren op [bereik](./../active-directory-saas-scoping-filters.md)voor meer informatie over het configureren van bereik filters.

15. Als u de Azure AD-inrichtings service voor Zscaler twee wilt inschakelen, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** :

    ![Inrichtings status](./media/zscaler-two-provisioning-tutorial/provisioning-status.png)

16. Definieer de gebruikers en/of groepen die u wilt inrichten voor Zscaler twee door de gewenste waarden onder **bereik** te kiezen in het gedeelte **instellingen** :

    ![Bereik waarden](./media/zscaler-two-provisioning-tutorial/scoping.png)

17. Wanneer u klaar bent om in te richten, selecteert u **Opslaan**:

    ![Opslaan selecteren](./media/zscaler-two-provisioning-tutorial/save-provisioning.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en groepen die zijn gedefinieerd onder **bereik** in de sectie **instellingen** . De initiële synchronisatie duurt langer dan volgende synchronisaties, wat ongeveer elke 40 minuten gebeurt, zolang de Azure AD-inrichtings service wordt uitgevoerd. U kunt de voortgang bewaken in de sectie **synchronisatie Details** . U kunt ook koppelingen volgen naar een rapport met inrichtings activiteiten, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD-inrichtings service op Zscaler twee.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../active-directory-saas-provisioning-reporting.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-03.png
