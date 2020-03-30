---
title: 'Zelfstudie: Zendesk configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te deprovisioneneren naar Zendesk.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 01d5e4d5-d856-42c4-a504-96fa554baf66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a480119ee88521b920be88669f6d80e3754d24d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062739"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Zelfstudie: Zendesk configureren voor automatische gebruikersinrichting

In deze zelfstudie worden de stappen weergegeven die moeten worden uitgevoerd in Zendesk en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en groepen automatisch in te richten en te deprovisioneren naar Zendesk.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD-service voor het inrichten van gebruikers. Zie [Gebruikersinrichting en deprovisioning voor software-as-a-service -toepassingen (SaaS) automatiseren met Azure Active Directory](../app-provisioning/user-provisioning.md)voor informatie over wat deze service doet, hoe deze werkt en veelgestelde vragen.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u:

* Een Azure AD-tenant.
* Een Zendesk huurder met het Professional plan of beter ingeschakeld.
* Een gebruikersaccount in Zendesk met beheerdersmachtigingen.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Zendesk toevoegen vanuit de Azure Marketplace

Voordat u Zendesk configureert voor automatische gebruikersvoorziening met Azure AD, voegt u Zendesk van de Azure Marketplace toe aan uw lijst met beheerde SaaS-toepassingen.

Voer de volgende stappen uit om Zendesk toe te voegen vanaf de Marketplace.

