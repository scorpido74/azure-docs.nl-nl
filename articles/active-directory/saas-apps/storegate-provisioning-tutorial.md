---
title: 'Zelfstudie: Storegate configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Storegate.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 628fa804-c0e6-4db1-ab6b-46ee9aab4d41
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: 72903a36f88f9092ce1d203b557003083407320b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064254"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>Zelfstudie: Storegate configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Storegate en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen naar Storegate.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant
* [Een Storegate-huurder](https://www.storegate.com)
* Een gebruikersaccount op een Storegate met beheerdersmachtigingen.

## <a name="assign-users-to-storegate"></a>Gebruikers toewijzen aan Storegate

Azure Active Directory gebruikt een concept genaamd toewijzingen om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot Storegate nodig hebben. Eenmaal besloten, u deze gebruikers en/of groepen toewijzen aan Storegate door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>Belangrijke tips voor het toewijzen van gebruikers aan Storegate

* Het wordt aanbevolen dat één Azure AD-gebruiker aan Storegate wordt toegewezen om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan Storegate toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="set-up-storegate-for-provisioning"></a>Storegate instellen voor inrichten

Voordat u Storegate configureert voor automatische gebruikersvoorziening met Azure AD, moet u bepaalde informatie ophalen bij Storegate.

1. Meld u aan bij uw [Storegate-beheerconsole](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) en navigeer naar de instellingen door te klikken op het gebruikerspictogram in de rechterbovenhoek en **selecteer Accountinstellingen**.

    ![Storegate Voeg SCIM toe](media/storegate-provisioning-tutorial/admin.png)

2. Navigeer in de instellingen **naar Team > Instellingen** en controleer of de schakelaar is ingeschakeld in de sectie Eén **aanmelding.**

    ![Storegate-instellingen](media/storegate-provisioning-tutorial/team.png)

    ![Knop Storegate-to-ggle](media/storegate-provisioning-tutorial/sso.png)

3. Kopieer de URL en **token** **van de tenant** . Deze waarden worden respectievelijk ingevoerd in de velden Url van **tenant** en **geheim token** op het tabblad Inrichten van uw Storegate-toepassing in de Azure-portal. 

    ![Token storegate maken](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>Storegate toevoegen vanuit de galerie

Als u Storegate wilt configureren voor automatische gebruikersvoorziening met Azure AD, moet u Storegate vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer **Storegate in**het zoekvak in, selecteer **Storegate** in het resultatenpaneel. 

    ![Storegate in de resultatenlijst](common/search-new-app.png)

5. Selecteer de knop **Aanmelden voor Storegate** die u doorverwijst naar de aanmeldingspagina van Storegate. 

    ![Toevoegen aan storegate OIDC](media/storegate-provisioning-tutorial/signup.png)

6.  Meld u aan bij uw [Storegate-beheerconsole](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) en navigeer naar de instellingen door te klikken op het gebruikerspictogram in de rechterbovenhoek en **selecteer Accountinstellingen**.

    ![Storegate-aanmelding](media/storegate-provisioning-tutorial/admin.png)

7. Ga in de instellingen naar **Team >-instellingen** en klik op de schakelaar in de sectie Eén aanmelding, hiermee wordt de toestemmingsstroom gestart. Klik op **Activeren**.

    ![Storegate-team](media/storegate-provisioning-tutorial/team.png)

    ![Storegate sso](media/storegate-provisioning-tutorial/sso.png)

    ![Storegate geactiveerd](media/storegate-provisioning-tutorial/activate.png)

8. Aangezien Storegate een OpenIDConnect-app is, kiest u ervoor om in te loggen bij Storegate met uw Microsoft-werkaccount.

    ![Storegate OIDC login](media/storegate-provisioning-tutorial/login.png)

9. Na een succesvolle verificatie accepteert u de toestemmingsprompt voor de toestemmingspagina. De applicatie wordt dan automatisch toegevoegd aan uw tenant en u wordt doorgestuurd naar uw Storegate-account.

    ![Storegate OIDc Toestemming](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>Automatische gebruikersvoorziening configureren voor Storegate 

In deze sectie u de Azure AD-inrichtingsservice configureren om gebruikers en/of groepen in Storegate te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!NOTE]
> Voor meer informatie over het SCIM-eindpunt van Storegate [raadpleegt u dit](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/).

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>Automatische gebruikersvoorziening voor Storegate in Azure AD configureren

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Storegate**in de lijst met toepassingen .

    ![De koppeling Storegate in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie `https://dialpad.com/scim` **Beheerdersreferenties** invoer in **Tenant-URL**in . Voer de waarde in die u eerder hebt opgehaald en opgeslagen in Storegate in **Secret Token.** Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Storegate. Als de verbinding mislukt, moet u ervoor zorgen dat uw Storegate-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer Azure **Active Directory-gebruikers synchroniseren met Storegate**onder de sectie **Toewijzingen** .

    ![Gebruikerstoewijzingen storegate](media/storegate-provisioning-tutorial/usermappings.png)

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Storegate in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Storegate te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Gebruikerskenmerken van storegate](media/storegate-provisioning-tutorial/userattributes.png)

10. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

11. Als u de Azure AD-inrichtingsservice voor Storegate wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten voor Storegate door de gewenste waarden in **Bereik** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

13. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitenrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Storegate.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)
