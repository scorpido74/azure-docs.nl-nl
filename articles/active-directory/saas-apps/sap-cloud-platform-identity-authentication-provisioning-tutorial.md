---
title: 'Zelf studie: SAP Cloud platform identiteits verificatie configureren voor automatische gebruikers inrichten met Azure Active Directory | Microsoft Docs'
description: Informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts tot SAP Cloud platform identiteits verificatie.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: c330d8000825b2e0ccbdab8c5c1cdefa4d3d45c8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543363"
---
# <a name="tutorial-configure-sap-cloud-platform-identity-authentication-for-automatic-user-provisioning"></a>Zelf studie: verificatie van de SAP-Cloud platform identiteit configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in SAP Cloud platform identiteits verificatie en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen voor SAP Cloud platform identiteits verificatie.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.
>
> Deze connector bevindt zich momenteel in de open bare preview. Zie [aanvullende gebruiksrecht overeenkomst voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de algemene Microsoft Azure gebruiksrecht overeenkomst voor preview-functies.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-Tenant
* [Een SAP Cloud platform identiteits verificatie Tenant](https://cloudplatform.sap.com/pricing.html)
* Een gebruikers account in SAP Cloud platform identiteits verificatie met beheerders machtigingen.

## <a name="assigning-users-to-sap-cloud-platform-identity-authentication"></a>Gebruikers toewijzen aan SAP Cloud platform identiteits verificatie

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot de identiteits verificatie van SAP-Cloud platform. Nadat u hebt besloten, kunt u deze gebruikers en/of groepen toewijzen aan SAP Cloud platform identiteits verificatie door de volgende instructies te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-sap-cloud-platform-identity-authentication"></a>Belang rijke tips voor het toewijzen van gebruikers aan SAP Cloud platform identiteits verificatie

* Het is raadzaam dat één Azure AD-gebruiker wordt toegewezen aan SAP Cloud platform Identity-verificatie om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan SAP Cloud platform identiteits verificatie, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

## <a name="setup-sap-cloud-platform-identity-authentication-for-provisioning"></a>SAP-Cloud platform identiteits verificatie instellen voor inrichting

1. Meld u aan bij de [beheer console voor identiteits verificatie van uw SAP-Cloud platform](https://sapmsftintegration.accounts.ondemand.com/admin). Navigeer naar **gebruikers & autorisaties > beheerders**.

    ![Beheer console voor SAP-Cloud platform identiteits verificatie](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/adminconsole.png)

2.  Druk op de knop **+ toevoegen** in het deel venster aan de linkerkant om een nieuwe beheerder toe te voegen aan de lijst. Kies **systeem toevoegen** en voer de naam van het systeem in.   

> [!NOTE]
> De beheerder-gebruiker in SAP Cloud platform identiteits verificatie moet van het type **System**zijn. Het maken van een normale beheerders gebruiker kan leiden tot *ongeoorloofde* fouten tijdens het inrichten.   

3.  Onder autorisatie configureren selecteert u de wissel knop voor het **beheren van gebruikers** en **groepen beheren**.

    ![SCIM voor SAP-Cloud platform-identiteits verificatie toevoegen](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/configurationauth.png)

4. U ontvangt een e-mail om uw account te activeren en een wacht woord in te stellen voor **SAP Cloud platform Identity Authentication Service**.

4.  Kopieer de **gebruikers-id** en het **wacht woord**. Deze waarden worden ingevoerd in de velden gebruikers naam en Administrator-wacht woord, respectievelijk op het tabblad inrichten van uw SAP Cloud platform identiteits verificatie toepassing in de Azure Portal.

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>SAP Cloud platform identiteits verificatie toevoegen vanuit de galerie

Voordat u SAP Cloud platform identiteits verificatie configureert voor automatische gebruikers toewijzing met Azure AD, moet u SAP Cloud platform identiteits verificatie toevoegen vanuit de Azure AD-toepassings galerie aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om de SAP Cloud platform identiteits verificatie toe te voegen vanuit de Azure AD-toepassings galerie:**

1. Selecteer in de **[Azure Portal](https://portal.azure.com)** in het navigatie venster links **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak SAP- **Cloud platform identiteits verificatie**in, selecteer **SAP Cloud platform identiteits verificatie** in het deel venster resultaten en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![SAP Cloud Platform-identiteitsverificatie in de lijst met resultaten](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-sap-cloud-platform-identity-authentication"></a>Automatische gebruikers inrichting configureren voor identiteits verificatie van SAP-Cloud platform 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen te maken, bij te werken en uit te scha kelen in SAP Cloud platform identiteits verificatie op basis van gebruikers-en/of groeps toewijzingen in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om op SAML gebaseerde eenmalige aanmelding voor SAP Cloud platform identiteits verificatie in te scha kelen, gevolgd door de instructies in de [zelf studie voor het ondertekenen van de SAP-Cloud platform identiteits verificatie](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial). Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar in de compliment

### <a name="to-configure-automatic-user-provisioning-for-sap-cloud-platform-identity-authentication-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor identiteits verificatie van SAP-Cloud platform in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **SAP Cloud Platform-identiteitsverificatie** in de lijst met toepassingen.

    ![De koppeling SAP Cloud Platform-identiteitsverificatie in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. Selecteer in de sectie **beheerders referenties** de invoer `https://<tenantID>.accounts.ondemand.com/service/scim ` in de Tenant- **URL**. Geef de waarden voor de **gebruikers-id** en het **wacht woord** op die u eerder hebt opgehaald in respectievelijk de gebruikers **naam** en het **beheerders wachtwoord** . Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met de SAP Cloud platform identiteits verificatie. Als de verbinding mislukt, zorgt u ervoor dat uw SAP Cloud platform identiteits verificatie account beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/testconnection.png)

6. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in om **een e-mail bericht te verzenden wanneer er een fout optreedt**.

    ![E-mail melding](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met SAP Cloud platform identiteits verificatie**.

    ![Gebruikers toewijzingen voor SAP-Cloud platform identiteits verificatie](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/mapping.png)

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar SAP Cloud platform identiteits verificatie in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in SAP Cloud platform identiteits verificatie voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

    ![Gebruikers kenmerken van SAP-Cloud platform identiteits verificatie](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/userattributes.png)

10. Raadpleeg de volgende instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

11. Als u de Azure AD-inrichtings service voor SAP Cloud platform identiteits verificatie wilt inschakelen, wijzigt **u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten voor de verificatie van de SAP-Cloud platform identiteit door de gewenste waarden in het **bereik** in het gedeelte **instellingen** te kiezen.

    ![Inrichtings bereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op SAP Cloud platform identiteits verificatie.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="connector-limitations"></a>Connector beperkingen

* Het SCIM-eind punt van de SAP-Cloud platform identiteits verificatie vereist dat bepaalde kenmerken een specifieke indeling hebben. [Hier](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/b10fc6a9a37c488a82ce7489b1fab64c.html#)vindt u meer informatie over deze kenmerken en hun specifieke indeling.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)

