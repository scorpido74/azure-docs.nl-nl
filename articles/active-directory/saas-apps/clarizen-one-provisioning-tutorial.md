---
title: 'Zelf studie: een Clarizen configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van de gebruikers accounts vanuit Azure AD op Clarizen.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d8021105-eb5b-4a20-8739-f02e0e22c147
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2020
ms.author: Zhchia
ms.openlocfilehash: 133e12450f01e14f2204810f0d24cd28b5f948f8
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92795240"
---
# <a name="tutorial-configure-clarizen-one-for-automatic-user-provisioning"></a>Zelf studie: een Clarizen configureren voor het automatisch inrichten van gebruikers

In deze zelf studie worden de stappen beschreven die u moet uitvoeren in zowel Clarizen één als Azure Active Directory (Azure AD) om het automatisch inrichten van gebruikers te configureren. Wanneer de configuratie is geconfigureerd, worden gebruikers en groepen door Azure AD automatisch voorzien van [een Clarizen](https://www.clarizen.com/) met behulp van de Azure AD-inrichtings service. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in Clarizen One
> * Gebruikers in Clarizen één verwijderen wanneer ze niet meer toegang nodig hebben
> * Gebruikers kenmerken gesynchroniseerd blijven tussen Azure AD en Clarizen
> * Inrichtings groepen en groepslid maatschappen in Clarizen One
> * [Eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/saas-apps/clarizen-tutorial) voor Clarizen één (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-Tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Een gebruikers account in azure AD met [toestemming](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) voor het configureren van inrichting (bijvoorbeeld toepassings beheerder, Cloud toepassings beheerder, eigenaar van de toepassing of globale beheerder). 
* Een gebruikers account in Clarizen One met **integratie** [machtigingen](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support)voor gebruiker en **Lite-beheerder** .

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Bepaal welke gegevens er moeten worden [toegewezen tussen Azure AD en Clarizen](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-clarizen-one-to-support-provisioning-with-azure-ad"></a>Stap 2. Clarizen configureren ter ondersteuning van het inrichten met Azure AD
1. Selecteer een van de vier volgende Tenant-Url's volgens uw Clarizen-omgeving en Data Center:
      * Amerikaanse productie Data Center: https://servicesapp2.clarizen.com/scim/v2
      * EU-productie Data Center: https://serviceseu1.clarizen.com/scim/v2
      * US sandbox Data Center: https://servicesapp.clarizentb.com/scim/v2
      * EU-sandbox Data Center: https://serviceseu.clarizentb.com/scim/v2

2. Genereer een [API-sleutel](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support). Deze waarde wordt ingevoerd in het veld **geheime token** op het tabblad inrichten van uw Clarizen één toepassing in de Azure Portal.

## <a name="step-3-add-clarizen-one-from-the-azure-ad-application-gallery"></a>Stap 3. Clarizen één toevoegen vanuit de Azure AD-toepassings galerie

Voeg Clarizen één toe vanuit de Azure AD-toepassings galerie om het inrichten van Provisioning naar Clarizen te starten. Als u Clarizen één voor EENMALIGe installatie hebt ingesteld, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan Clarizen één, moet u een andere rol dan **standaard toegang** selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-clarizen-one"></a>Stap 5. Automatische gebruikers inrichting configureren voor Clarizen één 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in TestApp te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

### <a name="to-configure-automatic-user-provisioning-for-clarizen-one-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor Clarizen één in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen** .

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Clarizen One** .

    ![De Clarizen één koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten** .

    ![Tabblad Inrichting](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch** .

    ![Tabblad inrichten automatisch](common/provisioning-automatic.png)

5. Geef in het gedeelte **beheerders referenties** de Clarizen een TENANT-URL en een geheim token op. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met Clarizen One. Als de verbinding mislukt, zorg er dan voor dat uw Clarizen één account beheerders machtigingen heeft en probeer het opnieuw.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en schakel het selectievakje **Een e-mailmelding verzenden als een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan** .

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren om één te Clarizen** .

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Clarizen, in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Clarizen één voor update bewerkingen. Als u ervoor kiest om het [overeenkomende doel kenmerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)te wijzigen, moet u ervoor zorgen dat de CLARIZEN één API het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |userName|Tekenreeks|
   |displayName|Tekenreeks|
   |actief|Booleaans|
   |title|Tekenreeks|
   |emails[type eq "work"].value|Tekenreeks|
   |e-mail berichten [type EQ "Home"]. waarde|Tekenreeks|
   |e-mail berichten [type EQ "Overig"]. waarde|Tekenreeks|
   |preferredLanguage|Tekenreeks|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |naam. opgemaakt|Tekenreeks|
   |name.honorificPrefix|Tekenreeks|
   |naam. honorificSuffix|Tekenreeks|
   |adressen [type EQ "other"]. Format|Tekenreeks|
   |adressen [type EQ "werk]. opgemaakt|Tekenreeks|
   |adressen [type EQ "werk]. land|Tekenreeks|
   |adressen [type EQ "werk]. regio|Tekenreeks|
   |adressen [type EQ "werk]. locatie|Tekenreeks|
   |addresses[type eq "work"].postalCode|Tekenreeks|
   |addresses[type eq "work"].streetAddress|Tekenreeks|
   |phoneNumbers[type eq "work"].value|Tekenreeks|
   |phoneNumbers[type eq "mobile"].value|Tekenreeks|
   |phoneNumbers[type eq "fax"].value|Tekenreeks|
   |phoneNumbers [type EQ "Home"]. waarde|Tekenreeks|
   |phoneNumbers [type EQ "other"]. waarde|Tekenreeks|
   |phoneNumbers [type EQ "semafoon"]. waarde|Tekenreeks|
   |externalId|Tekenreeks|
   |nickName|Tekenreeks|
   |landinstelling|Tekenreeks|
   |rollen [Primary EQ "True". type]|Tekenreeks|
   |rollen [Primary EQ "True". waarde]|Tekenreeks|
   |timezone|Tekenreeks|
   |userType|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Naslaginformatie|
   |urn: IETF: params: scim: schemas: extensie: Enter prise: 2.0: gebruiker: costCenter|Tekenreeks|
   |urn: IETF: params: scim: schemas: extensie: Enter prise: 2.0: gebruiker: deling|Tekenreeks|
   |urn: IETF: params: scim: schemas: extensie: Enter prise: 2.0: gebruiker: deling|Tekenreeks|

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren om één te Clarizen** .

11. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Clarizen, in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt voor het vergelijken van de groepen in Clarizen één voor update bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

      |Kenmerk|Type|
      |---|---|
      |displayName|Tekenreeks|
      |externalId|Tekenreeks|
      |leden|Naslaginformatie|

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Als u de Azure AD-inrichtings service voor Clarizen één wilt inschakelen, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor Clarizen door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan** .

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen** . De initiële cyclus duurt langer dan volgende cycli, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

1. Gebruik de [inrichtingslogboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangsbalk](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid
3. Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) voor meer informatie over quarantainestatussen.  

## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing
Als u een gebruiker toewijst aan de Clarizen één galerie-app, kiest **u alleen gebruikersrol** . De hieronder weer gegeven functies zijn ongeldig.

* Beheerder (beheerder)
* E-mail rapport gebruiker
* Externe gebruiker
* Financiële gebruiker
* Sociale gebruiker
* Super gebruiker
* Tijd & onkosten gebruiker



## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../manage-apps/check-status-user-account-provisioning.md)
