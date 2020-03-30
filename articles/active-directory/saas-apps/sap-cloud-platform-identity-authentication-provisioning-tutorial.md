---
title: 'Zelfstudie: Identiteitsverificatie van SAP Cloud Platform configureren voor automatische gebruikersinrichting met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor SAP Cloud Platform Identity Authentication.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f49b5738-c769-403b-8f29-84ddeea7fbf1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: c30a7b1e6440cf69f7a4858273b365d885e5ec7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060417"
---
# <a name="tutorial-configure-sap-cloud-platform-identity-authentication-for-automatic-user-provisioning"></a>Zelfstudie: Identiteitsverificatie van SAP Cloud Platform configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in SAP Cloud Platform Identity Authentication en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen voor SAP Cloud Platform Identity Authentication.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant
* [Een SAP Cloud Platform Identity Authentication-tenant](https://cloudplatform.sap.com/pricing.html)
* Een gebruikersaccount in SAP Cloud Platform Identity Authentication met beheerdersmachtigingen.

## <a name="assigning-users-to-sap-cloud-platform-identity-authentication"></a>Gebruikers toewijzen aan SAP Cloud Platform Identity Authentication

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot identiteitsverificatie van SAP Cloud Platform. Eenmaal besloten, u deze gebruikers en/of groepen toewijzen aan SAP Cloud Platform Identity Authentication door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-sap-cloud-platform-identity-authentication"></a>Belangrijke tips voor het toewijzen van gebruikers aan SAP Cloud Platform Identity Authentication

* Het wordt aanbevolen dat één Azure AD-gebruiker wordt toegewezen aan SAP Cloud Platform Identity Authentication om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker toewijst aan SAP Cloud Platform Identity Authentication, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="setup-sap-cloud-platform-identity-authentication-for-provisioning"></a>Sap Cloud Platform Identity Authentication instellen voor inrichten

1. Meld u aan bij uw [SAP Cloud Platform Identity Authentication Admin Console](https://sapmsftintegration.accounts.ondemand.com/admin). Navigeer naar **gebruikers & autorisaties > beheerders**.

    ![Beheerconsole voor identiteitsverificatie van SAP-cloudplatform](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/adminconsole.png)

2.  Maak een beheerdergebruiker en selecteer de gebruiker.  

3.  Schakel onder Autorisaties configureren de knop schakelen in tegen **Gebruikers beheren** en **Groepen beheren**.

    ![SAP Cloud Platform Identity Authentication Add SCIM](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/configurationauth.png)

4. U ontvangt een e-mail om uw account te activeren en een wachtwoord in te stellen voor **SAP Cloud Platform Identity Authentication Service.**

4.  Kopieer de **gebruikersnaam** en **het wachtwoord**. Deze waarden worden respectievelijk ingevoerd in de velden Gebruikersnaam en Beheerderswachtwoord voor beheerders op het tabblad Inrichting van uw SAP Cloud Platform Identity Authentication-toepassing in de Azure-portal.

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Sap Cloud Platform Identity Authentication toevoegen vanuit de galerie

Voordat u SAP Cloud Platform Identity Authentication configureert voor automatische gebruikersvoorziening met Azure AD, moet u SAP Cloud Platform Identity Authentication vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om SAP Cloud Platform Identity Authentication toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **SAP Cloud Platform Identity Authentication,** selecteer SAP Cloud Platform Identity **Authentication** in het deelvenster Resultaten en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![SAP Cloud Platform-identiteitsverificatie in de lijst met resultaten](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-sap-cloud-platform-identity-authentication"></a>Automatische gebruikersinrichting configureren voor SAP Cloud Platform Identity Authentication 

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen te maken, bij te werken en uit te schakelen in sap-cloudplatformidentiteitsverificatie op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-on in te schakelen voor SAP Cloud Platform Identity Authentication, volgens de instructies in de [SAP Cloud Platform Identity Authentication Single sign-on tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial). Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren

### <a name="to-configure-automatic-user-provisioning-for-sap-cloud-platform-identity-authentication-in-azure-ad"></a>Ga als het gaat om het configureren van automatische gebruikersinrichting voor SAP Cloud Platform Identity Authentication in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **SAP Cloud Platform-identiteitsverificatie** in de lijst met toepassingen.

    ![De koppeling SAP Cloud Platform-identiteitsverificatie in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie `https://<tenantID>.accounts.ondemand.com/service/scim ` **Beheerdersreferenties** invoer in **Tenant-URL**in . Voer **de** gebruikersnaam- en **wachtwoordwaarden** in die eerder zijn opgehaald in respectievelijk het gebruikersnaam- en **beheerderswachtwoord van** **de beheerder.** Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met SAP Cloud Platform Identity Authentication. Als de verbinding mislukt, moet u ervoor zorgen dat uw SAP Cloud Platform Identity Authentication-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Tenant URL + Token](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/testconnection.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer azure **Active Directory-gebruikers synchroniseren met SAP Cloud Platform Identity Authentication**in de sectie **Toewijzingen.**

    ![Gebruikerstoewijzingen voor gebruikersvan SAP Cloud Platform Identity Authentication](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/mapping.png)

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar SAP Cloud Platform Identity Authentication in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in SAP Cloud Platform Identity Authentication voor updatebewerkingen te matchen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Gebruikerskenmerken van SAP Cloud Platform Identity Authentication](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/userattributes.png)

10. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

11. Als u de Azure AD-inrichtingsservice voor SAP Cloud Platform Identity Authentication wilt inschakelen, wijzigt u de **inrichtingsstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten voor SAP Cloud Platform Identity Authentication door de gewenste waarden in **Scope** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

13. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitenrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op SAP Cloud Platform Identity Authentication.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Verbindingsbeperkingen

* Het SCIM-eindpunt van SAP Cloud Platform Identity Authentication vereist dat bepaalde kenmerken van specifieke indeling zijn. U meer weten over deze kenmerken en hun specifieke formaat [hier](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/b10fc6a9a37c488a82ce7489b1fab64c.html#).

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)

