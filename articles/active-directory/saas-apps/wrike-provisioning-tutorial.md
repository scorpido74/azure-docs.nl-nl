---
title: 'Zelfstudie: Wrike configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te deprovisioneneren voor Wrike.
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
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: 5dd4f5ac6152c22b5e2a84ecc0774672bcd5590b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064186"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>Zelfstudie: Wrike configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die u uitvoert in Wrike en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers of groepen automatisch in te richten en te deprovisioneren in Wrike.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD-service voor het inrichten van gebruikers. Zie [Gebruikersinrichting en deprovisioning voor software-as-a-service (SaaS)-toepassingen automatiseren met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen.
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-voorvertoningen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant
* [Een Wrike huurder](https://www.wrike.com/price/)
* Een gebruikersaccount in Wrike met beheerdersmachtigingen

## <a name="assign-users-to-wrike"></a>Gebruikers toewijzen aan Wrike
Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van automatische gebruikersinrichting worden alleen de gebruikers of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, bepaalt u welke gebruikers of groepen in Azure AD toegang tot Wrike nodig hebben. Wijs deze gebruikers of groepen vervolgens toe aan Wrike door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>Belangrijke tips voor het toewijzen van gebruikers aan Wrike

* We raden u aan één Azure AD-gebruiker aan Wrike toe te wijzen om de automatische configuratie van gebruikersinrichting te testen. Extra gebruikers of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker aan Wrike toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoogvenster Toewijzing. Gebruikers met de functie Standaardtoegang zijn uitgesloten van inrichten.

## <a name="set-up-wrike-for-provisioning"></a>Wrike instellen voor provisioning

Voordat u Wrike configureert voor automatische gebruikersvoorziening met Azure AD, moet u System for Cross-domain Identity Management (SCIM) inrichten op Wrike inschakelen.

1. Meld u aan bij uw [Wrike-beheerdersconsole](https://www.Wrike.com/login/). Ga naar je tenant-id. Selecteer **Apps &-integraties**.

    ![Apps & integraties](media/Wrike-provisioning-tutorial/admin.png)

2.  Ga naar **Azure AD** en selecteer deze.

    ![Azure AD](media/Wrike-provisioning-tutorial/Capture01.png)

3.  Selecteer SCIM. Kopieer de **basis-URL**.

    ![Basis-URL](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. Selecteer **API** > **Azure SCIM**.

    ![Azure SCIM](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  Er wordt een pop-up geopend. Voer hetzelfde wachtwoord in dat u eerder hebt gemaakt om een account te maken.

    ![Wrike Token maken](media/Wrike-provisioning-tutorial/password.png)

6.  Kopieer het **geheime token**en plak deze in Azure AD. Selecteer **Opslaan** om de inrichtingsinstelling op Wrike te voltooien.

    ![Permanente toegangstoken](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>Wrike toevoegen vanuit de galerie

Voordat u Wrike configureert voor automatische gebruikersvoorziening met Azure AD, voegt u Wrike uit de Azure AD-toepassingsgalerie toe aan uw lijst met beheerde SaaS-toepassingen.

Voer de volgende stappen uit om Wrike toe te voegen vanuit de Azure AD-toepassingsgalerie.

1. Selecteer Azure **Active Directory**in de [Azure-portal](https://portal.azure.com)in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Wrike**in het zoekvak, selecteer **Wrike** in het deelvenster Resultaten en selecteer **Toevoegen** om de toepassing toe te voegen.

    ![Wrike in de resultatenlijst](common/search-new-app.png)


## <a name="configure-automatic-user-provisioning-to-wrike"></a>Automatische gebruikersinrichting configureren voor Wrike 

In deze sectie u de Azure AD-inrichtingsservice configureren om gebruikers of groepen in Wrike te maken, bij te werken en uit te schakelen op basis van gebruikers- of groepstoewijzingen in Azure AD.

> [!TIP]
> Als u SAML-gebaseerde aanmelding voor Wrike wilt inschakelen, volgt u de instructies in de [wrike-zelfsigne-zelfstudie.](wrike-tutorial.md) Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen.

### <a name="configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>Automatische gebruikersinrichting voor Wrike configureren in Azure AD

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer Alle**toepassingen** **voor Ondernemingen** > selecteren .

    ![Alle toepassingen](common/enterprise-applications.png)

2. Selecteer **Wrike**in de lijst met toepassingen .

    ![De Wrike-link in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Inrichtingsmodus ingesteld op automatisch](common/provisioning-automatic.png)

5. Voer onder de sectie Beheerdersreferenties respectievelijk de **URL van** de basis en **permanente toegangstoken** in die eerder zijn opgehaald in **tenant-URL** en **secret token**in. Selecteer **Testverbinding** om ervoor te zorgen dat Azure AD verbinding kan maken met Wrike. Als de verbinding mislukt, moet u ervoor zorgen dat uw Wrike-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![URL van tenant + token](common/provisioning-testconnection-tenanturltoken.png)

7. Voer in het vak **Berichte-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen. Schakel het selectievakje **Een e-mailmelding verzenden wanneer er een fout optreedt** in.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

8. Selecteer **Opslaan**.

9. Selecteer Azure **Active Directory-gebruikers synchroniseren met Wrike**onder de sectie **Toewijzingen** .

    ![Wrike gebruikerskaarten](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Wrike in de sectie **Toewijzingen van kenmerken.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Wrike te matchen voor updatebewerkingen. Selecteer **Opslaan** om wijzigingen door te voeren.

    ![Wrike-gebruikerskenmerken](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. Als u scopingfilters wilt configureren, volgt u de instructies in de zelfstudie van het [filter Scoping](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Als u de Azure AD-inrichtingsservice voor Wrike wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen.**

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

13. Definieer de gebruikers of groepen die u wilt inrichten voor Wrike door de gewenste waarden in **Bereik** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

14. Wanneer u klaar bent voor inlevering, selecteert u **Opslaan**.

    ![Configuratie van het opslaan van de inrichting](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan latere synchronisaties. Zie Hoe lang het duurt voordat gebruikers of groepen worden ingericht voor meer informatie over het inrichten [van gebruikers of groepen?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

U de sectie **Huidige status** gebruiken om de voortgang te controleren en koppelingen naar uw inrichtingsactiviteitenrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Wrike. Zie [De status van gebruikersinrichting controleren](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)voor meer informatie . Zie [Rapportage over automatische gebruikersaccountinrichting](../app-provisioning/check-status-user-account-provisioning.md)voor het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountinrichting voor bedrijfsapps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)
