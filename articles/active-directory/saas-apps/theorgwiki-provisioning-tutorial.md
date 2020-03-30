---
title: 'Zelfstudie: TheOrgWiki configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen naar TheOrgWiki.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 5eb2096e-f7b6-4a56-a814-0f3d0e788063
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2b0b11ae89e1f0d150b84f49b04a8badcb44e82c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063137"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>Zelfstudie: TheOrgWiki configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in TheOrgWiki en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen naar TheOrgWiki.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant.
* [Een OrgWiki-tenant](https://www.theorgwiki.com/welcome/).
* Een gebruikersaccount in TheOrgWiki met beheerdersmachtigingen.

## <a name="assign-users-to-theorgwiki"></a>Gebruikers toewijzen aan TheOrgWiki

Azure Active Directory gebruikt een concept genaamd toewijzingen om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot TheOrgWiki nodig hebben. Eenmaal besloten, u deze gebruikers en/of groepen toewijzen aan TheOrgWiki door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>Belangrijke tips voor het toewijzen van gebruikers aan TheOrgWiki

* Het wordt aanbevolen dat één Azure AD-gebruiker aan TheOrgWiki wordt toegewezen om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan TheOrgWiki toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="set-up-theorgwiki-for-provisioning"></a>TheOrgWiki instellen voor inrichten

Voordat u TheOrgWiki configureert voor automatische gebruikersvoorziening met Azure AD, moet u SCIM-provisioning op TheOrgWiki inschakelen.

1. Meld u aan bij uw [TheOrgWiki-beheerconsole](https://www.theorgwiki.com/login/). Klik op **Admin Console**.

    ![TheOrgWiki Add SCIM](media/theorgwiki-provisioning-tutorial/login.png)

2. Klik in de beheerconsole op **het tabblad Instellingen**. 

    ![TheOrgWiki Add SCIM](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. Navigeer naar **Serviceaccounts**.

    ![TheOrgWiki Add SCIM](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. Klik op **+Service Account**. Selecteer onder **Serviceaccounttype** **token gebaseerd**. Klik op **Opslaan**.

    ![TheOrgWiki Add SCIM](media/theorgwiki-provisioning-tutorial/auth.png)

5.  Kopieer de **actieve tokens.** Deze waarde wordt ingevoerd in het veld Secret Token op het tabblad Provisioning van uw TheOrgWiki-toepassing in de Azure-portal.
     
    ![TheOrgWiki Add SCIM](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>TheOrgWiki toevoegen vanuit de galerie

Als u TheOrgWiki wilt configureren voor automatische gebruikersinrichting met Azure AD, moet u TheOrgWiki vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **TheOrgWiki**in, selecteer **TheOrgWiki** in het resultatenpaneel. 

    ![TheOrgWiki in de resultatenlijst](common/search-new-app.png)

5. Selecteer de **knop Aanmelden voor TheOrgWiki** die u doorverwijst naar de inlogpagina van TheOrgWiki. 

    ![TheOrgWiki Add SCIM](media/theorgwiki-provisioning-tutorial/image00.png)

6.  Selecteer **Rechtsboven de**optie Inloggen .

    ![TheOrgWiki Add SCIM](media/theorgwiki-provisioning-tutorial/image02.png)

7. Aangezien TheOrgWiki een OpenIDConnect-app is, kiest u ervoor om met uw Microsoft-werkaccount in te loggen op OrgWiki.

    ![TheOrgWiki Add SCIM](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. Na een succesvolle verificatie wordt de applicatie automatisch toegevoegd aan uw tenant en wordt u doorgestuurd naar uw TheOrgWiki-account.

    ![OrgWiki Voeg SCIM toe](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>Automatische gebruikersinrichting configureren naar TheOrgWiki 

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen in TheOrgWiki te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>Ga als een te meer met de automatische gebruikersinrichting voor TheOrgWiki in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **TheOrgWiki**in de lijst met toepassingen .

    ![De OrgWiki-koppeling in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie `https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/` **Beheerdersreferenties** invoer in **Tenant-URL**in . 

    Voorbeeld: `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> De **subdomeinwaarde** kan alleen worden ingesteld tijdens het eerste aanmeldingsproces voor TheOrgWiki.
 
6. Voer de tokenwaarde in in het veld **Secret Token,** die u eerder van TheOrgWiki hebt opgehaald. Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met TheOrgWiki. Als de verbinding mislukt, moet u ervoor zorgen dat uw TheOrgWiki-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

7. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

8. Klik op **Opslaan**.

9. Selecteer Azure **Active Directory-gebruikers synchroniseren met TheOrgWiki**onder de sectie **Toewijzingen** .

    ![TheOrgWiki-gebruikerstoewijzingen](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar TheOrgWiki in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in TheOrgWiki te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![TheOrgWiki-gebruikerskenmerken](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

12. Als u de Azure AD-inrichtingsservice voor TheOrgWiki wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

13. Definieer de gebruikers en/of groepen die u aan OrgWiki wilt inrichten door de gewenste waarden in **Scope** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

14. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan latere synchronisaties. Zie [Hoe lang het duurt voordat](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)gebruikers en/of groepen worden ingericht voor meer informatie over hoe lang het duurt voordat gebruikers zijn voorzien.

U de sectie **Huidige status** gebruiken om de voortgang te volgen en koppelingen naar uw installatieactiviteitsrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op TheOrgWiki. Zie [De status van gebruikersinrichting controleren](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)voor meer informatie . Zie [Rapportage over automatische gebruikersaccountinrichting](../app-provisioning/check-status-user-account-provisioning.md)voor het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md).