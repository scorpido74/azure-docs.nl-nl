---
title: 'Zelf studie: de functie voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts vanuit Azure AD naar de OpenText Directory Services.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: ad55ba5f-c56c-4ed0-bdfd-163d2883ed80
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2020
ms.author: Zhchia
ms.openlocfilehash: c0858c2e5b1ada866d252e45113f0f90b73caaf8
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92428882"
---
# <a name="tutorial-configure-opentext-directory-services-for-automatic-user-provisioning"></a>Zelf studie: de functie voor het automatisch inrichten van gebruikers met een adreslijst service configureren

In deze zelf studie worden de stappen beschreven die u moet uitvoeren in zowel OpenText Directory Services als Azure Active Directory (Azure AD) voor het configureren van automatische gebruikers inrichting. Wanneer deze is geconfigureerd, worden gebruikers en groepen in azure AD automatisch ingericht en ongedaan gemaakt in OpenText Directory-Services met behulp van de Azure AD-inrichtings service. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers in OpenText Directory Services maken
> * Gebruikers in Open Text-adreslijst Services verwijderen wanneer ze geen toegang meer nodig hebben
> * Gebruikers kenmerken gesynchroniseerd blijven tussen Azure AD en OpenText Directory Services
> * Inrichtings groepen en groepslid maatschappen in OpenText Directory Services
> * [Eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/saas-apps/opentext-directory-services-tutorial) bij open text Directory Services (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-Tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Een gebruikers account in azure AD met [toestemming](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) voor het configureren van inrichting (bijvoorbeeld toepassings beheerder, Cloud toepassings beheerder, eigenaar van de toepassing of globale beheerder). 
* Een OTDS-installatie die toegankelijk is voor Azure AD.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en OpenText Directory Services](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-opentext-directory-services-to-support-provisioning-with-azure-ad"></a>Stap 2. Opdracht voor het configureren van OpenText Directory-Services voor ondersteuning van het inrichten met Azure AD

> [!NOTE]
> De onderstaande stappen zijn van toepassing op een service voor het gebruik van OpenText Directory Services. Ze zijn niet van toepassing op CoreShare-tenants met OpenText of openOT2.

1. Maak een exclusieve vertrouwelijke **OAuth-client**.
2. Stel een levens duur van een lang **toegangs token**in.

      ![Levens duur van toegangs token](media/open-text-directory-services-provisioning-tutorial/token-life.png)

3. Geef geen omleidings-Url's op. Ze zijn niet vereist. 
4. Met OTDS wordt het **client geheim**gegenereerd en weer gegeven. Sla de **client-id** en het **client geheim** op een veilige locatie op.

      ![Clientgeheim](media/open-text-directory-services-provisioning-tutorial/client-secret.png)

5. Maak een partitie voor de gebruikers en groepen die moeten worden gesynchroniseerd vanuit Azure AD.

      ![Partitie pagina](media/open-text-directory-services-provisioning-tutorial/partition.png)

6. Ken beheerders rechten toe aan de OAuth-client die u hebt gemaakt op de partitie die u wilt gebruiken voor de Azure AD-gebruikers en-groepen die worden gesynchroniseerd.    
      * Partitie-> acties-> beheerders bewerken

      ![Pagina beheerder](media/open-text-directory-services-provisioning-tutorial/administrator.png)

5. Een geheim token moet worden opgehaald en geconfigureerd in azure AD. Een HTTP-client toepassing kan hiervoor worden gebruikt. Hieronder vindt u stappen die u kunt ophalen met behulp van de Swagger API-toepassing die is opgenomen in OTDS.
      * Ga in een webbrowser naar {OTDS URL}/otdsws/oauth2
      * Ga naar/token en Klik rechtsboven op het vergrendelings pictogram. Voer de OAuth-client-ID en het geheime geheim in die u eerder hebt opgehaald als de gebruikers naam en het wacht woord. Klik op autoriseren.

      ![Autorisatie knop](media/open-text-directory-services-provisioning-tutorial/authorization.png)

6. Selecteer **client_credentials** voor de grant_type en klik op **uitvoeren**.

      ![Buton uitvoeren](media/open-text-directory-services-provisioning-tutorial/execute.png)

7. Het toegangs token in het antwoord moet worden gebruikt in het veld **geheime token** in azure AD.

      ![Toegangstoken](media/open-text-directory-services-provisioning-tutorial/access-token.png)

## <a name="step-3-add-opentext-directory-services-from-the-azure-ad-application-gallery"></a>Stap 3. In de Azure AD-toepassings galerie Services voor OpenText toevoegen

Voeg open-tekst adreslijst Services toe vanuit de Azure AD-toepassings galerie om het beheer van de inrichting te starten voor OpenText Directory-Services. Als u de map voor het gebruik van OpenText-adreslijst Services voor SSO al hebt ingesteld, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan Open Text-adreslijst Services, moet u een andere rol dan **standaard toegang**selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-opentext-directory-services"></a>Stap 5. Automatische gebruikers inrichting configureren voor gelaagde adreslijst Services 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in TestApp te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

### <a name="to-configure-automatic-user-provisioning-for-opentext-directory-services-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor OpenText Directory Services in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **OpenText Directory Services** in de lijst met toepassingen.

    ![De koppeling OpenText Directory Services in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Tabblad Inrichting](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Tabblad inrichten automatisch](common/provisioning-automatic.png)

5. Geef in het gedeelte **beheerders referenties** de URL van uw OpenText Directory Services-Tenant op
   * Niet-specifc Tenant-URL: {OTDS URL}/scim/{partitionName}
   * Specifieke Tenant-URL: {OTDS URL}/otdstenant/{tenantID}/scim/{partitionName}

6. Voer het geheime token in dat is opgehaald uit stap 2. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met open text Directory-Services. Als de verbinding mislukt, zorg er dan voor dat uw OpenText Directory Services-account beheerders machtigingen heeft en probeer het opnieuw.

      ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en schakel het selectievakje **Een e-mailmelding verzenden als een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met OpenText Directory Services**.

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar de OpenText Directory Services in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikers accounts in OpenText Directory Services te vergelijken voor bijwerk bewerkingen. Als u ervoor kiest om het [overeenkomende doel kenmerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)te wijzigen, moet u ervoor zorgen dat de open text Directory Services-API filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |userName|Tekenreeks|
   |actief|Booleaans|
   |displayName|Tekenreeks|
   |title|Tekenreeks|
   |emails[type eq "work"].value|Tekenreeks|
   |preferredLanguage|Tekenreeks|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |naam. opgemaakt|Tekenreeks|
   |adressen [type EQ "werk]. opgemaakt|Tekenreeks|
   |addresses[type eq "work"].streetAddress|Tekenreeks|
   |adressen [type EQ "werk]. locatie|Tekenreeks|
   |adressen [type EQ "werk]. regio|Tekenreeks|
   |addresses[type eq "work"].postalCode|Tekenreeks|
   |adressen [type EQ "werk]. land|Tekenreeks|
   |phoneNumbers[type eq "work"].value|Tekenreeks|
   |phoneNumbers[type eq "mobile"].value|Tekenreeks|
   |phoneNumbers[type eq "fax"].value|Tekenreeks|
   |externalId|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Verwijzing| 

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren naar gelaagde Directory Services**.

11. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar de OpenText Directory Services in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt voor het vergelijken van de groepen in de OpenText Directory Services voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

      |Kenmerk|Type|
      |---|---|
      |displayName|Tekenreeks|
      |externalId|Tekenreeks|
      |leden|Naslaginformatie|

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Als u de Azure AD-inrichtings service voor open text Directory Services wilt inschakelen, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor OpenText Directory-Services door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële cyclus duurt langer dan volgende cycli, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

1. Gebruik de [inrichtingslogboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangsbalk](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid
3. Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) voor meer informatie over quarantainestatussen.  

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../manage-apps/check-status-user-account-provisioning.md)
