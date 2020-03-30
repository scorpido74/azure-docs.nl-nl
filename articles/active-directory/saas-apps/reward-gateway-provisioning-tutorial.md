---
title: 'Zelfstudie: Beloningsgateway configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Reward Gateway.
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
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 928d48907e43de5e65ca5604ff878bfb83d5e95b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060988"
---
# <a name="tutorial-configure-reward-gateway-for-automatic-user-provisioning"></a>Zelfstudie: Reward Gateway configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Reward Gateway en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen voor Reward Gateway.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
>
> Deze connector is momenteel in openbare preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant.
* Een [Reward Gateway tenant](https://www.rewardgateway.com/).
* Een gebruikersaccount in Reward Gateway met beheerdersmachtigingen.

## <a name="assigning-users-to-reward-gateway"></a>Gebruikers toewijzen aan Reward Gateway 

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot Reward Gateway nodig hebben. Eenmaal besloten, u deze gebruikers en/of groepen toewijzen aan Reward Gateway door de instructies te volgen in [Een gebruiker of groep toewijzen aan een bedrijfsapp.](../manage-apps/assign-user-or-group-access-portal.md)


## <a name="important-tips-for-assigning-users-to-reward-gateway"></a>Belangrijke tips voor het toewijzen van gebruikers aan Reward Gateway 

* Het wordt aanbevolen dat één Azure AD-gebruiker wordt toegewezen aan Reward Gateway om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan Reward Gateway toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="setup-reward-gateway--for-provisioning"></a>Reward-gateway instellen voor inrichten
Voordat u Reward Gateway configureert voor automatische gebruikersvoorziening met Azure AD, moet u SCIM-inrichting op Reward Gateway inschakelen.

1. Meld u aan bij uw [Reward Gateway Admin Console](https://rewardgateway.photoshelter.com/login/). Klik **op Integraties**.

    ![Gateway-beheerconsole belonen](media/reward-gateway-provisioning-tutorial/image00.png)

2.  Selecteer **Mijn integratie**.

    ![Gateway-beheerconsole belonen](media/reward-gateway-provisioning-tutorial/image001.png)

3.  Kopieer de waarden van **scim-URL (v2)** en **OAuth-token aan toonder**. Deze waarden worden ingevoerd in het veld URL van tenant en geheim token op het tabblad Inrichten van uw Reward Gateway-toepassing in de Azure-portal.

    ![Gateway-beheerconsole belonen](media/reward-gateway-provisioning-tutorial/image03.png)

## <a name="add-reward-gateway-from-the-gallery"></a>Reward Gateway toevoegen vanuit de galerie

Als u Reward Gateway wilt configureren voor automatische gebruikersvoorziening met Azure AD, moet u Reward Gateway uit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Reward Gateway toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Reward Gateway**in, selecteer **Reward Gateway** in het deelvenster Resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Reward Gateway in de resultatenlijst](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-reward-gateway"></a>Automatische gebruikersvoorziening configureren voor Reward Gateway  

In deze sectie u de Azure AD-inrichtingsservice configureren om gebruikers en/of groepen in Reward Gateway te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-on voor Reward Gateway in te schakelen, volgens de instructies in de [reward gateway single sign-on tutorial](reward-gateway-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="to-configure-automatic-user-provisioning-for-reward-gateway-in-azure-ad"></a>Ga als het gaat om het configureren van automatische gebruikersinrichting voor Reward Gateway in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Reward Gateway**in de lijst met toepassingen .

    ![De koppeling Beloningsgateway in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie **Beheerdersreferenties** de waarden **SCIM URL (v2)** en **OAuth Bearer Token** in die eerder zijn opgehaald in respectievelijk **tenant-URL** en **Secret Token.** Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met de beloningsgateway. Als de verbinding mislukt, moet u ervoor zorgen dat uw reward gateway-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer Azure **Active Directory-gebruikers synchroniseren om gateway te belonen**onder de sectie **Toewijzingen** .

    ![Gateway-beheerconsole belonen](media/reward-gateway-provisioning-tutorial/user-mappings.png)

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Reward Gateway in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Reward Gateway te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Gateway-beheerconsole belonen](media/reward-gateway-provisioning-tutorial/user-attributes.png)

10. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

11. Als u de Azure AD-inrichtingsservice voor Reward Gateway wilt inschakelen, wijzigt u de **inrichtingsstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten voor Reward Gateway door de gewenste waarden in **Bereik** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

13. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het inrichten van activiteitenrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Reward Gateway.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Verbindingsbeperkingen

Reward Gateway ondersteunt momenteel geen groepsinrichting.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)