1. Selecteer Azure **Active Directory**in de [Azure-portal](https://portal.azure.com)in het navigatiedeelvenster aan de linkerkant .

    ![Het Azure Active Directory-pictogram](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** bovenaan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Zendesk** in en selecteer **Zendesk** in het resultaatpaneel. Als u de toepassing wilt toevoegen, selecteert u **Toevoegen**.

    ![Zendesk in de resultatenlijst](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Gebruikers toewijzen aan Zendesk

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van automatische gebruikersinrichting worden alleen de gebruikers of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, bepaalt u welke gebruikers of groepen in Azure AD toegang tot Zendesk nodig hebben. Als u deze gebruikers of groepen aan Zendesk wilt toewijzen, volgt u de instructies in [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Belangrijke tips voor het toewijzen van gebruikers aan Zendesk

* Tegenwoordig worden Zendesk-rollen automatisch en dynamisch ingevuld in de gebruikersinterface van de Azure-portal. Voordat u Zendesk-rollen aan gebruikers toewijst, moet u ervoor zorgen dat een eerste synchronisatie met Zendesk is voltooid om de nieuwste rollen in uw Zendesk-tenant op te halen.

* We raden u aan één Azure AD-gebruiker aan Zendesk toe te wijzen om uw eerste automatische configuratie van gebruikersinrichting te testen. U later extra gebruikers of groepen toewijzen nadat de tests zijn geslaagd.

* Wanneer u een gebruiker aan Zendesk toewijst, selecteert u, indien beschikbaar, een geldige toepassingsspecifieke rol in het dialoogvenster Toewijzing. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Automatische gebruikersinrichting configureren naar Zendesk 

In deze sectie u de stappen doorlopen om de Azure AD-inrichtingsservice te configureren. Gebruik het om gebruikers of groepen in Zendesk te maken, bij te werken en uit te schakelen op basis van gebruikers- of groepstoewijzingen in Azure AD.

> [!TIP]
> U saml-gebaseerde enkele aanmelding ook inschakelen voor Zendesk. Volg de instructies in de [zelfstudie voor eenmalige aanmelding van Zendesk.](zendesk-tutorial.md) Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Automatische gebruikersinrichting configureren voor Zendesk in Azure AD

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen** > **Alle toepassingen** > **Zendesk**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Zendesk**in de lijst met toepassingen .

    ![De Zendesk-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Zendesk-provisioning](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Zendesk-inrichtingsmodus](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. Voer onder de sectie **Beheerdersreferenties** de gebruikersnaam van de beheerder, het geheime token en het domein van uw Zendesk-account in. Voorbeelden van deze waarden zijn:

   * Vul in het vak **Gebruikersnaam beheerder** de gebruikersnaam in van het beheerdersaccount op uw Zendesk-tenant. Een voorbeeld is admin@contoso.com.

   * Vul in het vak **Secret Token** het geheime token in zoals beschreven in stap 6.

   * Vul in het vak **Domein** het subdomein van uw Zendesk-tenant in. Bijvoorbeeld, voor een account met `https://my-tenant.zendesk.com`een tenant URL van , uw subdomein is **mijn-tenant**.

6. Het geheime token voor uw Zendesk-account bevindt zich in > **BeheerdersAPI-instellingen.** > **Settings** **Admin** Controleer of **Tokenaccess** is ingesteld op **Ingeschakeld**.

    ![Zendesk-beheerinstellingen](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Geheim Zendesk-token](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Nadat u de vakken in stap 5 hebt ingevuld, selecteert u **Testverbinding** om ervoor te zorgen dat Azure AD verbinding kan maken met Zendesk. Als de verbinding mislukt, controleert u of uw Zendesk-account beheerdersmachtigingen heeft en probeert u het opnieuw.

    ![Zendesk-testverbinding](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. Voer in het vak **Berichte-mail** het e-mailadres van de persoon of groep in om de meldingen van provisioning-fouten te ontvangen. Schakel het selectievakje **Een e-mailmelding verzenden wanneer er een fout optreedt** in.

    ![E-mail met meldingen voor Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Selecteer **Opslaan**.

10. Selecteer Azure **Active Directory-gebruikers synchroniseren met Zendesk**onder de sectie **Toewijzingen** .

    ![Synchronisatie van Zendesk-gebruikers](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Zendesk in de sectie **Toewijzingen van kenmerken.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Zendesk te matchen voor updatebewerkingen. Als u wijzigingen wilt opslaan, selecteert u **Opslaan**.

    ![Zendesk-overeenkomende gebruikerskenmerken](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. Selecteer Azure **Active Directory-groepen synchroniseren met Zendesk**onder de sectie **Toewijzingen** .

    ![Synchronisatie van Zendesk-groepen](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Controleer de groepskenmerken die zijn gesynchroniseerd van Azure AD naar Zendesk in de sectie **Toewijzingen van kenmerken.** De kenmerken die zijn geselecteerd als **Eigenschappen matching** worden gebruikt om de groepen in Zendesk te matchen voor updatebewerkingen. Als u wijzigingen wilt opslaan, selecteert u **Opslaan**.

    ![Zendesk-overeenkomende groepskenmerken](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Als u scopingfilters wilt configureren, volgt u de instructies in de zelfstudie van het [scopingfilter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

15. Als u de Azure AD-inrichtingsservice voor Zendesk wilt inschakelen, wijzigt u in de sectie **Instellingen** **de inrichtingsstatus** in **Aan**.

    ![Zendesk-inrichtingsstatus](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Definieer de gebruikers of groepen die u wilt inrichten in Zendesk. Selecteer **in** de sectie Instellingen de gewenste waarden in **Bereik**.

    ![Zendesk-bereik](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Wanneer u klaar bent voor inlevering, selecteert u **Opslaan**.

    ![Zendesk opslaan](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan later synchroniseert. Ze vinden ongeveer elke 40 minuten plaats zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het rapportagerapport te volgen. Het rapport beschrijft alle acties die worden uitgevoerd door de Azure AD-inrichtingsservice op Zendesk.

Zie [Rapportage over automatische gebruikersaccountinrichting](../app-provisioning/check-status-user-account-provisioning.md)voor informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Verbindingsbeperkingen

* Zendesk ondersteunt het gebruik van groepen voor gebruikers met alleen **Agent-rollen.** Zie voor meer informatie de [Zendesk-documentatie.](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups)

* Wanneer een aangepaste rol is toegewezen aan een gebruiker of groep, wijst de automatische gebruikersinrichtingsservice van Azure AD ook de **standaardrolagent**toe. Alleen agents kunnen een aangepaste rol toegewezen krijgen. Zie de [Zendesk API-documentatie](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests)voor meer informatie. 

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountinrichting voor bedrijfsapps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
