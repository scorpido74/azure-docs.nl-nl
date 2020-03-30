---
title: 'Zelfstudie: Robin configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Robin Powered.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 178ca5b3-4155-43ab-84f5-650d25c5a74a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2019
ms.author: Zhchia
ms.openlocfilehash: fabd8a1953bedf6c3db6da443903a6dbd965b01e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060929"
---
# <a name="tutorial-configure-robin-for-automatic-user-provisioning"></a>Zelfstudie: Robin configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in Robin en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen voor Robin.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Deze connector bevindt zich momenteel in Public Preview. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over de algemene gebruiksvoorwaarden van Microsoft Azure.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant
* [Een Robin huurder](https://robinpowered.com/pricing/)
* Een gebruikersaccount in Robin met beheerdersmachtigingen.

## <a name="assigning-users-to-robin"></a>Gebruikers toewijzen aan Robin

Azure Active Directory gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot Robin nodig hebben. Eenmaal besloten, u deze gebruikers en/of groepen toewijzen aan Robin door de instructies hier te volgen:
* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-robin"></a>Belangrijke tips voor het toewijzen van gebruikers aan Robin

* Het wordt aanbevolen dat één Azure AD-gebruiker aan Robin wordt toegewezen om de automatische configuratie van gebruikersinrichting te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan Robin toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="set-up-robin-for-provisioning"></a>Robin instellen voor provisioning

1. Meld u aan bij uw [Robin-beheerdersconsole](https://dashboard.robinpowered.com/login). Navigeer naar **>-integraties beheren > SCIM > Beheren**.

    ![Robin aangedreven admin console](media/robin-provisioning-tutorial/robin-admin.png)

2.  Een nieuw organisatietoken genereren. Als u dit token verliest, u altijd een nieuwe maken zonder bestaande gebruikers te beïnvloeden.

    ![robin powered Add SCIM](media/robin-provisioning-tutorial/robin-token.png)

3.  Kopieer het **SCIM-verificatietoken**. Deze waarde wordt ingevoerd in het veld Geheim token op het tabblad Provisioning van uw Robin-toepassing in de Azure-portal.



## <a name="add-robin-from-the-gallery"></a>Robin toevoegen vanuit de galerie

Voordat u Robin configureert voor automatische gebruikersvoorziening met Azure AD, moet u Robin uit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om Robin toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Selecteer **Azure Active Directory**in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster .

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het deelvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Robin**in het zoekvak en selecteer **Robin** in het deelvenster Resultaten en klik op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Robin in de resultatenlijst](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-robin"></a>Automatische gebruikersvoorziening configureren voor Robin 

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen in Robin te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-on in te schakelen voor Robin, volgens de instructies in de [Robin Single sign-on tutorial](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial). Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren

### <a name="to-configure-automatic-user-provisioning-for-robin-in-azure-ad"></a>Ga als een te meer met de automatische gebruikersvoorziening voor Robin in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Robin**in de lijst met toepassingen .

    ![De robin powered link in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie `https://api.robinpowered.com/v1.0/scim-2` **Beheerdersreferenties** invoer in **Tenant-URL**in . Voer de waarde van de **SCIM-verificatietoken** in die eerder in **Secret Token**is opgehaald. Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Robin. Als de verbinding mislukt, moet u ervoor zorgen dat uw Robin-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Tenant URL + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer Azure **Active Directory-gebruikers synchroniseren met Robin**onder de sectie **Toewijzingen** .

    ![Robin powered User Mappings](media/robin-provisioning-tutorial/robin-user-mapping.png)

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Robin in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Robin te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Robin aangedreven gebruikerskenmerken](media/robin-provisioning-tutorial/robin-user-attribute-mapping.png)

10. Selecteer Azure **Active Directory-groepen synchroniseren met Robin**onder de sectie **Toewijzingen** .

    ![Robin powered Group Mappings](media/robin-provisioning-tutorial/robin-group-mapping.png)

11. Controleer de groepskenmerken die zijn gesynchroniseerd van Azure AD naar Robin in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **Eigenschappen matching** worden gebruikt om de groepen in Robin te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

    ![Robin aangedreven groepskenmerken](media/robin-provisioning-tutorial/robin-group-attribute-mapping.png)

12. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

13. Als u de Azure AD-inrichtingsservice voor Robin wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u aan Robin wilt inrichten door de gewenste waarden in **Bereik** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

15. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitsrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op Robin.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.



## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)

