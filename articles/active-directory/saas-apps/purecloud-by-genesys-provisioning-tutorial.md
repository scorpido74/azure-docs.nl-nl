---
title: 'Zelfstudie: PureCloud by Genesys configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het automatisch inrichten en de-inrichten van gebruikersaccounts van Azure AD naar PureCloud door Genesys.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 5f04b88b-117e-40da-a15c-e3732b240d5d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2020
ms.author: Zhchia
ms.openlocfilehash: 119690b9046821ab538d879e1209c6ef77277370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77370679"
---
# <a name="tutorial-configure-purecloud-by-genesys-for-automatic-user-provisioning"></a>Zelfstudie: PureCloud by Genesys configureren voor automatische gebruikersinrichting

In deze zelfstudie worden de stappen beschreven die u moet uitvoeren in zowel PureCloud by Genesys als Azure Active Directory (Azure AD) om automatische gebruikersinrichting te configureren. Wanneer azure AD is geconfigureerd, worden gebruikers en groepen automatisch in- en uit-de-bepalingen naar [PureCloud door Genesys](https://www.genesys.com) voorzien met behulp van de Azure AD Provisioning-service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in PureCloud by Genesys
> * Verwijder gebruikers in PureCloud by Genesys wanneer ze geen toegang meer nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en PureCloud by Genesys
> * Provision groepen en groepslidmaatschappen in PureCloud door Genesys
> * [Single sign-on to](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial) PureCloud by Genesys (aanbevolen)

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* [Een Azure AD-tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Een gebruikersaccount in Azure AD met [toestemming](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) om provisioning te configureren (bijvoorbeeld toepassingsbeheerder, cloudtoepassingsbeheerder, toepassingseigenaar of globale beheerder). 
* Een PureCloud [organisatie](https://help.mypurecloud.com/?p=81984).
* Een gebruiker met [machtigingen](https://help.mypurecloud.com/?p=24360) om een Oauth-client te maken.

## <a name="step-1-plan-your-provisioning-deployment"></a>Step 1. Uw inrichtingsimplementatie plannen
1. Meer informatie over [hoe de inprovisioningservice werkt.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Bepaal wie in de ruimte voor [de inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)zal zijn .
3. Bepaal welke gegevens u wilt [toewijzen tussen Azure AD en PureCloud van Genesys.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-purecloud-by-genesys-to-support-provisioning-with-azure-ad"></a>Stap 2. PureCloud by Genesys configureren om provisioning met Azure AD te ondersteunen

1. Maak een [Oauth-client](https://help.mypurecloud.com/?p=188023) die is geconfigureerd in uw PureCloud-organisatie.
2. Genereer een token [met uw oauth-client.](https://developer.mypurecloud.com/api/rest/authorization/use-client-credentials.html)
3. Als u groepslidmaatschap automatisch wilt inrichten binnen PureCloud, moet u [Groepen in](https://help.mypurecloud.com/?p=52397) PureCloud maken met een identieke naam als de groep in Azure AD.

## <a name="step-3-add-purecloud-by-genesys-from-the-azure-ad-application-gallery"></a>Stap 3. PureCloud by Genesys toevoegen vanuit de Azure AD-toepassingsgalerie

Voeg PureCloud by Genesys toe vanuit de Azure AD-toepassingsgalerie om de inrichting voor PureCloud door Genesys te beheren. Als u eerder PureCloud by Genesys voor SSO hebt ingesteld, u dezelfde toepassing gebruiken. Het wordt echter aanbevolen om een aparte app te maken bij het testen van de integratie in eerste instantie. Meer informatie over het toevoegen van een toepassing uit de galerie [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Bepalen wie in het vermogen van de inrichting 

Met de Azure AD-inrichtingsservice u scopen die worden ingericht op basis van toewijzing aan de toepassing en of op basis van kenmerken van de gebruiker /groep. Als u ervoor kiest om het bereik te bepalen wie op basis van toewijzing aan uw app wordt toegewezen, u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om scope die zal worden ingericht uitsluitend op basis van attributen van de gebruiker of groep, u gebruik maken van een scoping filter zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)beschreven. 

* Wanneer u gebruikers en groepen door Genesys aan PureCloud toewijst, moet u een andere rol dan **Standaardtoegang**selecteren. Gebruikers met de functie Standaardtoegang zijn uitgesloten van inrichten en worden gemarkeerd als niet effectief gerechtigd in de inrichtingslogboeken. Als de enige rol die beschikbaar is in de toepassing de standaardtoegangsrol is, u [het toepassingsmanifest bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Begin klein. Test met een kleine set gebruikers en groepen voordat u naar iedereen uitrolt. Wanneer de inrichtingsruimte is ingesteld op toegewezen gebruikers en groepen, u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, u een [op kenmerken gebaseerd scopingfilter](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-purecloud-by-genesys"></a>Stap 5. Automatische gebruikersinrichting configureren voor PureCloud door Genesys 

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen in TestApp te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-purecloud-by-genesys-in-azure-ad"></a>Ga als het gaat om het configureren van automatische gebruikersinrichting voor PureCloud door Genesys in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **PureCloud by Genesys** in de lijst met toepassingen.

    ![De koppeling PureCloud by Genesys in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie **Beheerdersreferenties** uw PureCloud by Genesys API URL en Oauth Token in de velden **Tenant URL** en **Secret Token** in. De API-URL wordt `{{API Url}}/api/v2/scim/v2`gestructureerd als , met behulp van de API-URL voor uw PureCloud-regio vanuit het [PureCloud Developer Center.](https://developer.mypurecloud.com/api/rest/index.html) Klik **op Testverbinding** om ervoor te zorgen dat Azure AD verbinding kan maken met PureCloud by Genesys. Als de verbinding mislukt, moet u ervoor zorgen dat uw PureCloud by Genesys-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Provisioning](./media/purecloud-by-genesys-provisioning-tutorial/provisioning.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fout moet ontvangen en schakel het selectievakje **Een e-mailmelding verzenden in wanneer er een fout optreedt.**

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer Azure **Active Directory-gebruikers synchroniseren met PureCloud by Genesys**onder de sectie **Toewijzingen** .

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar PureCloud by Genesys in de sectie **Attribute-Mapping.** De kenmerken die zijn geselecteerd als **Eigenschappen matching** worden gebruikt om de gebruikersaccounts in PureCloud by Genesys te matchen voor updatebewerkingen. Als u ervoor kiest het [overeenkomende doelkenmerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)te wijzigen, moet u ervoor zorgen dat de PureCloud by Genesys API filteringgebruikers ondersteunt op basis van dat kenmerk. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

     |Kenmerk|Type|
     |---|---|
     |userName|Tekenreeks|
     |actief|Booleaans|
     |displayName|Tekenreeks|
     |e-mails[type eq "werk"].waarde|Tekenreeks|
     |titel|Tekenreeks|
     |phoneNumbers[type eq "mobile"].value|Tekenreeks|
     |phoneNumbers[type eq "werk"].waarde|Tekenreeks|
     |urn:ietf:params:scim:schema's:extension:enterprise:2.0:User:department|Tekenreeks|
     |urn:ietf:params:scim:schema's:extension:enterprise:2.0:User:manager|Naslaginformatie|

10. Selecteer azure **Active Directory-groepen synchroniseren met PureCloud by Genesys**onder de sectie **Toewijzingen** .

11. Bekijk de groepskenmerken die zijn gesynchroniseerd van Azure AD naar PureCloud by Genesys in de sectie **Attribute-Mapping.** De kenmerken die zijn geselecteerd als **Eigenschappen matching** worden gebruikt om de groepen in PureCloud by Genesys te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren. PureCloud by Genesys ondersteunt geen groepscreatie of verwijdering en ondersteunt alleen het bijwerken van groepen.

      |Kenmerk|Type|
      |---|---|
      |displayName|Tekenreeks|
      |extern id|Tekenreeks|
      |leden|Naslaginformatie|

12. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)

13. Als u de Azure AD-inrichtingsservice voor PureCloud by Genesys wilt inschakelen, wijzigt u de **ininrichtingsstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten op PureCloud by Genesys door de gewenste waarden in **Scope** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

15. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste cyclus duurt langer om uit te voeren dan de volgende cycli, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Zodra u de inrichting hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te controleren:

* Gebruik de [inrichtingslogboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) om te bepalen welke gebruikers met succes of zonder succes zijn ingericht
* Controleer de [voortgangsbalk](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtingscyclus te bekijken en hoe dicht deze bij voltooiing is
* Als de inrichtingsconfiguratie in een ongezonde status lijkt te zijn, wordt de toepassing in quarantaine geplaatst. Meer informatie over quarantainestaten [vindt u hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../manage-apps/check-status-user-account-provisioning.md)
