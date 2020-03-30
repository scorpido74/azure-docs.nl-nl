---
title: 'Zelfstudie: Soloinsight-CloudGate SSO configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Soloinsight-CloudGate SSO.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 07558ceb-d406-40e7-90b8-1b40fdc829e7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 6ab90a6aea262d5c7067f9f41b9ddfc090b7371d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063191"
---
# <a name="tutorial-configure-soloinsight-cloudgate-sso-for-automatic-user-provisioning"></a>Zelfstudie: Soloinsight-CloudGate SSO configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Soloinsight-CloudGate SSO en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionenten voor Soloinsight-CloudGate SSO.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant
* [Een Soloinsight-CloudGate SSO-tenant](https://www.soloinsight.com/)
* Een gebruikersaccount in Soloinsight-CloudGate SSO met beheerdersmachtigingen.

## <a name="assigning-users-to-soloinsight-cloudgate-sso"></a>Gebruikers toewijzen aan Soloinsight-CloudGate SSO

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Soloinsight-CloudGate SSO. Eenmaal besloten, u deze gebruikers en/of groepen toewijzen aan Soloinsight-CloudGate SSO door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-soloinsight-cloudgate-sso"></a>Belangrijke tips voor het toewijzen van gebruikers aan Soloinsight-CloudGate SSO

* Het wordt aanbevolen dat één Azure AD-gebruiker wordt toegewezen aan Soloinsight-CloudGate SSO om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan Soloinsight-CloudGate SSO toeschrijft, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="set-up-soloinsight-cloudgate-sso-for-provisioning"></a>Soloinsight-CloudGate SSO instellen voor inrichten

1. Meld u aan bij uw [Soloinsight-CloudGate SSO-beheerconsole.](https://soloinsight.sigateway.com/login) Navigeer naar **Beheer > systeeminstellingen**.

    ![Soloinsight-CloudGate SSO-beheerconsole](media/soloinsight-cloudgate-sso-provisioning-tutorial/admin.png)

2.  Navigeer naar **Algemeen**.

    ![Soloinsight-CloudGate SSO Voegt SCIM toe](media/soloinsight-cloudgate-sso-provisioning-tutorial/config.png)

3.  Schuif omlaag naar het einde van de pagina om de **URL van de tenant** en het geheime token **te**krijgen. Kopieer het **geheime token.** Deze waarde wordt ingevoerd in het veld Secret Token op het tabblad Provisioning van uw Soloinsight-CloudGate SSO-toepassing in de Azure-portal.

    ![Soloinsight-CloudGate SSO Create Token](media/soloinsight-cloudgate-sso-provisioning-tutorial/token.png)

## <a name="add-soloinsight-cloudgate-sso-from-the-gallery"></a>Soloinsight-CloudGate SSO toevoegen vanuit de galerij

Voordat u Soloinsight-CloudGate SSO configureert voor automatische gebruikersvoorziening met Azure AD, moet u Soloinsight-CloudGate SSO uit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Soloinsight-CloudGate SSO toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Soloinsight-CloudGate SSO**in het zoekvak, selecteer **Soloinsight-CloudGate SSO** in het resultatenpaneel en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Soloinsight-CloudGate SSO in de resultatenlijst](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-soloinsight-cloudgate-sso"></a>Automatische gebruikersinrichting configureren voor Soloinsight-CloudGate SSO 

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen te maken, bij te werken en uit te schakelen in Soloinsight-CloudGate SSO op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-on in te schakelen voor Soloinsight-CloudGate SSO, volgens de instructies in de [Soloinsight-CloudGate SSO Single sign-on tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial). Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren

### <a name="to-configure-automatic-user-provisioning-for-soloinsight-cloudgate-sso-in-azure-ad"></a>Ga als het gaat om het configureren van automatische gebruikersinrichting voor Soloinsight-CloudGate SSO in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen **Soloinsight-CloudGate SSO**.

    ![De koppeling voor Soloinsight-CloudGate SSO in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie `https://sigateway.com/scim/v2/sync/serviceproviderconfig` **Beheerdersreferenties** invoer in **Tenant-URL**in . Voer de waarde van de **SCIM-verificatietoken** in die eerder in **Secret Token**is opgehaald. Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Soloinsight-CloudGate SSO. Als de verbinding mislukt, moet u ervoor zorgen dat uw Soloinsight-CloudGate SSO-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer Azure **Active Directory-gebruikers synchroniseren met Soloinsight-CloudGate SSO**onder de sectie **Toewijzingen** .

    ![Soloinsight-CloudGate SSO-gebruikerstoewijzingen](media/soloinsight-cloudgate-sso-provisioning-tutorial/usermappings.png)

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Soloinsight-CloudGate SSO in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Soloinsight-CloudGate SSO te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Soloinsight-CloudGate SSO-gebruikerskenmerken](media/soloinsight-cloudgate-sso-provisioning-tutorial/userattributes.png)

10. Selecteer Azure **Active Directory-groepen synchroniseren met Soloinsight-CloudGate SSO**onder de sectie **Toewijzingen** .

    ![Soloinsight-CloudGate SSO-groepstoewijzingen](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupmappings.png)

11. Bekijk de groepskenmerken die zijn gesynchroniseerd van Azure AD naar Soloinsight-CloudGate SSO in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de groepen in Soloinsight-CloudGate SSO te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Soloinsight-CloudGate SSO-groepskenmerken](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupattributes.png)

12. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

13. Als u de Azure AD-inrichtingsservice voor Soloinsight-CloudGate SSO wilt inschakelen, wijzigt u de **inrichtingsstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten op Soloinsight-CloudGate SSO door de gewenste waarden in **Scope** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

15. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitenrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Soloinsight-CloudGate SSO.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)

