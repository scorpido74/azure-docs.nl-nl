---
title: 'Zelfstudie: Nieuwe relieken configureren(beperkte release) configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het automatisch inrichten en de-provisionen van gebruikersaccounts van Azure AD naar New Relic (Limited Release).
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 7fd0d976-4f70-4ce5-992e-3ea4ed8e5d60
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2020
ms.author: Zhchia
ms.openlocfilehash: 363b50a4835ead52b3a3a85978d4ea8210add7b2
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727344"
---
# <a name="tutorial-configure-new-reliclimited-release-for-automatic-user-provisioning"></a>Zelfstudie: Nieuwe Relic(Limited Release) configureren voor automatische gebruikersinrichting

In deze zelfstudie worden de stappen beschreven die u moet uitvoeren in zowel New Relic(Limited Release) als Azure Active Directory (Azure AD) om automatische gebruikersinrichting te configureren. Wanneer Azure AD is geconfigureerd, worden gebruikers en groepen automatisch ingericht en de-bepalingen naar [New Relic](https://newrelic.com/) uitgevoerd met behulp van de Azure AD Provisioning-service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in New Relic(Limited Release)
> * Verwijder gebruikers in New Relic (Limited Release) wanneer ze geen toegang meer nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en New Relic(Limited Release)
> * Groepen en groepslidmaatschappen in New Relic (Limited Release)
> * [Single sign-on to](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial) New Relic(Limited Release) (aanbevolen)

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* [Een Azure AD-tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Een gebruikersaccount in Azure AD met [toestemming](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) om provisioning te configureren (bijvoorbeeld toepassingsbeheerder, cloudtoepassingsbeheerder, toepassingseigenaar of globale beheerder). 
* Een of meer accounts bij New Relic (Limited Release) waar toe uw gebruikers toegang moeten hebben. 

## <a name="step-1-plan-your-provisioning-deployment"></a>Step 1. Uw inrichtingsimplementatie plannen
1. Meer informatie over [hoe de inprovisioningservice werkt.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Bepaal wie in de ruimte voor [de inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)zal zijn .
3. Bepaal welke gegevens u wilt [toewijzen tussen Azure AD en New Relic(Limited Release).](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-new-reliclimited-release-to-support-provisioning-with-azure-ad"></a>Stap 2. Nieuwe relieken (beperkte release) configureren om voorzieningen met Azure AD te ondersteunen

Werk samen met uw accountvertegenwoordiger of krijg ondersteuning bij support.newrelic.com om SCIM en SSO voor uw organisatie te configureren. U moet uw accountvertegenwoordiger voorzien van:

- Uw organisatienaam
- Lijst met nieuwe relic-account-geïdentificeerde gegevens die moeten worden geassocieerd met de organisatie

Met deze informatie maakt uw accountvertegenwoordiger een organisatierecord voor u in ons nieuwe systeem en koppelt u uw accounts aan de organisatie.

Uw accountvertegenwoordiger verstrekt u de volgende informatie die u nodig hebt om de Nieuwe Relic SCIM/SSO-applicatie voor uw identiteitsprovider te configureren:

- SCIM-eindpunt (URL van tenant)
- SCIM-token aan toonder (Secret Token)

Het SCIM-token aan toonder maakt het mogelijk om uw gebruikers bij New Relic te voorzien, dus houd de waarde beveiligd. Uw accountvertegenwoordiger zal het SCIM-token aan touw op een veilige manier overdragen.

## <a name="step-3-add-new-reliclimited-release-from-the-azure-ad-application-gallery"></a>Stap 3. Nieuwe relieken toevoegen (beperkte release) vanuit de Azure AD-toepassingsgalerie

Voeg Nieuwe Relic(Limited Release) toe vanuit de Azure AD-toepassingsgalerie om de inrichting te beheren aan New Relic (Limited Release). Als u eerder New Relic(Limited Release) voor SSO hebt ingesteld, u dezelfde toepassing gebruiken. Het wordt echter aanbevolen om een aparte app te maken bij het testen van de integratie in eerste instantie. Meer informatie over het toevoegen van een toepassing uit de galerie [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Bepalen wie in het vermogen van de inrichting 

Met de Azure AD-inrichtingsservice u scopen die worden ingericht op basis van toewijzing aan de toepassing en of op basis van kenmerken van de gebruiker /groep. Als u ervoor kiest om het bereik te bepalen wie op basis van toewijzing aan uw app wordt toegewezen, u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om scope die zal worden ingericht uitsluitend op basis van attributen van de gebruiker of groep, u gebruik maken van een scoping filter zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)beschreven. 

* Wanneer u gebruikers en groepen toewijs aan New Relic(Limited Release), moet u een andere rol dan **Standaardtoegang**selecteren. Gebruikers met de functie Standaardtoegang zijn uitgesloten van inrichten en worden gemarkeerd als niet effectief gerechtigd in de inrichtingslogboeken. Als de enige rol die beschikbaar is in de toepassing de standaardtoegangsrol is, u [het toepassingsmanifest bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Begin klein. Test met een kleine set gebruikers en groepen voordat u naar iedereen uitrolt. Wanneer de inrichtingsruimte is ingesteld op toegewezen gebruikers en groepen, u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, u een [op kenmerken gebaseerd scopingfilter](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-new-reliclimited-release"></a>Stap 5. Automatische gebruikersvoorziening configureren voor Nieuwe Relic(Beperkte release) 

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen in TestApp te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-new-reliclimited-release-in-azure-ad"></a>Ga als lid van het nieuwe relaatsom van nieuwe gebruikers (Limited Release) in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer In de lijst met toepassingen de optie **Nieuwe relikwie(beperkte release)**.

    ![De nieuwe relic-link in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie `https://scim-provisioning.service.newrelic.com/scim/v2` **Beheerdersreferenties** invoer in Tenant-URL in. Voer de scim-verificatietokenwaarde in die eerder in **Secret Token**is opgehaald. Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Nieuw Relic. Als de verbinding mislukt, moet u ervoor zorgen dat uw Nieuwe Relic-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Provisioning](./media/new-relic-limited-release-provisioning-tutorial/provisioning.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fout moet ontvangen en schakel het selectievakje **Een e-mailmelding verzenden in wanneer er een fout optreedt.**

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer Azure **Active Directory-gebruikers synchroniseren met nieuwe redeining (Limited Release)** onder de sectie **Toewijzingen.**

9. Bekijk de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar New Relic (Limited Release) in de sectie **Attribute-Mapping.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in New Relic (Limited Release) te matchen voor updatebewerkingen. Als u ervoor kiest het [overeenkomende doelkenmerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)te wijzigen, moet u ervoor zorgen dat de API Voor nieuwe relic(limited release) filteringgebruikers ondersteunt op basis van dat kenmerk. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |userName|Tekenreeks|
   |extern id|Tekenreeks|
   |actief|Booleaans|
   |e-mails[type eq "werk"].waarde|Tekenreeks|
   |name.givenName|Tekenreeks|
   |name.opgemaakt|Tekenreeks|
   |Uurzonee|Tekenreeks|

10. Selecteer Azure **Active Directory-groepen synchroniseren met nieuwe redeining (Limited Release)** onder de sectie **Toewijzingen.**

11. Bekijk de groepskenmerken die zijn gesynchroniseerd van Azure AD naar New Relic (Limited Release) in de sectie **Attribute-Mapping.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de groepen in New Relic (Limited Release) te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

      |Kenmerk|Type|
      |---|---|
      |displayName|Tekenreeks|
      |extern id|Tekenreeks|
      |leden|Naslaginformatie|

12. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)

13. Als u de Azure AD-inrichtingsservice voor Nieuwe redeining(Beperkte release) wilt inschakelen, wijzigt u de **ininrichtingsstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor Nieuwe Relic(Limited Release) door de gewenste waarden in **Scope** te kiezen in de sectie **Instellingen.**

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
