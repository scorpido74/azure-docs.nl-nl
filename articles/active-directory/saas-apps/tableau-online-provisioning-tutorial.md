---
title: 'Zelf studie: tableau online configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op tableau online.
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
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064209"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Zelf studie: tableau online configureren voor het automatisch inrichten van gebruikers

In deze zelf studie ziet u de stappen voor het uitvoeren van tableau online en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en groepen in tableau.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie voor meer informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen [gebruikers automatisch inrichten en ongedaan maken van de inrichting van SaaS-toepassingen (Software-as-a-Service) met Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u het volgende hebt:

*   Een Azure AD-tenant.
*   Een [tableau online-Tenant](https://www.tableau.com/).
*   Een gebruikers account in tableau online met beheerders machtigingen.

> [!NOTE]
> De integratie van Azure AD-inrichting is afhankelijk van de [tableau online rest API](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm). Deze API is beschikbaar voor tableau online-ontwikkel aars.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Tableau online toevoegen vanuit Azure Marketplace
Voordat u tableau online configureert voor het automatisch inrichten van gebruikers met Azure AD, voegt u tableau online toe vanuit Azure Marketplace aan uw lijst met beheerde SaaS-toepassingen.

Voer de volgende stappen uit om tableau online toe te voegen vanuit de Marketplace.

1. In de [Azure Portal](https://portal.azure.com), in het navigatie deel venster aan de linkerkant, selecteert u **Azure Active Directory**.

    ![Het Azure Active Directory pictogram](common/select-azuread.png)

2. Ga naar **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** bovenaan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer **tableau online** in in het zoekvak en selecteer **tableau online** in het deel venster voor resultaten. Selecteer **toevoegen**om de toepassing toe te voegen.

    ![Tableau online in de lijst met resultaten](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Gebruikers toewijzen aan tableau online

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u bepalen welke gebruikers of groepen in azure AD toegang moeten hebben tot tableau online. Volg de instructies in [een gebruiker of groep toewijzen aan een bedrijfs-app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)om deze gebruikers of groepen online toe te wijzen aan tableau.

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Belang rijke tips voor het toewijzen van gebruikers aan tableau online

*   We raden u aan één Azure AD-gebruiker toe te wijzen aan tableau online om de configuratie voor automatische gebruikers inrichting te testen. U kunt later aanvullende gebruikers of groepen toewijzen.

*   Wanneer u een gebruiker toewijst aan tableau online, selecteert u een geldige toepassingsspecifieke rol, indien beschikbaar, in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Automatische gebruikers inrichting configureren voor tableau online

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service. Gebruik dit om gebruikers of groepen in tableau online te maken, bij te werken en uit te scha kelen op basis van gebruikers-of groeps toewijzingen in azure AD.

> [!TIP]
> U kunt ook op SAML gebaseerde eenmalige aanmelding inschakelen voor tableau online. Volg de instructies in de [tableau online zelf studie voor eenmalige aanmelding](tableauonline-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Automatische gebruikers inrichting voor tableau online configureren in azure AD

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen** > **alle toepassingen** > **tableau online**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **tableau online**in de lijst toepassingen.

    ![De online koppeling tableau in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tableau online inrichten](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tableau online inrichtings modus](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. Geef in de sectie **beheerders referenties** het domein, de gebruikers naam van de beheerder, het beheerders wachtwoord en de INHOUDS-URL van uw tableau online-account op:

   * Vul in het vak **domein** het subdomein in op basis van stap 6.

   * Vul in het vak **Administrator-gebruikers naam** de gebruikers naam in van het beheerders account op uw Clarizen-Tenant. Een voorbeeld is admin@contoso.com.

   * In het vak **beheerders wachtwoord** vult u het wacht woord in van het beheerders account dat overeenkomt met de gebruikers naam van de beheerder.

   * Vul in het vak **URL van inhoud** het subdomein in op basis van stap 6.

6. Nadat u zich hebt aangemeld bij uw beheerders account voor tableau online, kunt u de waarden voor de **domein** -en **inhouds-URL** ophalen uit de URL van de beheer pagina.

    * Het **domein** voor uw tableau online-account kan vanuit dit deel van de URL worden gekopieerd:

        ![Tableau online domein](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * De **inhouds-URL** voor uw online tableau-account kan vanuit deze sectie worden gekopieerd. Het is een waarde die tijdens het instellen van het account is gedefinieerd. In dit voor beeld is de waarde ' Contoso ':

        ![URL van tableau online inhoud](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Uw **domein** kan afwijken van de naam die hier wordt weer gegeven.

7. Nadat u de vakken in stap 5 hebt ingevuld, selecteert u **verbinding testen** om te controleren of Azure AD verbinding kan maken met tableau online. Als de verbinding mislukt, controleert u of uw tableau online-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Tableau online-test verbinding](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. Voer in het vak **e-mail bericht** het e-mail adres van de persoon of groep in om de inrichtings fout meldingen te ontvangen. Schakel het selectie vakje **e-mail melding verzenden wanneer een fout optreedt** in.

    ![E-mail bericht tableau online](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Selecteer **Opslaan**.

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met tableau**.

    ![Tableau online-gebruikers synchronisatie](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Controleer de gebruikers kenmerken die vanuit Azure AD zijn gesynchroniseerd met tableau online in het gedeelte **kenmerk toewijzingen** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om de gebruikers accounts in tableau online te vergelijken voor bijwerk bewerkingen. Selecteer **Opslaan**om de wijzigingen op te slaan.

    ![Tableau online overeenkomende gebruikers kenmerken](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren met tableau**.

    ![Synchronisatie van online groep tableau](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Controleer de groeps kenmerken die vanuit Azure AD worden gesynchroniseerd met tableau online in het gedeelte **kenmerk toewijzingen** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om de gebruikers accounts in tableau online te vergelijken voor bijwerk bewerkingen. Selecteer **Opslaan**om de wijzigingen op te slaan.

    ![Tableau online overeenkomende groeps kenmerken](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Als u bereik filters wilt configureren, volgt u de instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik.

15. Als u de Azure AD-inrichtings service voor tableau online wilt inschakelen, wijzigt u in de sectie **instellingen** de **inrichtings status** in **op aan**.

    ![Tableau online-inrichtings status](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Definieer de gebruikers of groepen die u online wilt inrichten tableau. Selecteer in de sectie **instellingen** de waarden die u in het **bereik**wilt.

    ![Tableau online-bereik](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Wanneer u klaar bent om in te richten, selecteert u **Opslaan**.

    ![Tableau online opslaan](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan bij latere synchronisaties. Ze treden ongeveer elke 40 minuten in beslag, zolang de Azure AD-inrichtings service wordt uitgevoerd. 

U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen. In het rapport worden alle acties beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op tableau online.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Het inrichten van gebruikers accounts beheren voor zakelijke apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
