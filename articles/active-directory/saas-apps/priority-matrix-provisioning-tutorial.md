---
title: 'Zelfstudie: Prioriteitsmatrix configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Priority Matrix.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: a4598a99-3c98-4c14-86c2-95cc562e2439
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2019
ms.author: Zhchia
ms.openlocfilehash: 80ffaba6713027d216958e0be2cd4ae35a8d2d70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063436"
---
# <a name="tutorial-configure-priority-matrix-for-automatic-user-provisioning"></a>Zelfstudie: Prioriteitsmatrix configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Priority Matrix en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen voor Priority Matrix.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant
* [Een Priority Matrix-tenant](https://appfluence.com/pricing/)
* Een gebruikersaccount in een Priority Matrix met beheerdersmachtigingen.

## <a name="assign-users-to-priority-matrix"></a>Gebruikers toewijzen aan Priority Matrix

Azure Active Directory gebruikt een concept genaamd toewijzingen om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot Priority Matrix nodig hebben. Zodra u hebt besloten, u deze gebruikers en/of groepen toewijzen aan Priority Matrix door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-priority-matrix"></a>Belangrijke tips voor het toewijzen van gebruikers aan Priority Matrix

* Het wordt aanbevolen dat één Azure AD-gebruiker wordt toegewezen aan Priority Matrix om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan Priority Matrix toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="set-up-priority-matrix-for-provisioning"></a>Prioriteitsmatrix instellen voor inrichten

Voordat u Priority Matrix configureert voor automatische gebruikersvoorziening met Azure AD, moet u bepaalde informatie ophalen uit Priority Matrix.

1. Meld u aan bij uw [Priority Matrix-beheerconsole](https://sync.appfluence.com/accounts/login/?next=/accounts/provisioning).

3. Klik **op Oauth-aanmeldingstoken** voor Priority Matrix

    ![Priority Matrix Voeg SCIM toe](media/priority-matrix-provisioning-tutorial/oauthlogin.png)

4. Klik op de knop **NIEUW TOKEN DOWNLOADEN.** Kopieer de **tekenreeks .** Deze waarde wordt ingevoerd in het veld **Geheim token** op het tabblad Provisioning van uw Priority Matrix-toepassing in de Azure-portal. 

    ![Token voor prioriteitsmatrix maken](media/priority-matrix-provisioning-tutorial/token.png)

## <a name="add-priority-matrix-from-the-gallery"></a>Prioriteitsmatrix toevoegen vanuit de galerie

Als u Priority Matrix wilt configureren voor automatische gebruikersvoorziening met Azure AD, moet u Priority Matrix vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Priority Matrix**in het zoekvak , selecteer **Priority Matrix** in het deelvenster Resultaten. 

    ![Priority Matrix in de resultatenlijst](common/search-new-app.png)

5. Selecteer de knop **Aanmelden voor Priority Matrix** waarmee u wordt omgeleid naar de aanmeldingspagina van Priority Matrix. 

    ![Priority Matrix OIDC Toevoegen](media/priority-matrix-provisioning-tutorial/signup.png)

6. Aangezien Priority Matrix een OpenIDConnect-app is, kiest u ervoor om u aan te sluiten bij Priority Matrix met uw Microsoft-werkaccount.

    ![Priority Matrix OIDC login](media/priority-matrix-provisioning-tutorial/msftsignin.png)

7. Na een succesvolle verificatie accepteert u de toestemmingsprompt voor de toestemmingspagina. De applicatie wordt dan automatisch toegevoegd aan uw tenant en u wordt doorgestuurd naar uw Priority Matrix-account.

    ![Priority Matrix OIDc Consent](media/priority-matrix-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-priority-matrix"></a>Automatische gebruikersvoorziening configureren voor Priority Matrix 

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen in Priority Matrix te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!NOTE]
> Voor meer informatie over het SCIM-eindpunt van Priority Matrix raadpleegt u [Gebruikersinrichting en Priority Matrix](https://appfluence.com/help/article/user-provisioning/).

### <a name="to-configure-automatic-user-provisioning-for-priority-matrix-in-azure-ad"></a>Ga als een te meer met de automatische gebruikersvoorziening voor Priority Matrix in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Priority Matrix**in de lijst met toepassingen .

    ![De koppeling Priority Matrix in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie `https://sync.appfluence.com/scim/v2/` **Beheerdersreferenties** invoer in **Tenant-URL**in . Voer de waarde in die u eerder hebt opgehaald en opgeslagen uit Priority Matrix in **Secret Token**. Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Priority Matrix. Als de verbinding mislukt, moet u ervoor zorgen dat uw Priority Matrix-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer Azure **Active Directory-gebruikers synchroniseren met Priority Matrix**onder de sectie **Toewijzingen** .

    ![Priority Matrix-gebruikerstoewijzingen](media/priority-matrix-provisioning-tutorial/usermappings.png)

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Priority Matrix in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Priority Matrix te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Gebruikerskenmerken van priority matrix](media/priority-matrix-provisioning-tutorial/userattributes.png)

10. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

11. Als u de Azure AD-inrichtingsservice voor Priority Matrix wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten in Priority Matrix door de gewenste waarden in **Bereik** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

13. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitsrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Priority Matrix.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)


