---
title: 'Zelfstudie: Samanage configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te deprovisioneneren in Samanage.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 988efc2087b3b30e6073bd7f6e2cf08f91fd397c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060463"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Zelfstudie: Samanage configureren voor automatische gebruikersinrichting

In deze zelfstudie worden de stappen weergegeven die moeten worden uitgevoerd in Samanage en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en groepen automatisch in te richten en te deprovisioneneren aan Samanage.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD-service voor het inrichten van gebruikers. Zie [Gebruikersinrichting en deprovisioning voor software-as-a-service -toepassingen (SaaS) automatiseren met Azure Active Directory](../app-provisioning/user-provisioning.md)voor informatie over wat deze service doet, hoe deze werkt en veelgestelde vragen.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u:

* Een Azure AD-tenant.
* Een [Samanage huurder](https://www.samanage.com/pricing/) met het Professional pakket.
* Een gebruikersaccount in Samanage met beheerdersmachtigingen.

> [!NOTE]
> De Azure AD-integratie is gebaseerd op de [Samanage Rest API.](https://www.samanage.com/api/) Deze API is beschikbaar voor Samanage-ontwikkelaars voor accounts met het Professional-pakket.

## <a name="add-samanage-from-the-azure-marketplace"></a>Samanage toevoegen vanuit de Azure Marketplace

Voordat u Samanage configureert voor automatische gebruikersvoorziening met Azure AD, voegt u Samanage van de Azure Marketplace toe aan uw lijst met beheerde SaaS-toepassingen.

Voer de volgende stappen uit om Samanage toe te voegen vanaf de Marketplace.

1. Selecteer Azure **Active Directory**in de [Azure-portal](https://portal.azure.com)in het navigatiedeelvenster aan de linkerkant .

    ![Het Azure Active Directory-pictogram](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** bovenaan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Samanage** in en selecteer **Samanage** in het resultaatpaneel. Als u de toepassing wilt toevoegen, selecteert u **Toevoegen**.

    ![Samanage in de resultatenlijst](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Gebruikers toewijzen aan Samanage

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van automatische gebruikersinrichting worden alleen de gebruikers of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, bepaalt u welke gebruikers of groepen in Azure AD toegang tot Samanage nodig hebben. Als u deze gebruikers of groepen wilt toewijzen aan Samanage, volgt u de instructies in [Een gebruiker of groep toewijzen aan een bedrijfsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-samanage"></a>Belangrijke tips voor het toewijzen van gebruikers aan Samanage

*    Tegenwoordig worden Samanage-rollen automatisch en dynamisch ingevuld in de gebruikersinterface van de Azure-portal. Voordat u Samanage-rollen aan gebruikers toewijst, moet u ervoor zorgen dat een eerste synchronisatie is voltooid ten opzichte van Samanage om de nieuwste rollen in uw Samanage-tenant op te halen.

*    We raden u aan één Azure AD-gebruiker aan Samanage toe te wijzen om uw initiële automatische configuratie van gebruikersinrichting te testen. U later extra gebruikers en groepen toewijzen nadat de tests zijn geslaagd.

*    Wanneer u een gebruiker aan Samanage toewijst, selecteert u, indien beschikbaar, een geldige toepassingsspecifieke rol in het dialoogvenster Toewijzing. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Automatische gebruikersinrichting configureren voor Samanage

In deze sectie u de stappen doorlopen om de Azure AD-inrichtingsservice te configureren. Gebruik het om gebruikers of groepen in Samanage te maken, bij te werken en uit te schakelen op basis van gebruikers- of groepstoewijzingen in Azure AD.

> [!TIP]
> U saml-gebaseerde enkele aanmelding ook inschakelen voor Samanage. Volg de instructies in de [zelfstudie voor eenmalige aanmelding samanage.](samanage-tutorial.md) Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Automatische gebruikersinrichting voor Samanage configureren in Azure AD

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise Applications** > **All applications** > **Samanage**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Samanage** in de lijst met toepassingen.

    ![De Koppeling Samanage in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Samanage Provisioning](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie **Beheerdersreferenties** de **URL** van uw Samanage Tenant en **geheim token in.** Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Samanage. Als de verbinding mislukt, moet u ervoor zorgen dat uw Samanage-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Samanage-testverbinding](./media/samanage-provisioning-tutorial/provisioning.png)

6. Voer in het vak **Berichte-mail** het e-mailadres van de persoon of groep in om de meldingen van provisioning-fouten te ontvangen. Schakel het selectievakje **Een e-mailmelding verzenden wanneer er een fout optreedt** in.

    ![E-mail met meldingen van Samanage](./media/samanage-provisioning-tutorial/EmailNotification.png)

7. Selecteer **Opslaan**.

8. Selecteer Azure **Active Directory-gebruikers synchroniseren met Samanage**onder de sectie **Toewijzingen** .

    ![Synchronisatie van gebruikers samanage](./media/samanage-provisioning-tutorial/UserMappings.png)

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Samanage in de sectie **Kenmerktoewijzingen.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Samanage te matchen voor updatebewerkingen. Als u wijzigingen wilt opslaan, selecteert u **Opslaan**.

    ![Overeenkomende gebruikerskenmerken van Samanage](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

10. Als u groepstoewijzingen wilt inschakelen, selecteert u onder de sectie **Toewijzingen** de optie **Azure Active Directory Groups synchroniseren met Samanage**.

    ![Synchronisatie van samanage-groepen](./media/samanage-provisioning-tutorial/GroupMappings.png)

11. **Ingeschakeld instellen** op **Ja** om groepen te synchroniseren. Controleer de groepskenmerken die zijn gesynchroniseerd van Azure AD naar Samanage in de sectie **Kenmerktoewijzingen.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Samanage te matchen voor updatebewerkingen. Als u wijzigingen wilt opslaan, selecteert u **Opslaan**.

    ![Overeenkomende groepskenmerken van Samanage](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Als u scopingfilters wilt configureren, volgt u de instructies in de zelfstudie van het [scopingfilter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

13. Als u de Azure AD-inrichtingsservice voor Samanage wilt inschakelen, wijzigt u in de sectie **Instellingen** **de inrichtingsstatus** in **Aan**.

    ![Status van voorziening samanage](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

14. Definieer de gebruikers of groepen die u wilt inrichten in Samanage. Selecteer **in** de sectie Instellingen de gewenste waarden in **Bereik**. Wanneer u de optie **Alle gebruikers en groepen synchroniseren** selecteert, moet u rekening houden met de beperkingen zoals beschreven in de volgende sectie 'Verbindingsbeperkingen'.

    ![Samanage-bereik](./media/samanage-provisioning-tutorial/ScopeSync.png)

15. Wanneer u klaar bent voor inlevering, selecteert u **Opslaan**.

    ![Opslaan bij Samanage](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan later synchroniseert. Ze vinden ongeveer elke 40 minuten plaats zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het rapportagerapport te volgen. Het rapport beschrijft alle acties die worden uitgevoerd door de Azure AD-inrichtingsservice op Samanage.

Zie [Rapportage over automatische gebruikersaccountinrichting](../app-provisioning/check-status-user-account-provisioning.md)voor informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Verbindingsbeperkingen

Als u de optie **Alle gebruikers en groepen synchroniseren** selecteert en een waarde configureert voor het kenmerk Samanage-rollen, moet de waarde onder het vak **Standaardwaarde als null (is optioneel)** worden uitgedrukt in de volgende indeling: **roles**

- {"displayName":"rol"}, waarbij de rol de standaardwaarde is die u wilt.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountinrichting voor bedrijfsapps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
