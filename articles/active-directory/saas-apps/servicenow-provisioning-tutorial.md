---
title: 'Zelfstudie: ServiceNow configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het automatisch inrichten en de-inrichten van gebruikersaccounts van Azure AD naar ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: 9e93d4b3f1880f2ac56a32a7b85aa6801fb7c14e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205097"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Zelfstudie: ServiceNow configureren voor automatische gebruikersinrichting

In deze zelfstudie worden de stappen beschreven die u moet uitvoeren in zowel ServiceNow als Azure Active Directory (Azure AD) om automatische gebruikersinrichting te configureren. Wanneer azure AD is geconfigureerd, worden gebruikers en groepen automatisch in- en uitvoorzieningen van Azure AD [voorzien](https://www.servicenow.com/) met behulp van de Azure AD Provisioning-service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in ServiceNow
> * Gebruikers verwijderen in ServiceNow wanneer ze geen toegang meer nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en ServiceNow
> * Voorzieningsgroepen en groepslidmaatschappen in ServiceNow
> * [Eenmalige aanmelding bij](servicenow-tutorial.md) ServiceNow (aanbevolen)

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* [Een Azure AD-tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Een gebruikersaccount in Azure AD met [toestemming](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) om provisioning te configureren (bijvoorbeeld toepassingsbeheerder, cloudtoepassingsbeheerder, toepassingseigenaar of globale beheerder). 
* Een [ServiceNow-exemplaar](https://www.servicenow.com/) van Calgary of hoger
* Een [ServiceNow Express-exemplaar](https://www.servicenow.com/) van Helsinki of hoger
* Een gebruikersaccount in ServiceNow met de beheerdersrol

## <a name="step-1-plan-your-provisioning-deployment"></a>Step 1. Uw inrichtingsimplementatie plannen
1. Meer informatie over [hoe de inprovisioningservice werkt.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Bepaal wie in de ruimte voor [de inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)zal zijn .
3. Bepaal welke gegevens u wilt [toewijzen tussen Azure AD en ServiceNow](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Stap 2. ServiceNow configureren om de inrichting met Azure AD te ondersteunen

1. Identificeer de naam van uw Instantie ServiceNow. U de instantienaam vinden in de URL die u gebruikt om toegang te krijgen tot ServiceNow. In het onderstaande voorbeeld is de instantienaam dev35214.

![Instantie ServiceNow](media/servicenow-provisioning-tutorial/servicenow_instance.png)

    
2. Referenties verkrijgen voor een beheerder in ServiceNow. Navigeer naar het gebruikersprofiel in ServiceNow en controleer of de gebruiker de beheerdersrol heeft. 

![ServiceNow-beheerrol](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)

## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Stap 3. ServiceNow toevoegen vanuit de Azure AD-toepassingsgalerie

Voeg ServiceNow toe vanuit de Azure AD-toepassingsgalerie om de inrichting aan ServiceNow te beheren. Als u voorheen ServiceNow voor SSO hebt ingesteld, u dezelfde toepassing gebruiken. Het wordt echter aanbevolen om een aparte app te maken bij het testen van de integratie in eerste instantie. Meer informatie over het toevoegen van een toepassing uit de galerie [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Bepalen wie in het vermogen van de inrichting 

Met de Azure AD-inrichtingsservice u scopen die worden ingericht op basis van toewijzing aan de toepassing en of op basis van kenmerken van de gebruiker /groep. Als u ervoor kiest om het bereik te bepalen wie op basis van toewijzing aan uw app wordt toegewezen, u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om scope die zal worden ingericht uitsluitend op basis van attributen van de gebruiker of groep, u gebruik maken van een scoping filter zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)beschreven. 

* Wanneer u gebruikers en groepen aan ServiceNow toewijst, moet u een andere rol dan **Standaardtoegang**selecteren. Gebruikers met de functie Standaardtoegang zijn uitgesloten van inrichten en worden gemarkeerd als niet effectief gerechtigd in de inrichtingslogboeken. Als de enige rol die beschikbaar is in de toepassing de standaardtoegangsrol is, u [het toepassingsmanifest bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Begin klein. Test met een kleine set gebruikers en groepen voordat u naar iedereen uitrolt. Wanneer de inrichtingsruimte is ingesteld op toegewezen gebruikers en groepen, u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, u een [op kenmerken gebaseerd scopingfilter](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Stap 5. Automatische gebruikersvoorziening configureren voor ServiceNow 

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen in TestApp te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>Ga als het gaat om het configureren van automatische gebruikersinrichting voor ServiceNow in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **ServiceNow** in de lijst met toepassingen.

    ![De koppeling ServiceNow in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie **Beheerdersreferenties** uw servicenow-beheerdersreferenties en gebruikersnaam in. Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met ServiceNow. Als de verbinding mislukt, moet u ervoor zorgen dat uw ServiceNow-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Provisioning](./media/servicenow-provisioning-tutorial/provisioning.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fout moet ontvangen en schakel het selectievakje **Een e-mailmelding verzenden in wanneer er een fout optreedt.**

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer Azure **Active Directory-gebruikers synchroniseren met ServiceNow**onder de sectie **Toewijzingen** .

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar ServiceNow in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in ServiceNow te matchen voor updatebewerkingen. Als u ervoor kiest het [overeenkomende doelkenmerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)te wijzigen, moet u ervoor zorgen dat de ServiceNow-API filteringgebruikers ondersteunt op basis van dat kenmerk. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

10. Selecteer Azure **Active Directory-groepen synchroniseren met ServiceNow**onder de sectie **Toewijzingen** .

11. Controleer de groepskenmerken die zijn gesynchroniseerd van Azure AD naar ServiceNow in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **Eigenschappen matching** worden gebruikt om de groepen in ServiceNow te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

12. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

13. Als u de Azure AD-inrichtingsservice voor ServiceNow wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u aan ServiceNow wilt inrichten door de gewenste waarden in **Bereik** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

15. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste cyclus duurt langer om uit te voeren dan de volgende cycli, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Zodra u de inrichting hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te controleren:

1. Gebruik de [inrichtingslogboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) om te bepalen welke gebruikers met succes of zonder succes zijn ingericht
2. Controleer de [voortgangsbalk](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtingscyclus te bekijken en hoe dicht deze bij voltooiing is
3. Als de inrichtingsconfiguratie in een ongezonde status lijkt te zijn, wordt de toepassing in quarantaine geplaatst. Meer informatie over quarantainestaten [vindt u hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing
* **Ongeldige LookupReference:** Bij het inrichten van bepaalde kenmerken, zoals Afdeling en Locatie in ServiceNow, moeten de waarden al bestaan in een referentietabel in ServiceNow. U bijvoorbeeld twee locaties (Seattle, Los Angeles) en drie afdelingen (Verkoop, Financiën, Marketing) in de tabel **naamtabel invoegen** in ServiceNow hebben. Als u probeert een gebruiker in te richten waar zijn afdeling "Verkoop" is en locatie "Seattle" is, wordt hij met succes ingericht. Als u een gebruiker probeert in te richten bij afdeling 'Verkoop' en locatie "LA" wordt de gebruiker niet in- en ingericht. De locatie LA moet worden toegevoegd aan de referentietabel in ServiceNow of het gebruikerskenmerk in Azure AD moet worden bijgewerkt om de indeling in ServiceNow te matchen. 
* **EntryJoiningPropertyValueIsMissing:** Controleer uw [kenmerktoewijzingen](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) om het overeenkomende kenmerk te identificeren. Deze waarde moet aanwezig zijn op de gebruiker of groep die u probeert in te richten. 
* Controleer de [SOAP-API van ServiceNow](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) om inzicht te krijgen in eventuele vereisten of beperkingen (bijvoorbeeld opmaak om landcode voor een gebruiker op te geven)
* Voor sommige ServiceNow-implementaties zijn IP-bereiken voor de Azure AD-inrichtingsservice vereist. De gereserveerde IP-bereiken voor de Azure AD-inrichtingsservice zijn [hier](https://www.microsoft.com/download/details.aspx?id=56519) te vinden onder 'AzureActiveDirectoryDomainServices'.
* Inrichtingsaanvragen worden standaard verzonden naar https://{your-instance-name}.service-now.com/{table-name} . Als u een aangepaste tenant-URL nodig hebt, u de volledige URL in het veld instantienaam opgeven.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)
