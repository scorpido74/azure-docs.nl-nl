---
title: 'Zelfstudie: Smartsheet configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Smartsheet.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: 9fbdf8a1c4b1881fc6dfd9d7b95a4103761e9ce7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063183"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Zelfstudie: Smartsheet configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Smartsheet en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen voor Smartsheet.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant
* [Een Smartsheet-tenant](https://www.smartsheet.com/pricing)
* Een gebruikersaccount op een Smartsheet Enterprise- of Enterprise Premier-abonnement met systeembeheerdersmachtigingen.

## <a name="assign-users-to-smartsheet"></a>Gebruikers toewijzen aan Smartsheet

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot Smartsheet nodig hebben. Zodra u hebt besloten, u deze gebruikers en/of groepen aan Smartsheet toewijzen door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>Belangrijke tips voor het toewijzen van gebruikers aan Smartsheet

* Het wordt aanbevolen dat één Azure AD-gebruiker aan Smartsheet wordt toegewezen om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan Smartsheet toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

* Om pariteit in gebruikersroltoewijzingen tussen Smartsheet en Azure AD te garanderen, wordt aanbevolen om dezelfde roltoewijzingen te gebruiken die in de volledige Lijst met Smartsheet-gebruikers worden ingevuld. Als u deze gebruikerslijst wilt ophalen uit Smartsheet, navigeert u naar **Accountbeheerder > Gebruikersbeheer > Meer acties > gebruikerslijst downloaden (csv).**

* Om toegang te krijgen tot bepaalde functies in de app, vereist Smartsheet dat een gebruiker meerdere rollen heeft. Ga voor meer informatie over gebruikerstypen en machtigingen in Smartsheet naar [Gebruikerstypen en machtigingen](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Als een gebruiker meerdere rollen in Smartsheet heeft toegewezen, **moet** u ervoor zorgen dat deze roltoewijzingen worden gerepliceerd in Azure AD om een scenario te voorkomen waarin gebruikers de toegang tot Smartsheet-objecten permanent kunnen verliezen. Elke unieke rol in Smartsheet **moet** worden toegewezen aan een andere groep in Azure AD. De gebruiker **moet** vervolgens worden toegevoegd aan elk van de groepen die overeenkomen met gewenste rollen. 

## <a name="set-up-smartsheet-for-provisioning"></a>Smartsheet instellen voor inrichten

Voordat u Smartsheet configureert voor automatische gebruikersvoorziening met Azure AD, moet u SCIM-inrichting op Smartsheet inschakelen.

1. Meld u aan als **SysAdmin** in de **[Smartsheet-portal](https://app.smartsheet.com/b/home)** en navigeer naar **Accountbeheerder**.

    ![Smartsheet-accountbeheerder](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Ga naar **Beveiligingsbesturingselementen > Automatische inrichting van de gebruiker > Bewerken**.

    ![Beveiligingsbesturingselementen voor Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Voeg de e-maildomeinen toe en valideer de e-maildomeinen voor de gebruikers die u van Azure AD naar Smartsheet wilt inrichten. Kies **Niet ingeschakeld** om ervoor te zorgen dat alle inrichtingsacties alleen afkomstig zijn van Azure AD en om er ook voor te zorgen dat uw Smartsheet-gebruikerslijst synchroon loopt met Azure AD-toewijzingen.

    ![Smartsheet-gebruikersinrichting](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Zodra de validatie is voltooid, moet u het domein activeren. 

    ![Smartsheet Activeren Domein](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Genereer het **geheime token dat** nodig is om automatische gebruikersinrichting met Azure AD te configureren door te navigeren naar Apps en **integraties.**

    ![Smartsheet installeren](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Kies **API-toegang**. Klik **op Nieuw toegangstoken genereren**.

    ![Smartsheet installeren](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Definieer de naam van het API Access-token. Klik op **OK**.

    ![Smartsheet installeren](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Kopieer het API Access Token en sla het op, want dit is de enige keer dat u het bekijken. Dit is vereist in het veld **Secret Token** in Azure AD.

    ![Smartsheet-token](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>Smartsheet toevoegen vanuit de galerie

Als u Smartsheet wilt configureren voor automatische gebruikersinrichting met Azure AD, moet u Smartsheet uit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Smartsheet**in , selecteer **Smartsheet** in het deelvenster Resultaten. 

    ![Smartsheet in de resultatenlijst](common/search-new-app.png)

5. Selecteer de knop **Aanmelden voor Smartsheet** waarmee u wordt omgeleid naar de aanmeldingspagina van Smartsheet. 

    ![Smartsheet OIDC toevoegen](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Aangezien Smartsheet een OpenIDConnect-app is, kiest u ervoor om in te loggen op Smartsheet met uw Microsoft-werkaccount.

    ![Smartsheet OIDC login](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. Na een succesvolle verificatie accepteert u de toestemmingsprompt voor de toestemmingspagina. De applicatie wordt dan automatisch toegevoegd aan uw tenant en u wordt doorgestuurd naar uw Smartsheet-account.

    ![Smartsheet OIDc-toestemming](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Automatische gebruikersinrichting configureren in Smartsheet 

In deze sectie u de Azure AD-inrichtingsservice configureren om gebruikers en/of groepen in Smartsheet te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Ga als een automatisch beheer van de gebruiker voor Smartsheet in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Smartsheet**in de lijst met toepassingen .

    ![De koppeling Smartsheet in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie `https://scim.smartsheet.com/v2/` **Beheerdersreferenties** invoer in **Tenant-URL**in . Voer de waarde in die u eerder hebt opgehaald en opgeslagen uit Smartsheet in **Secret Token**. Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Smartsheet. Als de verbinding mislukt, moet u ervoor zorgen dat uw Smartsheet-account sysadmin-machtigingen heeft en het opnieuw proberen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer Azure **Active Directory-gebruikers synchroniseren met Smartsheet**onder de sectie **Toewijzingen** .

    ![Toewijzingen van Smartsheet-gebruikers](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Smartsheet in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Smartsheet te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Kenmerken van Smartsheet-gebruikers](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

11. Als u de Azure AD-inrichtingsservice voor Smartsheet wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten voor Smartsheet door de gewenste waarden in **Bereik** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

13. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitsrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Smartsheet.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Verbindingsbeperkingen

* Smartsheet biedt geen ondersteuning voor soft-deletes. Wanneer het **actieve** kenmerk van een gebruiker is ingesteld op False, verwijdert Smartsheet de gebruiker permanent.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)
