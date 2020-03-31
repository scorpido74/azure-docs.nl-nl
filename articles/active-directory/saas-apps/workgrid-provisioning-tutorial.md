---
title: 'Zelfstudie: Werkraster configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Workgrid.
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
ms.openlocfilehash: 94d70447117c73a309959ddf66972c921aa5e687
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062804"
---
# <a name="tutorial-configure-workgrid--for-automatic-user-provisioning"></a>Zelfstudie: Werkraster configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Workgrid en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen naar Workgrid.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant.
* [Een Workgrid-tenant](https://www.workgrid.com/)
* Een gebruikersaccount in Workgrid met beheerdersmachtigingen.

## <a name="assigning-users-to-workgrid"></a>Gebruikers toewijzen aan Workgrid 

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot Workgrid nodig hebben. Zodra u hebt besloten, u deze gebruikers en/of groepen toewijzen aan Workgrid door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-workgrid"></a>Belangrijke tips voor het toewijzen van gebruikers aan Workgrid 

* Het wordt aanbevolen dat één Azure AD-gebruiker aan Workgrid wordt toegewezen om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan Workgrid toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="set-up-workgrid-for-provisioning"></a>Workgrid instellen voor inrichten

Voordat u Workgrid configureert voor automatische gebruikersvoorziening met Azure AD, moet u SCIM-inrichting op Workgrid inschakelen.

1. Log in bij Workgrid. Navigeer naar **gebruikers > gebruikersinrichting**.

    ![Workgrid](media/Workgrid-provisioning-tutorial/user.png)

2. Klik **onder Api voor accountbeheer**op Referenties **maken**.

    ![Workgrid](media/Workgrid-provisioning-tutorial/scim.png)

3. Kopieer de waarden **SCIM-eindpunt** en **Access Token.** Deze worden ingevoerd in het **veld Url** van tenant en **geheim token** op het tabblad Inrichten van uw Workgrid-toepassing in de Azure-portal.

    ![Workgrid](media/Workgrid-provisioning-tutorial/token.png)


## <a name="add-workgrid--from-the-gallery"></a>Werkraster toevoegen vanuit de galerie

Als u Workgrid wilt configureren voor automatische gebruikersvoorziening met Azure AD, moet u Workgrid vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Workgrid toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Workgrid**in het zoekvak, selecteer **Workgrid** in het deelvenster Resultaten en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Workgrid in de resultatenlijst](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-workgrid"></a>Automatische gebruikersvoorziening configureren voor Workgrid  

In deze sectie u de Azure AD-inrichtingsservice configureren om gebruikers en/of groepen in Workgrid te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-on voor Workgrid in te schakelen, volgens de instructies in de [Workgrid Single sign-on tutorial](Workgrid-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren

### <a name="to-configure-automatic-user-provisioning-for-workgrid--in-azure-ad"></a>Ga als een automatisch beheer van de gebruiker voor Workgrid in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Workgrid**in de lijst met toepassingen .

    ![De koppeling Workgrid in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie Beheerdersreferenties de **scim-eindpunt-** en **Access-tokenwaarden** in die eerder zijn opgehaald in respectievelijk **tenant-URL** en **Secret Token.** Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Workgrid. Als de verbinding mislukt, moet u ervoor zorgen dat uw Workgrid-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer azure **Active Directory-gebruikers synchroniseren met Workgrid**onder de sectie **Toewijzingen** .

    ![Toewijzingen van Workgrid-gebruikers](media/Workgrid-provisioning-tutorial/usermapping.png)

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Workgrid in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Workgrid te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Kenmerken van Workgrid-gebruikers](media/Workgrid-provisioning-tutorial/userattribute.png)

10. Selecteer onder de sectie **Toewijzingen** de optie **Azure Active Directory Groups synchroniseren met Workgrid**

    ![Toewijzingen van Workgrid-gebruikers](media/Workgrid-provisioning-tutorial/groupmapping.png)

12. Controleer de groepskenmerken die zijn gesynchroniseerd van Azure AD naar Workgrid in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Workgrid te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Toewijzingen van Workgrid-gebruikers](media/Workgrid-provisioning-tutorial/groupattribute.png)

13. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

14. Als u de Azure AD-inrichtingsservice voor Workgrid wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

15. Definieer de gebruikers en/of groepen die u wilt inrichten in Workgrid door de gewenste waarden in **Bereik** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

16. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan latere synchronisaties. Zie [Hoe lang het duurt voordat](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)gebruikers en/of groepen worden ingericht voor meer informatie over hoe lang het duurt voordat gebruikers zijn voorzien.

U de sectie **Huidige status** gebruiken om de voortgang te controleren en koppelingen naar uw inrichtingsactiviteitenrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Workgrid. Zie [De status van gebruikersinrichting controleren](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)voor meer informatie . Zie [Rapportage over automatische gebruikersaccountinrichting](../app-provisioning/check-status-user-account-provisioning.md)voor het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)
