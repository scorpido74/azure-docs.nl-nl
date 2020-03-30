---
title: 'Zelfstudie: Tableau Online configureren voor automatische gebruikersinrichting met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te deprovisioneren voor Tableau Online.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0be9c435-f9a1-484d-8059-e578d5797d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fae770950810899f7c6583fa401110c3e85022b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064209"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Zelfstudie: Tableau Online configureren voor automatische gebruikersinrichting

In deze zelfstudie worden de stappen weergegeven die moeten worden uitgevoerd in Tableau Online en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en groepen automatisch in te richten en te deprovisioneren op Tableau Online.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD-service voor het inrichten van gebruikers. Zie [Gebruikersinrichting en deprovisioning voor software-as-a-service -toepassingen (SaaS) automatiseren met Azure Active Directory](../app-provisioning/user-provisioning.md)voor informatie over wat deze service doet, hoe deze werkt en veelgestelde vragen.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u:

*   Een Azure AD-tenant.
*   Een [Tableau Online tenant.](https://www.tableau.com/)
*   Een gebruikersaccount in Tableau Online met beheerdersmachtigingen.

> [!NOTE]
> De Integratie azure AD-inrichting is gebaseerd op de [Tableau Online Rest API.](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm) Deze API is beschikbaar voor Tableau Online-ontwikkelaars.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Tableau Online toevoegen vanuit de Azure Marketplace
Voordat u Tableau Online configureert voor automatische gebruikersvoorziening met Azure AD, voegt u Tableau Online van de Azure Marketplace toe aan uw lijst met beheerde SaaS-toepassingen.

Voer deze stappen uit om Tableau Online toe te voegen vanaf de Marketplace.

1. Selecteer Azure **Active Directory**in de [Azure-portal](https://portal.azure.com)in het navigatiedeelvenster aan de linkerkant .

    ![Het Azure Active Directory-pictogram](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** bovenaan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer tableau **online** in het zoekvak en selecteer **Tableau Online** in het resultatenpaneel. Als u de toepassing wilt toevoegen, selecteert u **Toevoegen**.

    ![Tableau Online in de resultatenlijst](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Gebruikers toewijzen aan Tableau Online

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van automatische gebruikersinrichting worden alleen de gebruikers of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, bepaalt u welke gebruikers of groepen in Azure AD toegang tot Tableau Online nodig hebben. Als u deze gebruikers of groepen wilt toewijzen aan Tableau Online, volgt u de instructies in [Een gebruiker of groep toewijzen aan een bedrijfsapp.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Belangrijke tips voor het toewijzen van gebruikers aan Tableau Online

*   We raden u aan één Azure AD-gebruiker toe te wijzen aan Tableau Online om de automatische configuratie van gebruikersinrichting te testen. U later extra gebruikers of groepen toewijzen.

*   Wanneer u een gebruiker aan Tableau Online toewijst, selecteert u, indien beschikbaar, een geldige toepassingsspecifieke rol in het dialoogvenster Toewijzing. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Automatische gebruikersinrichting configureren voor Tableau Online

In deze sectie u de stappen doorlopen om de Azure AD-inrichtingsservice te configureren. Gebruik het om gebruikers of groepen in Tableau Online te maken, bij te werken en uit te schakelen op basis van gebruikers- of groepstoewijzingen in Azure AD.

> [!TIP]
> Je saml-gebaseerde enkele aanmelding ook inschakelen voor Tableau Online. Volg de instructies in de [enkele aanmeldingstutorial van Tableau Online.](tableauonline-tutorial.md) Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Tableau Online in Azure AD

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen** > **Alle toepassingen** > Tableau**Online**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Tableau Online**in de lijst met toepassingen .

    ![De Tableau Online link in de lijst met applicaties](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tableau Online Provisioning](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tableau Online Provisioning-modus](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. Voer onder de sectie **Beheerdersreferenties** het domein, de gebruikersnaam van de beheerder, het beheerderswachtwoord en de inhouds-URL van uw Tableau Online-account in:

   * Vul **in het** vak Domein het subdomein in op basis van stap 6.

   * Vul in het vak **Gebruikersnaam beheerder** de gebruikersnaam in van het beheerdersaccount op uw Clarizen-tenant. Een voorbeeld is admin@contoso.com.

   * Vul in het vak **Beheerderswachtwoord** het wachtwoord in van het beheerdersaccount dat overeenkomt met de gebruikersnaam van de beheerder.

   * Vul in het vak **Inhouds-URL** het subdomein in op basis van stap 6.

6. Nadat je je hebt aangemeld bij je beheerdersaccount voor Tableau Online, kun je de waarden voor **domein-** en **inhouds-URL** ophalen via de URL van de beheerderspagina.

    * Het **domein** voor uw Tableau Online-account kan worden gekopieerd uit dit deel van de URL:

        ![Tableau Online Domein](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * De **inhouds-URL** voor je Tableau Online-account kan uit deze sectie worden gekopieerd. Het is een waarde die wordt gedefinieerd tijdens het instellen van een account. In dit voorbeeld is de waarde "contoso":

        ![Url van Tableau Online-inhoud](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Uw **domein** kan afwijken van het domein dat hier wordt weergegeven.

7. Nadat u de vakken in stap 5 hebt ingevuld, selecteert u **Testverbinding** om ervoor te zorgen dat Azure AD verbinding kan maken met Tableau Online. Als de verbinding mislukt, controleert u of je Tableau Online-account beheerdersmachtigingen heeft en probeer het opnieuw.

    ![Tableau Online TestVerbinding](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. Voer in het vak **Berichte-mail** het e-mailadres van de persoon of groep in om de meldingen van provisioning-fouten te ontvangen. Schakel het selectievakje **Een e-mailmelding verzenden wanneer er een fout optreedt** in.

    ![Tableau Online-meldingse-mail](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Selecteer **Opslaan**.

10. Selecteer Azure **Active Directory-gebruikers synchroniseren met Tableau**onder de sectie **Toewijzingen.**

    ![Synchronisatie van gebruikers tableau Online](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Tableau Online in de sectie **Kenmerkentoewijzingen.** De kenmerken die zijn geselecteerd als **Eigenschappen matching** worden gebruikt om de gebruikersaccounts in Tableau Online te matchen voor updatebewerkingen. Als u wijzigingen wilt opslaan, selecteert u **Opslaan**.

    ![Tableau Online matching user attributen](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. Selecteer Azure **Active Directory-groepen synchroniseren met Tableau**onder de sectie **Toewijzingen** .

    ![Synchronisatie van Tableau Online-groepen](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Bekijk de groepskenmerken die zijn gesynchroniseerd van Azure AD naar Tableau Online in de sectie **Kenmerkentoewijzingen.** De kenmerken die zijn geselecteerd als **Eigenschappen matching** worden gebruikt om de gebruikersaccounts in Tableau Online te matchen voor updatebewerkingen. Als u wijzigingen wilt opslaan, selecteert u **Opslaan**.

    ![Tableau Online-overeenkomende groepskenmerken](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Als u scopingfilters wilt configureren, volgt u de instructies in de zelfstudie van het [scopingfilter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

15. Als u de Azure AD-inrichtingsservice voor Tableau Online wilt inschakelen, wijzigt u in **de** sectie Instellingen **de inrichtingsstatus** in **Aan**.

    ![Tableau Online Provisioning Status](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Definieer de gebruikers of groepen die u wilt inrichten op Tableau Online. Selecteer **in** de sectie Instellingen de gewenste waarden in **Bereik**.

    ![Tableau Online-bereik](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Wanneer u klaar bent voor inlevering, selecteert u **Opslaan**.

    ![Tableau Online opslaan](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan later synchroniseert. Ze vinden ongeveer elke 40 minuten plaats zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het rapportagerapport te volgen. Het rapport beschrijft alle acties die worden uitgevoerd door de Azure AD-inrichtingsservice op Tableau Online.

Zie [Rapportage over automatische gebruikersaccountinrichting](../app-provisioning/check-status-user-account-provisioning.md)voor informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountinrichting voor bedrijfsapps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
