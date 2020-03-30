---
title: 'Zelfstudie: Meta Networks Connector configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Meta Networks Connector.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 6e34e540-7761-4bdf-a2f9-8c12becc119d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: 03c2dc6253fba5c2c7d59f3aefc5c1c663ed8248
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061354"
---
# <a name="tutorial-configure-meta-networks-connector-for-automatic-user-provisioning"></a>Zelfstudie: Meta Networks Connector configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Meta Networks Connector en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen voor Meta Networks Connector.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant
* [Een Meta Networks Connector-tenant](https://www.metanetworks.com/)
* Een gebruikersaccount in Meta Networks Connector met beheerdersmachtigingen.

## <a name="assigning-users-to-meta-networks-connector"></a>Gebruikers toewijzen aan Meta Networks Connector

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Meta Networks Connector. Eenmaal besloten, u deze gebruikers en/of groepen toewijzen aan Meta Networks Connector door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-meta-networks-connector"></a>Belangrijke tips voor het toewijzen van gebruikers aan Meta Networks Connector

* Het wordt aanbevolen dat één Azure AD-gebruiker wordt toegewezen aan Meta Networks Connector om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker toewijst aan Meta Networks Connector, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="setup-meta-networks-connector-for-provisioning"></a>Meta Networks Connector instellen voor inrichten

1. Meld u aan bij uw [Meta Networks Connector Admin Console](https://login.metanetworks.com/login/) met de naam van uw organisatie. Navigeer naar **Beheer > API-sleutels**.

    ![Meta Networks Connector Admin Console](media/meta-networks-connector-provisioning-tutorial/apikey.png)

2.  Klik op het plusteken rechtsboven in het scherm om een nieuwe **API-sleutel**te maken.

    ![Pictogram Meta Networks Connector plus](media/meta-networks-connector-provisioning-tutorial/plusicon.png)

3.  Stel de **API-sleutelnaam** en **API-sleutelbeschrijving in**.

    ![Meta Networks Connector Token maken](media/meta-networks-connector-provisioning-tutorial/keyname.png)

4.  Schakel **Schrijfbevoegdheden** in voor **groepen** en **gebruikers.**

    ![Meta Networks Connector-bevoegdheden](media/meta-networks-connector-provisioning-tutorial/privileges.png)

5.  Klik op **Toevoegen**. Kopieer het **geheim** en sla het op, want dit is de enige keer dat je het bekijken. Deze waarde wordt ingevoerd in het veld Geheim token op het tabblad Provisioning van uw Meta Networks Connector-toepassing in de Azure-portal.

    ![Meta Networks Connector Token maken](media/meta-networks-connector-provisioning-tutorial/token.png)

6.  Voeg een IdP toe door te navigeren naar **Beheer > Instellingen > IdP > Nieuw maken**.

    ![Meta Networks Connector IdP toevoegen](media/meta-networks-connector-provisioning-tutorial/newidp.png)

7.  Op de pagina **IdP-configuratie** u uw IdP-configuratie een **naam** geven en een **pictogram**kiezen.

    ![IdP-naam metanetwerkenconnector](media/meta-networks-connector-provisioning-tutorial/idpname.png)

    ![Pictogram IdP-connector-idp-connectormeta-netwerken](media/meta-networks-connector-provisioning-tutorial/icon.png)

8.  Selecteer **onder SCIM configureren** de API-sleutelnaam die in de vorige stappen is gemaakt. Klik op **Opslaan**.

    ![Meta Networks Connector configureren SCIM](media/meta-networks-connector-provisioning-tutorial/configure.png)

9.  Navigeer naar **het tabblad Beheer > Instellingen > IdP**. Klik op de naam van de IdP-configuratie die in de vorige stappen is gemaakt om de **IdP-id**weer te geven. Deze **id** wordt toegevoegd aan het einde van **de URL** van tenant terwijl u de waarde in het **veld Tenant-URL** invoert op het tabblad Inname van uw Meta Networks Connector-toepassing in de Azure-portal.

    ![IdP-id-connector-id metanetwerken](media/meta-networks-connector-provisioning-tutorial/idpid.png)

## <a name="add-meta-networks-connector-from-the-gallery"></a>Meta Networks Connector toevoegen vanuit de galerie

Voordat u Meta Networks Connector configureert voor automatische gebruikersvoorziening met Azure AD, moet u Meta Networks Connector vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Meta Networks Connector toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Meta Networks Connector,** selecteer **Meta Networks Connector** in het deelvenster Resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Meta Networks Connector in de resultatenlijst](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-meta-networks-connector"></a>Automatische gebruikersvoorziening configureren voor Meta Networks Connector 

In deze sectie u de Azure AD-inrichtingsservice configureren om gebruikers en/of groepen in Meta Networks Connector te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-on in te schakelen voor Meta Networks Connector, volgens de instructies in de [Meta Networks Connector Single sign-on tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial). Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren

### <a name="to-configure-automatic-user-provisioning-for-meta-networks-connector-in-azure-ad"></a>Ga als lid van het nieuwe gebruik voor Meta Networks Connector in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Meta Networks Connector** in de lijst met toepassingen.

    ![De koppeling Meta Networks Connector in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie `https://api.metanetworks.com/v1/scim/<IdP ID>` **Beheerdersreferenties** invoer in **Tenant-URL**in . Voer de waarde van de **SCIM-verificatietoken** in die eerder in **Secret Token**is opgehaald. Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Meta Networks Connector. Als de verbinding mislukt, moet u ervoor zorgen dat uw Meta Networks Connector-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer Azure **Active Directory-gebruikers synchroniseren met Meta Networks Connector**onder de sectie **Toewijzingen** .

    ![Gebruikerstoewijzingen van meta-netwerkenconnector](media/meta-networks-connector-provisioning-tutorial/usermappings.png)

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Meta Networks Connector in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Meta Networks Connector te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Gebruikerskenmerken van meta-netwerkenconnector](media/meta-networks-connector-provisioning-tutorial/userattributes.png)

10. Selecteer Azure **Active Directory-groepen synchroniseren met Meta Networks Connector**in de sectie **Toewijzingen** .

    ![Groepstoewijzingen van metanetwerkenconnector](media/meta-networks-connector-provisioning-tutorial/groupmappings.png)

11. Controleer de groepskenmerken die zijn gesynchroniseerd van Azure AD naar Meta Networks Connector in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de groepen in Meta Networks Connector te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Kenmerken van meta-netwerkenconnectorgroep](media/meta-networks-connector-provisioning-tutorial/groupattributes.png)

12. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

13. Als u de Azure AD-inrichtingsservice voor Meta Networks Connector wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten op Meta Networks Connector door de gewenste waarden in **Scope** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

15. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitsrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Meta Networks Connector.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)

