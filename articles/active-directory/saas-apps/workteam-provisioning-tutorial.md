---
title: 'Zelfstudie: Workteam configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Workteam.
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
ms.date: 08/17/2019
ms.author: Zhchia
ms.openlocfilehash: e9b1e93cf543836b282525c53756752630d5e4f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062781"
---
# <a name="tutorial-configure-workteam--for-automatic-user-provisioning"></a>Zelfstudie: Workteam configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Workteam en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en de-provisionen van gebruikers en/of groepen naar Workteam.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant.
* [Een Workteam-huurder](https://workte.am/pricing.html)
* Een gebruikersaccount in Workteam met beheerdersmachtigingen.

## <a name="assigning-users-to-workteam"></a>Gebruikers toewijzen aan Workteam 

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot Workteam nodig hebben. Zodra u hebt besloten, u deze gebruikers en/of groepen toewijzen aan Workteam door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-workteam"></a>Belangrijke tips voor het toewijzen van gebruikers aan Workteam 

* Het wordt aanbevolen dat één Azure AD-gebruiker aan Workteam wordt toegewezen om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan Workteam toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="setup-workteam--for-provisioning"></a>Installatie-werkteam voor inrichten

Voordat u Workteam configureert voor automatische gebruikersvoorziening met Azure AD, moet u SCIM-inrichting op Workteam inschakelen.

1. Log in bij [Workteam](https://app.workte.am/account/signin). Klik **op Instellingen voor organisatie** > **- INSTELLINGEN**.

    ![Workteam](media/workteam-provisioning-tutorial/settings.png)

2. Schuif naar beneden en schakel de inrichtingsmogelijkheden van Workteam in.

    ![Workteam](media/workteam-provisioning-tutorial/icon.png)

3. Kopieer de **basisurl** en **het token aan toonder.** Deze waarden worden ingevoerd in het **veld URL van tenant**en geheim **token** op het tabblad Inrichten van uw Workteam-toepassing in de Azure-portal.

    ![Workteam](media/workteam-provisioning-tutorial/scim.png)


## <a name="add-workteam--from-the-gallery"></a>Workteam toevoegen vanuit de galerie

Als u Workteam wilt configureren voor automatische gebruikersvoorziening met Azure AD, moet u Workteam uit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Workteam toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Workteam**in, selecteer **Workteam** in het deelvenster Resultaten en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Workteam in de resultatenlijst](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-workteam"></a>Automatische gebruikersvoorziening configureren voor Workteam  

In deze sectie u de Azure AD-inrichtingsservice configureren om gebruikers en/of groepen in Workteam te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde eenmalige aanmelding voor Workteam in te schakelen, volgens de instructies in de [zelfstudie workteam Single sign-on](workteam-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren

### <a name="to-configure-automatic-user-provisioning-for-workteam--in-azure-ad"></a>Ga als lid van het werkteam voor automatische gebruikersvoorziening voor Workteam:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Workteam**in de lijst met toepassingen .

    ![De koppeling Workteam in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie Beheerdersreferenties de URL van de **basis** en **de waarden van het token aan toonder** die eerder zijn opgehaald in respectievelijk **tenant-URL** en **Secret Token.** Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Workteam. Als de verbinding mislukt, moet u ervoor zorgen dat uw Workteam-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer Azure **Active Directory-gebruikers synchroniseren met workteam**onder de sectie **Toewijzingen** .

    ![Gebruikerstoewijzingen van workteam](media/workteam-provisioning-tutorial/usermapping.png)

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Workteam in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Workteam te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Gebruikerskenmerken van workteam](media/workteam-provisioning-tutorial/userattribute.png)

11. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

12. Als u de Azure AD-inrichtingsservice voor Workteam wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

13. Definieer de gebruikers en/of groepen die u aan Workteam wilt inrichten door de gewenste waarden in **Bereik** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

14. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan latere synchronisaties. Zie [Hoe lang het duurt voordat](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)gebruikers en/of groepen worden ingericht voor meer informatie over hoe lang het duurt voordat gebruikers zijn voorzien.

U de sectie **Huidige status** gebruiken om de voortgang te controleren en koppelingen naar uw installatieactiviteitsrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Workteam. Zie [De status van gebruikersinrichting controleren](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)voor meer informatie . Zie [Rapportage over automatische gebruikersaccountinrichting](../app-provisioning/check-status-user-account-provisioning.md)voor het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)
