---
title: 'Zelfstudie: Cornerstone OnDemand configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te deprovisioneneren naar Cornerstone OnDemand.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6212e74ecbf8327d3939138de2e92868f29b0f1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058420"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Zelfstudie: Cornerstone OnDemand configureren voor automatische gebruikersinrichting

In deze zelfstudie worden de stappen weergegeven die moeten worden uitgevoerd in Cornerstone OnDemand en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers of groepen automatisch in te richten en te deprovisioneneren naar Cornerstone OnDemand.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD-service voor het inrichten van gebruikers. Zie [Gebruikersinrichting en deprovisioning voor software-as-a-service -toepassingen (SaaS) automatiseren met Azure Active Directory](../app-provisioning/user-provisioning.md)voor informatie over wat deze service doet, hoe deze werkt en veelgestelde vragen.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u:

* Een Azure AD-tenant.
* Een Cornerstone OnDemand huurder.
* Een gebruikersaccount in Cornerstone OnDemand met beheerdersmachtigingen.

> [!NOTE]
> De azure AD-integratie is gebaseerd op de [Cornerstone OnDemand-webservice](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf). Deze service is beschikbaar voor Cornerstone OnDemand-teams.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Cornerstone OnDemand toevoegen vanuit de Azure Marketplace

Voeg Cornerstone OnDemand toe aan uw lijst met beheerde SaaS-toepassingen voordat u Cornerstone OnDemand configureert voor automatische gebruikersvoorziening met Azure AD.

Voer deze stappen uit om Cornerstone OnDemand toe te voegen vanaf de Marketplace.

1. Selecteer Azure **Active Directory**in de [Azure-portal](https://portal.azure.com)in het navigatiedeelvenster aan de linkerkant .

    ![Het Azure Active Directory-pictogram](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** bovenaan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Hoeksteen OnDemand** in het zoekvak en selecteer **Cornerstone OnDemand** in het resultaatpaneel. Als u de toepassing wilt toevoegen, selecteert u **Toevoegen**.

    ![Cornerstone OnDemand in de resultatenlijst](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Gebruikers toewijzen aan Cornerstone OnDemand

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van automatische gebruikersinrichting worden alleen de gebruikers of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, bepaalt u welke gebruikers of groepen in Azure AD toegang tot Cornerstone OnDemand nodig hebben. Als u deze gebruikers of groepen wilt toewijzen aan Cornerstone OnDemand, volgt u de instructies in [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Belangrijke tips voor het toewijzen van gebruikers aan Cornerstone OnDemand

* We raden u aan één Azure AD-gebruiker toe te wijzen aan Cornerstone OnDemand om de automatische configuratie van gebruikersinrichting te testen. U later extra gebruikers of groepen toewijzen.

* Wanneer u een gebruiker toewijst aan Cornerstone OnDemand, selecteert u, indien beschikbaar, een geldige toepassingsspecifieke rol in het dialoogvenster Toewijzing. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Automatische gebruikersinrichting configureren op Cornerstone OnDemand

In deze sectie u de stappen doorlopen om de Azure AD-inrichtingsservice te configureren. Gebruik het om gebruikers of groepen te maken, bij te werken en uit te schakelen in Cornerstone OnDemand op basis van gebruikers- of groepstoewijzingen in Azure AD.

Voer de volgende stappen uit om automatische gebruikersinrichting voor Cornerstone OnDemand in Azure AD te configureren.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen** > **Alle toepassingen** > Cornerstone**OnDemand**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Cornerstone OnDemand**in de lijst met toepassingen .

    ![De Hoeksteen OnDemand-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Cornerstone OnDemand Provisioning](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. **Instelmodus instellen** op **Automatisch**.

    ![Cornerstone OnDemand Provisioning Mode](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Voer onder de sectie **Beheerdersreferenties** de gebruikersnaam, het beheerderswachtwoord en het domein van het account van uw Hoeksteen OnDemand in:

    * Vul in het vak **Gebruikersnaam beheerder** het domein of de gebruikersnaam van het beheerdersaccount in op uw Cornerstone OnDemand-tenant. Een voorbeeld is contoso\admin.

    * Vul in het vak **Beheerderswachtwoord** het wachtwoord in dat overeenkomt met de gebruikersnaam van de beheerder.

    * Vul in het vak **Domein** de URL van de webservice van de tenant Cornerstone OnDemand in. De service bevindt zich `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`bijvoorbeeld op , en `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`voor Contoso is het domein . Zie [deze pdf](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf)voor meer informatie over het ophalen van de URL van de webservice.

6. Nadat u de vakken in stap 5 hebt ingevuld, selecteert u **Testverbinding** om ervoor te zorgen dat Azure AD verbinding kan maken met Cornerstone OnDemand. Als de verbinding mislukt, moet u ervoor zorgen dat uw Cornerstone OnDemand-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Hoeksteen OnDemand-testverbinding](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. Voer in het vak **Berichte-mail** het e-mailadres van de persoon of groep in om de meldingen van provisioning-fouten te ontvangen. Schakel het selectievakje **Een e-mailmelding verzenden wanneer er een fout optreedt** in.

    ![Cornerstone OnDemand-meldingse-mail](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Selecteer **Opslaan**.

9. Selecteer Azure **Active Directory-gebruikers synchroniseren met Cornerstone OnDemand**onder de sectie **Toewijzingen** .

    ![Cornerstone OnDemand-synchronisatie](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Cornerstone OnDemand in de sectie **Kenmerktoewijzingen.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Cornerstone OnDemand te matchen voor updatebewerkingen. Als u wijzigingen wilt opslaan, selecteert u **Opslaan**.

    ![Hoeksteen OnDemand-kenmerkentoewijzingen](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Als u scopingfilters wilt configureren, volgt u de instructies in de zelfstudie van het [scopingfilter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

12. Als u de Azure AD-inrichtingsservice voor Cornerstone OnDemand wilt inschakelen, wijzigt u in **de** sectie Instellingen **de inrichtingsstatus** in **Aan**.

    ![Cornerstone OnDemand Provisioning Status](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Definieer de gebruikers of groepen die u wilt inrichten op Cornerstone OnDemand. Selecteer **in** de sectie Instellingen de gewenste waarden in **Bereik**.

    ![Cornerstone OnDemand-scope](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Wanneer u klaar bent voor inlevering, selecteert u **Opslaan**.

    ![Hoeksteen OnDemand Opslaan](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan later synchroniseert. Ze vinden ongeveer elke 40 minuten plaats zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het rapportagerapport te volgen. Het rapport beschrijft alle acties die worden uitgevoerd door de Azure AD-inrichtingsservice op Cornerstone OnDemand.

Zie [Rapportage over automatische gebruikersaccountinrichting](../app-provisioning/check-status-user-account-provisioning.md)voor informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Verbindingsbeperkingen

Het kenmerk Cornerstone OnDemand **Position** verwacht een waarde die overeenkomt met de rollen op de Cornerstone OnDemand-portal. Als u een lijst met geldige **positiewaarden wilt** krijgen, gaat u naar **Gebruikersrecord bewerken > organisatiestructuur > positie** in de Cornerstone OnDemand-portal.

![Cornerstone OnDemand Provisioning Gebruikersrecord bewerken](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Hoeksteen OnDemand Provisioning Positie](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Cornerstone OnDemand Provisioning position list](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountinrichting voor bedrijfsapps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
