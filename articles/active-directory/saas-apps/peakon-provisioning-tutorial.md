---
title: 'Zelfstudie: Peakon automatische gebruikersinrichting configureren met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Peakon .
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
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 0a67dc8069ee71305a47bd5d2a724a61cec234a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063370"
---
# <a name="tutorial-configure-peakon-for-automatic-user-provisioning"></a>Zelfstudie: Peakon configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Peakon en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen naar Peakon.

> [!NOTE]
>  In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Voorbeeld. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.
## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt

* Een Azure AD-tenant.
* [Een Peakon huurder.](https://peakon.com/us/pricing/)
* Een gebruikersaccount in Peakon met beheerdersmachtigingen.

## <a name="assigning-users-to-peakon"></a>Gebruikers toewijzen aan Peakon

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot Peakon nodig hebben. Eenmaal besloten, u deze gebruikers en/of groepen toewijzen aan Peakon door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-peakon"></a>Belangrijke tips voor het toewijzen van gebruikers aan Peakon 

* Het wordt aanbevolen dat één Azure AD-gebruiker aan Peakon wordt toegewezen om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan Peakon toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="set-up-peakon-for-provisioning"></a>Peakon instellen voor inrichten

1.  Meld u aan bij uw [Peakon-beheerconsole](https://app.Peakon.com/login). Klik op **Configuratie**. 

    ![Peakon-beheerconsole](media/Peakon-provisioning-tutorial/Peakon-admin-configuration.png)

2.  Selecteer **Integraties**.
    
    ![Peakon-employee-provision](media/Peakon-provisioning-tutorial/Peakon-select-integration.png)

3.  Inrichten **van werknemers**inschakelen .

    ![Peakon-employee-provision](media/Peakon-provisioning-tutorial/peakon05.png)

4.  Kopieer de waarden voor **SCIM 2.0 URL** en **OAuth Bearer Token**. Deze waarden worden ingevoerd in het **veld URL van tenant** en geheim **token** op het tabblad Provisioning van uw Peakon-toepassing in de Azure-portal.

    ![Peakon Token maken](media/Peakon-provisioning-tutorial/peakon04.png)

## <a name="add-peakon-from-the-gallery"></a>Peakon toevoegen vanuit de galerie

Als u Peakon wilt configureren voor automatische gebruikersvoorziening met Azure AD, moet u Peakon vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Peakon**in het zoekvak, selecteer **Peakon** in het deelvenster Resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Peakon in de resultatenlijst](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-peakon"></a>Automatische gebruikersvoorziening configureren voor Peakon 

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen in Peakon te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-on in te schakelen voor Peakon, volgens de instructies in de [Peakon Single sign-on tutorial](peakon-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="to-configure-automatic-user-provisioning-for-peakon--in-azure-ad"></a>Ga als bedoeldinname van eindgebruikers voor Peakon in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Peakon**in de lijst met toepassingen .

    ![De koppeling Peakon in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie **Beheerdersreferenties** de **scim 2.0 URL-** en **OAuth-tokenwaarden voor dragers** in die eerder zijn opgehaald in respectievelijk **tenant-URL** en **Secret Token.** Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Peakon. Als de verbinding mislukt, moet u ervoor zorgen dat uw Peakon-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

7. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

8. Klik op **Opslaan**.

9. Selecteer Azure **Active Directory-gebruikers synchroniseren met Peakon**onder de sectie **Toewijzingen** .

    ![Peakon-gebruikerstoewijzingen](media/Peakon-provisioning-tutorial/Peakon-user-mappings.png)

10. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Peakon in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **Eigenschappen matching** worden gebruikt om de gebruikersaccounts in Peakon te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Kenmerken van Peakon-gebruikers](media/Peakon-provisioning-tutorial/Peakon-user-attributes.png)

12. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
    
    ![Inrichtingskader](common/provisioning-scope.png)

15. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitsrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Peakon.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Verbindingsbeperkingen

* Alle aangepaste gebruikerskenmerken in Peakon moeten worden uitgebreid van peakon's `urn:ietf:params:scim:schemas:extension:peakon:2.0:User`aangepaste SCIM-gebruikersextensie van .

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)