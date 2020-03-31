---
title: 'Zelfstudie: StarLeaf configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor StarLeaf.
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
ms.date: 07/19/2019
ms.author: zhchia
ms.openlocfilehash: 520373fc6a05bcaada973273e3553f9da623c669
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064288"
---
# <a name="tutorial-configure-starleaf-for-automatic-user-provisioning"></a>Zelfstudie: StarLeaf configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in StarLeaf en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen voor StarLeaf.

> [!NOTE]
>  In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Voorbeeld. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant.
* [Een StarLeaf huurder](https://www.starleaf.com/solutions/).
* Een gebruikersaccount in StarLeaf met beheerdersmachtigingen.

## <a name="assign-users-to-starleaf"></a>Gebruikers toewijzen aan StarLeaf
Azure Active Directory gebruikt een concept genaamd toewijzingen om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en groepen in Azure AD toegang tot StarLeaf nodig hebben. Vervolgens u de gebruikers en groepen toewijzen aan StarLeaf door deze instructies op te [volgen.](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-starleaf"></a>Belangrijke tips voor het toewijzen van gebruikers aan StarLeaf

* Het wordt aanbevolen dat één Azure AD-gebruiker aan StarLeaf wordt toegewezen om de automatische configuratie van gebruikersinrichting te testen. Extra gebruikers en groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker aan StarLeaf toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de functie Standaardtoegang zijn uitgesloten van inrichten.

## <a name="set-up-starleaf-for-provisioning"></a>StarLeaf instellen voor inrichten

Voordat u StarLeaf configureert voor automatische gebruikersvoorziening met Azure AD, moet u SCIM-provisioning configureren in StarLeaf:

1. Meld u aan bij uw [StarLeaf-beheerconsole](https://portal.starleaf.com/#page=login). Navigeren naar **integraties** > **Integratie toevoegen**.

    ![StarLeaf Add SCIM](media/starleaf-provisioning-tutorial/image00.png)

2. Selecteer het **type dat** Microsoft Azure Active Directory wordt. Voer een geschikte naam in **Naam**in . Klik op **Toepassen**.

    ![StarLeaf Add SCIM](media/starleaf-provisioning-tutorial/image01.png)

3.  De **URL-url-basis** en **de tokenwaarden van Access** worden vervolgens weergegeven. Deze waarden worden ingevoerd in de velden **URL van tenant** en geheim **token** op het tabblad Inrichten van uw StarLeaf-toepassing in de Azure-portal. 

    ![StarLeaf-token maken](media/starleaf-provisioning-tutorial/image02.png)

## <a name="add-starleaf-from-the-gallery"></a>StarLeaf toevoegen vanuit de galerij

Als u StarLeaf wilt configureren voor automatische gebruikersvoorziening met Azure AD, moet u StarLeaf vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om StarLeaf toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **StarLeaf**in het zoekvak , selecteer **StarLeaf** in het resultatenpaneel.
    ![StarLeaf in de resultatenlijst](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-starleaf"></a>Automatische gebruikersinrichting configureren voor StarLeaf

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen in StarLeaf te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **StarLeaf**in de lijst met toepassingen .

    ![De StarLeaf-koppeling in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie Beheerdersreferenties de URL en **Access Token-waarden** van **SCIM Base** in die eerder zijn opgehaald in respectievelijk **tenant-URL** en **Secret Token.** Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met StarLeaf. Als de verbinding mislukt, moet u ervoor zorgen dat uw StarLeaf-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fout moet ontvangen en schakel het selectievakje **Een e-mailmelding verzenden in wanneer er een fout optreedt in.**

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer Azure **Active Directory-gebruikers synchroniseren met StarLeaf**onder de sectie **Toewijzingen** .

    ![StarLeaf-token maken](media/starleaf-provisioning-tutorial/usermapping.png)

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar StarLeaf in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in StarLeaf te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![StarLeaf-token maken](media/starleaf-provisioning-tutorial/userattribute.png)


10. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)


11. Als u de Azure AD-inrichtingsservice voor StarLeaf wilt inschakelen, wijzigt u de **inrichtingsstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u aan StarLeaf wilt inrichten door de gewenste waarden in **Scope** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

13. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitenrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op StarLeaf.

Zie [Rapportage over automatische gebruikersaccountinrichting](../app-provisioning/check-status-user-account-provisioning.md) voor meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Verbindingsbeperkingen

* StarLeaf ondersteunt momenteel geen groepsinrichting. 
* StarLeaf vereist **dat e-mail-** en **userName-waarden** dezelfde bronwaarde hebben.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountinrichting voor bedrijfsapps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md).
