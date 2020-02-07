---
title: 'Zelf studie: sneeuw configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers accounts op sneeuw.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f9ce85f4-0992-4bc6-8276-4b2efbce8dcb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2c5d91894ba35233f3fbebffdff9104edcfdd27b
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063139"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Zelf studie: sneeuw configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in sneeuw en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen op sneeuw.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.
>
> Deze connector bevindt zich momenteel in de open bare preview. Zie [aanvullende gebruiksrecht overeenkomst voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de algemene Microsoft Azure gebruiksrecht overeenkomst voor preview-functies.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-tenant.
* [Een sneeuw-Tenant](https://www.Snowflake.com/pricing/).
* Een gebruikers account in sneeuw met beheerders machtigingen.

## <a name="assigning-users-to-snowflake"></a>Gebruikers toewijzen aan sneeuw vlokken

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot sneeuw. Nadat u hebt besloten, kunt u deze gebruikers en/of groepen toewijzen aan sneeuw door de volgende instructies te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-snowflake"></a>Belang rijke tips voor het toewijzen van gebruikers aan sneeuw

* Het is raadzaam om één Azure AD-gebruiker toe te wijzen aan sneeuw om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan sneeuw, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

## <a name="setup-snowflake-for-provisioning"></a>Sneeuw vlokken instellen voor inrichting

Voordat u een sneeuw gaat configureren voor automatische gebruikers inrichting met Azure AD, moet u SCIM inrichten inschakelen op sneeuw vlokken.

1. Meld u aan bij uw sneeuw-beheer console. Voer de query in die hieronder wordt weer gegeven in het werk blad en klik op **uitvoeren**.

    ![Sneeuw-beheer console](media/Snowflake-provisioning-tutorial/image00.png)

2.  Er wordt een SCIM-toegangs token gegenereerd voor uw sneeuw-Tenant. Als u deze wilt ophalen, klikt u op de gemarkeerde koppeling hieronder.

    ![Sneeuw SCIM toevoegen](media/Snowflake-provisioning-tutorial/image01.png)

3. Kopieer de gegenereerde token waarde en klik op **gereed**. Deze waarde wordt ingevoerd in het veld **geheime token** op het tabblad inrichten van uw sneeuw-toepassing in de Azure Portal.

    ![Sneeuw SCIM toevoegen](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="add-snowflake-from-the-gallery"></a>Sneeuw toevoegen vanuit de galerie

Als u sneeuw wilt configureren voor het automatisch inrichten van gebruikers met Azure AD, moet u addSnowflake van de Azure AD-toepassings galerie naar uw lijst met beheerde SaaS-toepassingen.

**Als u een sneeuw wilt toevoegen vanuit de Azure AD-toepassings galerie, voert u de volgende stappen uit:**

1. Selecteer in de **[Azure Portal](https://portal.azure.com)** in het navigatie venster links **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **sneeuw**in, selecteer **sneeuw** in het resultaten paneel en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![Snowflake toevoegen vanuit de galerie](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-snowflake"></a>Automatische gebruikers inrichting configureren voor sneeuw 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in sneeuw te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te scha kelen voor sneeuw, gevolgd door de instructies in de [zelf studie eenmalige aanmelding met sneeuw vlokken](Snowflake-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar behoeven.

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>Automatische gebruikers inrichting voor sneeuw configureren in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **sneeuw vlokken**.

    ![De koppeling naar Snowflake in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. Selecteer in de sectie beheerders referenties de invoer `https://<Snowflake Account URL>/scim/v2` in de Tenant-URL. Een voor beeld van de Tenant-URL: `https://acme.snowflakecomputing.com/scim/v2`

6. Voer de waarde voor het **scim-verificatie token** in die eerder is opgehaald in het **geheime token**. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met sneeuw vlokken. Als de verbinding mislukt, zorg er dan voor dat uw sneeuw-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

7. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in om **een e-mail bericht te verzenden wanneer er een fout optreedt**.

    ![E-mail melding](common/provisioning-notification-email.png)

8. Klik op **Opslaan**.

9. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren naar sneeuw vlokken**.

    ![Gebruikers toewijzingen sneeuw vlokken](media/Snowflake-provisioning-tutorial/user-mapping.png)

10. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar sneeuw vlokken in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in sneeuw voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Gebruikers kenmerken sneeuw vlokken](media/Snowflake-provisioning-tutorial/user-attribute.png)

11. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren met sneeuw vlokken**.

    ![Sneeuw groeps toewijzingen](media/Snowflake-provisioning-tutorial/group-mapping.png)

12. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD op sneeuw vlokken in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de groepen in sneeuw te vergelijken voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Eigenschappen van sneeuw groep](media/Snowflake-provisioning-tutorial/group-attribute.png)

13. Raadpleeg de volgende instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

14. Als u de Azure AD-inrichtings service voor sneeuw wilt inschakelen, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

15. Definieer de gebruikers en/of groepen die u wilt inrichten voor sneeuw door de gewenste waarden in het **bereik** in het gedeelte **instellingen** te kiezen. Als deze optie niet beschikbaar is, configureert u de vereiste velden onder beheerders referenties, klikt u op **Opslaan** en de pagina vernieuwen. 

    ![Inrichtings bereik](common/provisioning-scope.png)

16. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

    Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op sneeuw vlokken.

    Voor meer informatie over het lezen van de Azure AD-inrichtings logboeken raadpleegt u [rapportage over automatische gebruikers accounts inrichten](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Connector beperkingen

* Gegenereerde SCIM-tokens verlopen in 6 maanden. Houd er rekening mee dat deze moeten worden vernieuwd voordat deze verloopt om de inrichtings synchronisaties te kunnen blijven gebruiken. 

## <a name="additional-resources"></a>Aanvullende resources

* Het [inrichten van een gebruikers account voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over inrichtings activiteiten](../app-provisioning/check-status-user-account-provisioning.md).
