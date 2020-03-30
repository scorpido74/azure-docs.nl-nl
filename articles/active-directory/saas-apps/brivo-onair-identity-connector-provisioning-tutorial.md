---
title: 'Zelfstudie: Brivo Onair-identiteitsconnector configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Brivo Onair Identity Connector.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 542ce04c-ef7d-4154-9b0e-7f68e1154f6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: f3c1e7337c0ce07b7fbebb9f954deeb75f0b9584
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246650"
---
# <a name="tutorial-configure-brivo-onair-identity-connector-for-automatic-user-provisioning"></a>Zelfstudie: Brivo Onair-identiteitsconnector configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Brivo Onair Identity Connector en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen voor Brivo Onair Identity Connector.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant
* [Een brivo Onair Identity Connector-tenant](https://www.brivo.com/lp/quote)
* Een gebruikersaccount in Brivo Onair Identity Connector met machtigingen voor senior administrator.

## <a name="assigning-users-to-brivo-onair-identity-connector"></a>Gebruikers toewijzen aan Brivo Onair Identity Connector

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang nodig hebben tot Brivo Onair Identity Connector. Eenmaal besloten, u deze gebruikers en/of groepen toewijzen aan Brivo Onair Identity Connector door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-brivo-onair-identity-connector"></a>Belangrijke tips voor het toewijzen van gebruikers aan Brivo Onair Identity Connector

* Het wordt aanbevolen dat één Azure AD-gebruiker wordt toegewezen aan Brivo Onair Identity Connector om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker toewijst aan Brivo Onair Identity Connector, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="setup-brivo-onair-identity-connector-for-provisioning"></a>Brivo Onair-identiteitsconnector instellen voor inrichten

1.    Meld u aan bij uw [Brivo Onair Identity Connector Admin Console](https://acs.brivo.com/login/). Navigeer naar **Account > Accountinstellingen**.

    ![Brivo Onair-identiteitsconnector-beheerconsole](media/brivo-onair-identity-connector-provisioning-tutorial/admin.png)

2.  Klik op **het tabblad Ad van Azure.** Voer op de pagina **Azure AD-gegevens** het wachtwoord van uw senior administrator-account opnieuw in. Klik op **Verzenden**.

    ![Azure van Brivo Onair Identity Connector](media/brivo-onair-identity-connector-provisioning-tutorial/azuread.png)

3.    Klik op de knop **Token kopiëren** en sla de geheime **token op.** Deze waarde wordt ingevoerd in het veld Geheim token op het tabblad Inrichten van uw Brivo Onair Identity Connector-toepassing in de Azure-portal.

    ![Brivo Onair-identiteitsconnector-token](media/brivo-onair-identity-connector-provisioning-tutorial/token.png)

## <a name="add-brivo-onair-identity-connector-from-the-gallery"></a>Brivo Onair-identiteitsconnector toevoegen vanuit de galerie

Voordat u Brivo Onair Identity Connector configureert voor automatische gebruikersvoorziening met Azure AD, moet u Brivo Onair Identity Connector toevoegen vanuit de Azure AD-toepassingsgalerie aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Brivo Onair Identity Connector toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **brivo onair-identiteitsconnector**in het zoekvak, selecteer **Brivo Onair Identity Connector** in het deelvenster Resultaten en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Brivo Onair Identity Connector in de resultatenlijst](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-brivo-onair-identity-connector"></a>Automatische gebruikersvoorziening configureren voor Brivo Onair Identity Connector 

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen in Brivo Onair Identity Connector te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-brivo-onair-identity-connector-in-azure-ad"></a>Ga als het gaat om het configureren van automatische gebruikersinrichting voor Brivo Onair Identity Connector in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Brivo Onair Identity Connector**in de lijst met toepassingen .

    ![De koppeling Brivo Onair Identity Connector in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie `https://scim.brivo.com/ActiveDirectory/v2/` **Beheerdersreferenties** invoer in **Tenant-URL**in . Voer de waarde van de **SCIM-verificatietoken** in die eerder in **Secret Token**is opgehaald. Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Brivo Onair Identity Connector. Als de verbinding mislukt, moet u ervoor zorgen dat uw Brivo Onair Identity Connector-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer Azure **Active Directory-gebruikers synchroniseren met Brivo Onair Identity Connector**in de sectie **Toewijzingen** .

    ![Gebruikerstoewijzingen van Brivo Onair Identity Connector](media/brivo-onair-identity-connector-provisioning-tutorial/user-mappings.png )

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Brivo Onair Identity Connector in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Brivo Onair Identity Connector te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Gebruikerskenmerken van brivo Onair Identity Connector](media/brivo-onair-identity-connector-provisioning-tutorial/user-attributes.png)

10. Selecteer azure Active Directory-groepen synchroniseren in de sectie **Toewijzingen** in de sectie **Azure Active Directory Groups synchroniseren met Brivo Onair Identity Connector**.

    ![Brivo Onair Identity Connector Group Mappings](media/brivo-onair-identity-connector-provisioning-tutorial/group-mappings.png)

11. Controleer de groepskenmerken die zijn gesynchroniseerd van Azure AD naar Brivo Onair Identity Connector in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de groepen in Brivo Onair Identity Connector te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Kenmerken van brivo-identiteitsconnector](media/brivo-onair-identity-connector-provisioning-tutorial/group-attributes.png)

12. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

13. Als u de Azure AD-inrichtingsservice voor Brivo Onair Identity Connector wilt inschakelen, wijzigt u de **inrichtingsstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten in Brivo Onair Identity Connector door de gewenste waarden in **Scope** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

15. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitsrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Brivo Onair Identity Connector.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)

