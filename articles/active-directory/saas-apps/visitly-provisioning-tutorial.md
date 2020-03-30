---
title: 'Zelfstudie: Visitly configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te deprovisioneneren aan Visitly.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 73cc1a58689db7902843f222aa4874a5e188be44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063153"
---
# <a name="tutorial-configure-visitly-for-automatic-user-provisioning"></a>Zelfstudie: Visitly configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die u uitvoert in Visitly en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers of groepen automatisch te voorzien en te deprovisioneren voor Visitly.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD-service voor het inrichten van gebruikers. Zie [Gebruikersinrichting en deprovisioning voor software-as-a-service (SaaS)-toepassingen automatiseren met Azure Active Directory](../app-provisioning/user-provisioning.md)voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen.
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-voorvertoningen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant
* [Een Visitly huurder](https://www.visitly.io/pricing/)
* Een gebruikersaccount in Visitly met beheerdersmachtigingen

## <a name="assign-users-to-visitly"></a>Gebruikers toewijzen aan Visitly 

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van automatische gebruikersinrichting worden alleen de gebruikers of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, bepaalt u welke gebruikers of groepen in Azure AD toegang tot Visitly nodig hebben. Wijs deze gebruikers of groepen vervolgens toe aan Visitly door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-visitly"></a>Belangrijke tips voor het toewijzen van gebruikers aan Visitly 

* We raden u aan één Azure AD-gebruiker aan Visitly toe te wijzen om de automatische configuratie van gebruikersinrichting te testen. Extra gebruikers of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker aan Visitly toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoogvenster Toewijzing. Gebruikers met de functie Standaardtoegang zijn uitgesloten van inrichten.

## <a name="set-up-visitly-for-provisioning"></a>Visitly instellen voor inrichting

Voordat u Visitly configureert voor automatische gebruikersvoorziening met Azure AD, moet u De inrichting van System for Cross-domain Identity Management (SCIM) op Visitly inschakelen.

1. Log hier [Visitly](https://app.visitly.io/login)in Selecteer Synchronisatie**van hosthostintegratie met** **integraties** > .

    ![Hostsynchronisatie](media/Visitly-provisioning-tutorial/login.png)

2. Selecteer de sectie **Azure AD.**

    ![Azure AD-sectie](media/Visitly-provisioning-tutorial/integration.png)

3. Kopieer de **API-sleutel**. Deze waarden worden ingevoerd in het vak **Secret Token** op het tabblad **Provisioning** van uw Visitly-toepassing in de Azure-portal.

    ![API-sleutel](media/Visitly-provisioning-tutorial/token.png)


## <a name="add-visitly-from-the-gallery"></a>Visitly toevoegen vanuit de galerie

Als u Visitly wilt configureren voor automatische gebruikersvoorziening met Azure AD, voegt u Visitly uit de Azure AD-toepassingsgalerie toe aan uw lijst met beheerde SaaS-toepassingen.

Voer deze stappen uit om Visitly toe te voegen vanuit de Azure AD-toepassingsgalerie.

1. Selecteer Azure **Active Directory**in de [Azure-portal](https://portal.azure.com)in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Visitly**in, selecteer **Visitly** in het deelvenster Resultaten en selecteer **Toevoegen** om de toepassing toe te voegen.

    ![Visitly in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-visitly"></a>Automatische gebruikersvoorziening configureren voor Visitly 

In deze sectie u de Azure AD-inrichtingsservice configureren om gebruikers of groepen in Visitly te maken, bij te werken en uit te schakelen op basis van gebruikers- of groepstoewijzingen in Azure AD.

> [!TIP]
> Als u saml-gebaseerde aanmelding voor Visitly wilt inschakelen, volgt u de instructies in de [zelfstudie voor eenmalige aanmelding van Visitly.](Visitly-tutorial.md) Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen.

### <a name="configure-automatic-user-provisioning-for-visitly-in-azure-ad"></a>Automatische gebruikersinrichting voor Visitly configureren in Azure AD

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer Alle**toepassingen** **voor Ondernemingen** > selecteren .

    ![Alle toepassingen](common/enterprise-applications.png)

2. Selecteer **Visitly** in de lijst met toepassingen.

    ![De Visitly-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Inrichtingsmodus ingesteld op automatisch](common/provisioning-automatic.png)

5. Voer onder de sectie Beheerdersreferenties `https://api.visitly.io/v1/usersync/SCIM` de waarden en **API-sleutel** invoeren die eerder zijn opgehaald in respectievelijk **Tenant-URL** en Secret **Token.** Selecteer **Testverbinding** om ervoor te zorgen dat Azure AD verbinding kan maken met Visitly. Als de verbinding mislukt, moet u ervoor zorgen dat uw Visitly-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![URL van tenant + token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het vak **Berichte-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen. Schakel het selectievakje **Een e-mailmelding verzenden wanneer er een fout optreedt** in.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer Azure **Active Directory-gebruikers synchroniseren in de**sectie **Toewijzingen** in De optie Azure Active Directory Users synchroniseren om bezoekend .

    ![Visitly user mappings](media/visitly-provisioning-tutorial/usermapping.png)

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Visitly in de sectie **Kenmerktoewijzingen.** De kenmerken die zijn geselecteerd als **Eigenschappen matching** worden gebruikt om de gebruikersaccounts in Visitly te matchen voor updatebewerkingen. Selecteer **Opslaan** om wijzigingen door te voeren.

    ![De kenmerken van de bezoekende gebruiker](media/visitly-provisioning-tutorial/userattribute.png)

10. Als u scopingfilters wilt configureren, volgt u de instructies in de zelfstudie van het [filter Scoping](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Als u de Azure AD-inrichtingsservice voor Visitly wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen.**

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers of groepen die u wilt inrichten in Visitly door de gewenste waarden in **Bereik** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

13. Wanneer u klaar bent voor inlevering, selecteert u **Opslaan**.

    ![Configuratie van het opslaan van de inrichting](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan latere synchronisaties. Zie Hoe lang het duurt voordat gebruikers of groepen worden ingericht voor meer informatie over het inrichten [van gebruikers of groepen?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

U de sectie **Huidige status** gebruiken om de voortgang te controleren en koppelingen naar uw inrichtingsactiviteitenrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Visitly. Zie [De status van gebruikersinrichting controleren](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)voor meer informatie . Zie [Rapportage over automatische gebruikersaccountinrichting](../app-provisioning/check-status-user-account-provisioning.md)voor het lezen van de azure AD-inrichtingslogboeken.

## <a name="connector-limitations"></a>Verbindingsbeperkingen

Visitly ondersteunt geen harde verwijderingen. Alles is zacht verwijderen alleen.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountinrichting voor bedrijfsapps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)
