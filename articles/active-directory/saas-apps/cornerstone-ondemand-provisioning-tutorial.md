---
title: 'Zelf studie: hoek steen configureren voor automatische gebruikers inrichten met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van gebruikers accounts op de hoek van de OnDemand.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 4200bc9879aba5e18282123be03576cc617a5e14
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88549225"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Zelf studie: hoek steen configureren voor automatische gebruikers inrichting

In deze zelf studie ziet u de stappen die u moet uitvoeren op de OnDemand-en Azure Active Directory (Azure AD) voor het configureren van Azure AD voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers of groepen op hoek steen.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie voor meer informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen [gebruikers automatisch inrichten en ongedaan maken van de inrichting van SaaS-toepassingen (Software-as-a-Service) met Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u het volgende hebt:

* Een Azure AD-tenant.
* Een hoek steen van de OnDemand-Tenant.
* Een gebruikers account in hoek steen en de OnDemand met beheerders machtigingen.

> [!NOTE]
> De integratie van Azure AD-inrichting is afhankelijk van de [OnDemand-webservice](https://www.cornerstoneondemand.com/)van de hoek. Deze service is beschikbaar voor de OnDemand-teams van de hoek.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Hoek steen toevoegen op de Azure Marketplace

Voordat u de hoek steen voor het automatisch inrichten van de gebruiker met Azure AD configureert, voegt u de OnDemand-opdracht van de Marketplace toe aan uw lijst met beheerde SaaS-toepassingen.

Ga als volgt te werk om de OnDemand-procedure van de Marketplace toe te voegen.

1. In de [Azure Portal](https://portal.azure.com), in het navigatie deel venster aan de linkerkant, selecteert u **Azure Active Directory**.

    ![Het Azure Active Directory pictogram](common/select-azuread.png)

2. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** bovenaan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. In het zoekvak voert u de **OnDemand** in en selecteert u op de begin **hoek** van het deel venster met het resultaat. Selecteer **toevoegen**om de toepassing toe te voegen.

    ![Cornerstone OnDemand in de resultatenlijst](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Gebruikers toewijzen aan hoek steen van de OnDemand

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u het automatisch inrichten van gebruikers configureert en inschakelt, moet u beslissen welke gebruikers of groepen in azure AD toegang nodig hebben tot de hoek van de OnDemand. Volg de instructies in [een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)om deze gebruikers of groepen toe te wijzen aan de OnDemand.

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Belang rijke tips voor het toewijzen van gebruikers aan de hoek van de OnDemand

* We raden u aan één Azure AD-gebruiker toe te wijzen op hoek steen van de OnDemand om de configuratie van automatische gebruikers inrichting te testen. U kunt later aanvullende gebruikers of groepen toewijzen.

* Wanneer u een gebruiker toewijst op hoek steen, selecteert u een geldige toepassingsspecifieke rol, indien beschikbaar, in het dialoog venster toewijzing. Gebruikers met de rol **Standaardtoegang** worden uitgesloten van het inrichten.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Automatische gebruikers inrichten op hoek steen van de OnDemand configureren

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service. Gebruik dit om gebruikers of groepen te maken, bij te werken en uit te scha kelen in hoek steen op basis van gebruikers-of groeps toewijzingen in azure AD.

Ga als volgt te werk om het automatisch inrichten van gebruikers te configureren voor de OnDemand van een hoek steen in azure AD.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). **Bedrijfs toepassingen**selecteren  >  **alle toepassingen op de**  >  **OnDemand**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Cornerstone OnDemand** in de lijst met toepassingen.

    ![De koppeling van de hoek steen in de lijst toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Hoek steen van de OnDemand-inrichting](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Hoek steen van de OnDemand-inrichtings modus](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Onder de sectie **beheerders referenties** voert u de gebruikers naam van de beheerder, het beheerders wachtwoord en het domein van uw berekenings account van hoek steen.

    * Vul in het vak **Administrator-gebruikers naam** het domein of de gebruikers naam van het beheerders account in op de Tenant met de hoek steen. Een voor beeld is contoso\admin.

    * In het vak **beheerders wachtwoord** vult u het wacht woord in dat overeenkomt met de gebruikers naam van de beheerder.

    * Vul in het vak **domein** de WEBSERVICE-URL in van de hoek van het bewaarde Tenant. De service bevindt zich bijvoorbeeld op `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx` en voor contoso is het domein `https://ws-contoso.csod.com/feed30/clientdataservice.asmx` . Zie voor meer informatie over het ophalen van de webservice [-URL deze PDF](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. Nadat u de vakken in stap 5 hebt ingevuld, selecteert u **verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met de hoek steen van de OnDemand. Als de verbinding mislukt, moet u ervoor zorgen dat uw OnDemand-account over beheerders rechten beschikt en het opnieuw proberen.

    ![Hoek steen van OnDemand test verbinding](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. Voer in het vak **e-mail bericht** het e-mail adres van de persoon of groep in om de inrichtings fout meldingen te ontvangen. Schakel het selectie vakje **e-mail melding verzenden wanneer een fout optreedt** in.

    ![E-mail melding van hoek steen](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Selecteer **Opslaan**.

9. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren op hoek steen**.

    ![Hoek steen van de OnDemand-synchronisatie](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar de hoek van de gebruiker in de sectie **kenmerk toewijzingen** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in hoek steen van de OnDemand voor bijwerk bewerkingen. Selecteer **Opslaan**om de wijzigingen op te slaan.

    ![Hoek steen van OnDemand kenmerk toewijzingen](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Als u bereik filters wilt configureren, volgt u de instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik.

12. Als u de Azure AD-inrichtings service voor hoek steen op de OnDemand wilt inschakelen, wijzigt u de **inrichtings status** in het gedeelte **instellingen** in **op aan**.

    ![De inrichtings status van hoek steen en OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Definieer de gebruikers of groepen die u wilt inrichten op de hoek van de OnDemand. Selecteer in de sectie **instellingen** de waarden die u in het **bereik**wilt.

    ![Hoek steen van de OnDemand-Scope](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. Wanneer u klaar bent om in te richten, selecteert u **Opslaan**.

    ![Hoek steen van de OnDemand bespaard](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan bij latere synchronisaties. Ze treden ongeveer elke 40 minuten in beslag, zolang de Azure AD-inrichtings service wordt uitgevoerd. 

U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen. In het rapport worden alle acties beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op de hoek steen van de OnDemand.

Zie [Rapportage over automatische toewijzing van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Connectorbeperkingen

Het kenmerk voor de OnDemand- **positie** van de hoek steen verwacht een waarde die overeenkomt met de functies op de portal van de hoek steen. Als u een lijst met geldige **positie** waarden wilt ophalen, gaat u naar **gebruikers record bewerken > organisatie structuur > positie** in de portal van de hoek steen.

![Hoek steen, OnDemand, gebruikers record bewerken](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Hoek van de OnDemand-inrichtings positie](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![De lijst met ondemande inrichtings posities](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Aanvullende bronnen

* [Het inrichten van gebruikers accounts beheren voor zakelijke apps](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
