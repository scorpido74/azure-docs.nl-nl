---
title: 'Zelfstudie: Signagelive configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Signagelive.
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
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: bbd0d3c60c5cc4056d5cbadfc7c6d90ae2a37ca6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063234"
---
# <a name="tutorial-configure-signagelive--for-automatic-user-provisioning"></a>Zelfstudie: Signagelive configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Signagelive en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen voor Signagelive.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant.
* [Een Signagelive huurder](https://signagelive.com/pricing/)
* Een gebruikersaccount in Signagelive met beheerdersmachtigingen.

## <a name="assigning-users-to-signagelive"></a>Gebruikers toewijzen aan Signagelive   

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot Signagelive nodig hebben. Eenmaal besloten, u deze gebruikers en/ of groepen toewijzen aan Signagelive door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-signagelive"></a>Belangrijke tips voor het toewijzen van gebruikers aan Signagelive   

* Het wordt aanbevolen dat één Azure AD-gebruiker aan Signagelive wordt toegewezen om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan Signagelive toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="setup-signagelive--for-provisioning"></a>Setup Signagelive voor provisioning

Voordat u Signagelive configureert voor automatische gebruikersvoorziening met Azure AD, moet u SCIM-inrichting op Signagelive inschakelen.

  Neem contact op met [Signagelive](mailto:development@signagelive.com) om het geheime token te verkrijgen dat nodig is om SCIM-provisioning te configureren.

## <a name="add-signagelive-from-the-gallery"></a>Signagelive toevoegen vanuit de galerij

Als u Signagelive wilt configureren voor automatische gebruikersvoorziening met Azure AD, moet u Signagelive vanuit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Als u Signagelive wilt toevoegen vanuit de Azure AD-toepassingsgalerie, voert u de volgende stappen uit:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Signagelive**in het zoekvak, selecteer **Signagelive** in het resultatenpaneel en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Signagelive in de resultatenlijst](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-signagelive"></a>Automatische gebruikersvoorziening configureren voor Signagelive    

In deze sectie u de Azure AD-inrichtingsservice configureren om gebruikers en/of groepen in Signagelive te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
>  U er ook voor kiezen om SAML-gebaseerde single sign-on in te schakelen voor Signagelive, volgens de instructies in de [Signagelive Single sign-on tutorial.](Signagelive-tutorial.md) Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="to-configure-automatic-user-provisioning-for-signagelive--in-azure-ad"></a>Ga als een te meer met de automatische gebruikersvoorziening voor Signagelive in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen de optie **Signagelive**.

    ![De Signagelive-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie ` https://samlapi.signagelive.com/scim/v2` Beheerdersreferenties invoer in **Tenant-URL**in . Voer in het veld **Secret Token** de **tokenwaarde voor de drager** in die wordt geleverd door het engineeringontwikkelingsteam. Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Signagelive. Als de verbinding mislukt, moet u ervoor zorgen dat uw Signagelive-account beheerdersmachtigingen heeft en het opnieuw proberen.
    ![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer Azure **Active Directory-gebruikers synchroniseren met Signagelive**onder de sectie **Toewijzingen** .

    ![Signagelive Gebruikerstoewijzingen](media/signagelive-provisioning-tutorial/usermapping.png)

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Signagelive in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Signagelive te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Kenmerken van signagelive-gebruikers](media/signagelive-provisioning-tutorial/userattribute.png)

10. Selecteer Azure Active Directory Group synchroniseren in de sectie **Toewijzingen** in de sectie **Azure Active Directory Group synchroniseren met Signagelive**.

    ![Signagelive Gebruikerstoewijzingen](media/signagelive-provisioning-tutorial/groupmapping.png)

11. Controleer de groepskenmerken die zijn gesynchroniseerd van Azure AD naar Signagelive in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **Eigenschappen matching** worden gebruikt om de groepsaccounts in Signagelive te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Kenmerken van signagelive-gebruikers](media/signagelive-provisioning-tutorial/groupattribute.png)

12. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

13. Als u de Azure AD-inrichtingsservice voor Signagelive wilt inschakelen, wijzigt u de **inrichtingsstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten aan Signagelive door de gewenste waarden in **Scope** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

15. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan latere synchronisaties. Zie [Hoe lang het duurt voordat](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)gebruikers en/of groepen worden ingericht voor meer informatie over hoe lang het duurt voordat gebruikers zijn voorzien. 

U de sectie **Huidige status** gebruiken om de voortgang te controleren en koppelingen naar uw inrichtingsactiviteitenrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Signagelive. Zie [De status van gebruikersinrichting controleren](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)voor meer informatie . Zie [Rapportage over automatische gebruikersaccountinrichting](../app-provisioning/check-status-user-account-provisioning.md)voor het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)
