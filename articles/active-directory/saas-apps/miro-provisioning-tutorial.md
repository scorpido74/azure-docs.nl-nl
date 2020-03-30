---
title: 'Zelfstudie: Miro configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Miro.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 81eecaff-d888-472b-a1c2-0b7b0c9ccd8d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2019
ms.author: Zhchia
ms.openlocfilehash: b7e1907e3fa1eb9d775fb7662445b08d5671e0b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063407"
---
# <a name="tutorial-configure-miro-for-automatic-user-provisioning"></a>Zelfstudie: Miro configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Miro en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen ten opzichte van Miro.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant
* [Een Miro huurder](https://miro.com/pricing/)
* Een gebruikersaccount in Miro met beheerdersmachtigingen.

## <a name="assigning-users-to-miro"></a>Gebruikers toewijzen aan Miro

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot Miro nodig hebben. Eenmaal besloten, u deze gebruikers en/ of groepen toewijzen aan Miro door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-miro"></a>Belangrijke tips voor het toewijzen van gebruikers aan Miro

* Het wordt aanbevolen dat één Azure AD-gebruiker aan Miro wordt toegewezen om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan Miro toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="set-up-miro-for-provisioning"></a>Miro instellen voor provisioning

1.  Neem contact op **met** miro support support@miro.comteam op . Deze waarde wordt ingevoerd in het veld Geheim token op het tabblad Provisioning van uw Miro-toepassing in de Azure-portal.

## <a name="add-miro-from-the-gallery"></a>Miro toevoegen vanuit de galerij

Voordat u Miro configureert voor automatische gebruikersvoorziening met Azure AD, moet u Miro uit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Miro toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Miro**in het deelvenster **Resultaten** in het zoekvak en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Miro in de resultatenlijst](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-miro"></a>Automatische gebruikersvoorziening configureren voor Miro 

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen in Miro te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-on in te schakelen voor Miro, volgens de instructies in de [Miro Single sign-on tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-tutorial). Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

> [!NOTE]
> Voor meer informatie over Miro's SCIM eindpunt, verwijs [dit](https://help.miro.com/hc/en-us/articles/360036777814).

### <a name="to-configure-automatic-user-provisioning-for-miro-in-azure-ad"></a>Automatische gebruikersinrichting voor Miro in Azure AD configureren

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Miro**in de lijst met toepassingen .

    ![De Miro-link in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie `https://miro.com/api/v1/scim` **Beheerdersreferenties** invoer in **Tenant-URL**in . Voer de waarde van de **SCIM-verificatietoken** in die eerder in **Secret Token**is opgehaald. Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Miro. Als de verbinding mislukt, moet u ervoor zorgen dat uw Miro-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer Azure **Active Directory-gebruikers synchroniseren met Miro**in de sectie **Toewijzingen.**

    ![Miro-gebruikerstoewijzingen](media/miro-provisioning-tutorial/usermappings.png)

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Miro in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Miro te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Miro-gebruikerskenmerken](media/miro-provisioning-tutorial/userattributes.png)

10. Selecteer Azure **Active Directory-groepen synchroniseren in**de sectie **Toewijzingen** synchroniseren met Miro .

    ![Miro Group Mappings](media/miro-provisioning-tutorial/groupmappings.png)

11. Controleer de groepskenmerken die zijn gesynchroniseerd van Azure AD naar Miro in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de groepen in Miro te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren. Schakel **het selectievakje Maken** en **verwijderen uit** onder **Doelobjectacties** uit, omdat Miro SCIM API geen ondersteuning biedt voor het maken en verwijderen van groepen.

    ![Miro-groepskenmerken](media/miro-provisioning-tutorial/groupattributes.png)

12. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

13. Als u de Azure AD-inrichtingsservice voor Miro wilt inschakelen, wijzigt u de **inrichtingsstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u aan Miro wilt inrichten door de gewenste waarden in **Scope** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

15. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitenrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Miro.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Verbindingsbeperkingen

* Het SCIM-eindpunt van Miro staat **bewerkingen voor** groepen niet maken en **verwijderen** toe. Het ondersteunt alleen groep **Update** operatie.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)

