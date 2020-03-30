---
title: 'Zelfstudie: Bonus configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Bonusly.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 879b0ee9-042a-441b-90a7-8c364d62426a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7694e441a59680a9b9544d3479100c1f779964ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058872"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>Zelfstudie: Bonus configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Extraly en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen voor Bonusly.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al het volgende hebt:

* Een Azure AD-tenant
* Een [bonushuurder](https://bonus.ly/pricing)
* Een gebruikersaccount in Bonusly met beheerdersmachtigingen

> [!NOTE]
> De Azure AD-integratie is gebaseerd op de [Bonusly Rest API](https://konghq.com/solutions/gateway/), die beschikbaar is voor Bonusly-ontwikkelaars.

## <a name="adding-bonusly-from-the-gallery"></a>Bonusly toevoegen vanuit de galerij

Voordat u Bonusly configureert voor automatische gebruikersvoorziening met Azure AD, moet u Bonusly toevoegen vanuit de Azure AD-toepassingsgalerie aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Bonusly toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ In het zoekvak **Bonus,** selecteer **Bonusly** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

    ![Bonusly in de resultatenlijst](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>Gebruikers toewijzen aan Bonusly

Azure Active Directory gebruikt een concept genaamd 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn 'toegewezen' aan een toepassing in Azure AD gesynchroniseerd. 

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot Bonusly nodig hebben. Eenmaal besloten, u deze gebruikers en/ of groepen aan Bonusly toewijzen door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>Belangrijke tips voor het toewijzen van gebruikers aan Bonusly

* Het wordt aanbevolen dat één Azure AD-gebruiker aan Bonusly wordt toegewezen om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan Bonusly toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>Automatische gebruikersvoorziening configureren voor Bonusly

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen in Bonus te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-on in te schakelen voor Bonusly, volgens de instructies in de [Bonusly single sign-on tutorial](bonus-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>Ga als het gaat om het configureren van automatische gebruikersinrichting voor Bonusly in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en selecteer **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Vervolgens Bonus .**

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Bonus .**

    ![De bonuslink in de lijst Met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Bonusly Provisioning](./media/bonusly-provisioning-tutorial/ProvisioningTab.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Bonusly Provisioning](./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png)

5. Voer onder de sectie **Beheerdersreferenties** het **geheime token** van uw Bonusly-account in zoals beschreven in stap 6.

    ![Bonusly Provisioning](./media/bonusly-provisioning-tutorial/secrettoken.png)

6. Het **geheime token** voor uw Bonusly-account bevindt zich in Admin > Company > **Integraties.** Klik in de sectie **Als u wilt coderen** op API > Nieuwe API **Access-token maken** om een nieuw geheim token te maken.

    ![Bonusly Provisioning](./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png)

    ![Bonusly Provisioning](./media/bonusly-provisioning-tutorial/BonsulyRestApi.png)

    ![Bonusly Provisioning](./media/bonusly-provisioning-tutorial/CreateToken.png)

7. Typ in het volgende scherm een naam voor het toegangstoken in het meegeleverde tekstvak en druk op **Api-toets maken**. Het nieuwe toegangstoken verschijnt gedurende enkele seconden in een pop-up.

    ![Bonusly Provisioning](./media/bonusly-provisioning-tutorial/Token01.png)

    ![Bonusly Provisioning](./media/bonusly-provisioning-tutorial/Token02.png)

8. Klik op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Bonusly wanneer u de velden in stap 5 bevolken. Als de verbinding mislukt, moet u ervoor zorgen dat uw Bonusly-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Bonusly Provisioning](./media/bonusly-provisioning-tutorial/TestConnection.png)

9. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje **Een e-mailmelding verzenden wanneer er een fout optreedt**in.

    ![Bonusly Provisioning](./media/bonusly-provisioning-tutorial/EmailNotification.png)

10. Klik op **Opslaan**.

11. Selecteer Azure **Active Directory-gebruikers synchroniseren in**de sectie **Toewijzingen** om extra te gebruiken .

    ![Bonusly Provisioning](./media/bonusly-provisioning-tutorial/UserMappings.png)

12. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Bonusly in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **Eigenschappen matching** worden gebruikt om de gebruikersaccounts in Bonusly te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Bonusly Provisioning](./media/bonusly-provisioning-tutorial/UserAttributeMapping.png)

13. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

14. Als u de Azure AD-inrichtingsservice voor Bonusly wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen.**

    ![Bonusly Provisioning](./media/bonusly-provisioning-tutorial/ProvisioningStatus.png)

15. Definieer de gebruikers en/of groepen die u aan Bonusly wilt inrichten door de gewenste waarden in **Bereik** te kiezen in de sectie **Instellingen.**

    ![Bonusly Provisioning](./media/bonusly-provisioning-tutorial/ScopeSync.png)

16. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Bonusly Provisioning](./media/bonusly-provisioning-tutorial/SaveProvisioning.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitenrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Bonusly.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png
