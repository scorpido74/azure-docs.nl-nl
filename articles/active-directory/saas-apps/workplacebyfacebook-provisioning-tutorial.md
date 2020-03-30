---
title: 'Zelfstudie: Werkplek door Facebook configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Workplace by Facebook configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22576be8dec021f0f18a6e2dda16891ce70d4f13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603203"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Zelfstudie: Workplace by Facebook configureren voor automatische gebruikersinrichting

In deze zelfstudie worden de stappen beschreven die u moet uitvoeren in zowel Workplace by Facebook als Azure Active Directory (Azure AD) om automatische gebruikersinrichting te configureren. Wanneer azure AD is geconfigureerd, worden gebruikers en groepen automatisch ingericht en de-bepalingen voor [Workplace door Facebook](https://work.workplace.com/) met behulp van de Azure AD Provisioning-service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="migrating-to-the-new-workplace-by-facebook-application"></a>Migreren naar de nieuwe Workplace by Facebook-applicatie
Als je een bestaande integratie met Workplace by Facebook hebt, zie je het onderstaande gedeelte over de komende wijzigingen. Als je Workplace by Facebook voor het eerst instelt, kun je deze sectie overslaan en naar de ondersteunde mogelijkheden gaan. 

#### <a name="whats-changing"></a>Wat verandert er?
* Wijzigingen aan de Azure AD-kant: de autorisatiemethode voor het inrichten van gebruikers in Workplace is van oudsher een langlevend geheim token. Binnenkort ziet u de autorisatiemethode gewijzigd in de OAuth autorisatie subsidie. 
* Wijzigingen aan de werkplekkant: voorheen was de Azure AD-app een aangepaste integratie in Workplace by Facebook. Nu ziet u Azure AD in de map Workplace-integraties als een toepassing van derden. 

 

#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>Wat moet ik doen om mijn bestaande aangepaste integratie te migreren naar de nieuwe toepassing?
Als u een bestaande Workplace-integratie hebt met een geldig token, **is er geen actie vereist.** We migreren automatisch klanten elke week naar de nieuwe applicatie. Dit gebeurt volledig achter de schermen. Als u niet wachten en handmatig naar de nieuwe toepassing wilt gaan, u vanuit de galerie een nieuw exemplaar van Workplace toevoegen en de inrichting opnieuw configureren. Alle nieuwe exemplaren van Workplace gebruiken automatisch de nieuwe toepassingsversie. 

 
Als uw Workplace-integratie in quarantaine is, moet u opnieuw een geldig token leveren om u te kunnen migreren. De sectie beheerdersreferenties wordt grijs weergegeven, maar u het volgende toevoegen (**? Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=true)** aan uw URL om referenties opnieuw op te slaan. 

https://portal.azure.com/?Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=true

#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Hoe weet ik of mijn aanvraag is gemigreerd? 
Wanneer uw toepassing wordt gemigreerd, wordt de banner in de autorisatiesectie over upcomming changes verwijderd en wordt het geheime tokenveld vervangen door een blauwe autocodeknop. 

#### <a name="the-admin-credentials-section-is-greyed-out-on-my-application-and-i-cant-save-why"></a>De admin referenties sectie is grijs op mijn applicatie en ik kan niet opslaan. Hoe komt dat?
We hebben de sectie beheerdersreferenties voor bestaande Workplace-klanten vergrendeld. Wanneer uw tenant is gemigreerd naar de nieuwe Workplace-toepassing, u de sectie beheerdersreferenties opnieuw bijwerken. Als u niet wachten, u de bovenstaande URL gebruiken om uw toepassing te bewerken. 

 
#### <a name="when-will-these-changes-happen"></a>Wanneer zullen deze veranderingen plaatsvinden?
Alle nieuwe exemplaren van Workplace maken al gebruik van de nieuwe integratie/ autorisatiemethode. Bestaande integraties zullen in mei geleidelijk worden gemigreerd. Het werkplekteam heeft een verlenging van de deadline van 28 februari tot 1 mei verstrekt. 

## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken op Workplace by Facebook
> * Verwijder gebruikers in Workplace by Facebook wanneer ze geen toegang meer nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en Workplace by Facebook
> * [Single sign-on to](https://docs.microsoft.com/azure/active-directory/saas-apps/workplacebyfacebook-tutorial) Workplace by Facebook (aanbevolen)

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* [Een Azure AD-tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Een gebruikersaccount in Azure AD met [toestemming](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) om inrichting te configureren (bijvoorbeeld toepassingsbeheerder, cloudtoepassingsbeheerder, toepassingseigenaar of globale beheerder)
* Een abonnement op Workplace by Facebook met één teken op ingeschakeld

> [!NOTE]
> Als u de stappen in deze zelfstudie wilt testen, is het raadzaam om niet de productieomgeving te gebruiken.

Volg deze aanbevelingen als u de stappen in deze zelfstudie wilt testen:

- Gebruik niet de productieomgeving, tenzij dit echt nodig is.
- Als u geen Azure AD-proefomgeving hebt, u [hier](https://azure.microsoft.com/pricing/free-trial/)een proefperiode van één maand krijgen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Step 1. Uw inrichtingsimplementatie plannen
1. Meer informatie over [hoe de inprovisioningservice werkt.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Bepaal wie in de ruimte voor [de inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)zal zijn .
3. Bepaal welke gegevens u wilt [toewijzen tussen Azure AD en Workplace by Facebook](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>Stap 2. Workplace by Facebook configureren om voorzieningen met Azure AD te ondersteunen

Voordat u de inrichtingsservice configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD de gebruikers vertegenwoordigen die toegang tot uw Workplace per Facebook-app nodig hebben. Eenmaal besloten, u deze gebruikers toewijzen aan uw Workplace door Facebook app door het volgen van de instructies hier:

*   Het wordt aanbevolen dat één Azure AD-gebruiker door Facebook aan Workplace wordt toegewezen om de inrichtingsconfiguratie te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

*   Wanneer je een gebruiker door Facebook aan Workplace toewijst, moet je een geldige gebruikersrol selecteren. De rol 'Standaardtoegang' werkt niet voor inrichten.

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>Stap 3. Workplace by Facebook toevoegen vanuit de Azure AD-toepassingsgalerie

Voeg Workplace by Facebook toe vanuit de Azure AD-toepassingsgalerie om de inrichting voor Workplace door Facebook te beheren. Als je Workplace by Facebook voor SSO eerder hebt ingesteld, kun je dezelfde toepassing gebruiken. Het wordt echter aanbevolen om een aparte app te maken bij het testen van de integratie in eerste instantie. Meer informatie over het toevoegen van een toepassing uit de galerie [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Bepalen wie in het vermogen van de inrichting 

Met de Azure AD-inrichtingsservice u scopen die worden ingericht op basis van toewijzing aan de toepassing en of op basis van kenmerken van de gebruiker /groep. Als u ervoor kiest om het bereik te bepalen wie op basis van toewijzing aan uw app wordt toegewezen, u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om scope die zal worden ingericht uitsluitend op basis van attributen van de gebruiker of groep, u gebruik maken van een scoping filter zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)beschreven. 

* Wanneer u gebruikers en groepen door Facebook aan Workplace toewijst, moet u een andere rol dan **Standaardtoegang**selecteren. Gebruikers met de functie Standaardtoegang zijn uitgesloten van inrichten en worden gemarkeerd als niet effectief gerechtigd in de inrichtingslogboeken. Als de enige rol die beschikbaar is in de toepassing de standaardtoegangsrol is, u [het toepassingsmanifest bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Begin klein. Test met een kleine set gebruikers en groepen voordat u naar iedereen uitrolt. Wanneer de inrichtingsruimte is ingesteld op toegewezen gebruikers en groepen, u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, u een [op kenmerken gebaseerd scopingfilter](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)opgeven. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer Workplace by **Facebook**in de lijst met toepassingen .

    ![De koppeling Workplace by Facebook in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Klik onder de sectie **Beheerdersreferenties** op **Autoriseren**. Je wordt doorgestuurd naar Workplace via de autorisatiepagina van Facebook. Voer je Workplace in op facebook gebruikersnaam en klik op de knop **Doorgaan.** Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Workplace by Facebook. Als de verbinding mislukt, moet u ervoor zorgen dat je Workplace by Facebook-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Provisioning](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![autoriseren](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fout moet ontvangen en schakel het selectievakje **Een e-mailmelding verzenden in wanneer er een fout optreedt.**

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer Azure **Active Directory-gebruikers synchroniseren met De werkplek door Facebook**in de sectie **Toewijzingen** .

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Workplace by Facebook in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Workplace by Facebook te matchen voor updatebewerkingen. Als u ervoor kiest het [overeenkomende doelkenmerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)te wijzigen, moet u ervoor zorgen dat de Workplace by Facebook API filteringgebruikers ondersteunt op basis van dat kenmerk. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |userName|Tekenreeks|
   |displayName|Tekenreeks|
   |actief|Booleaans|
   |titel|Booleaans|
   |e-mails[type eq "werk"].waarde|Tekenreeks|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |name.opgemaakt|Tekenreeks|
   |adressen[type eq "werk"].opgemaakt|Tekenreeks|
   |adressen[type eq "werk"].streetAddress|Tekenreeks|
   |adressen[type eq "werk"].plaats|Tekenreeks|
   |adressen[type eq "werk"].regio|Tekenreeks|
   |adressen[type eq "werk"].land|Tekenreeks|
   |adressen[type eq "werk"].postcode|Tekenreeks|
   |adressen[type eq "andere"].opgemaakt|Tekenreeks|
   |phoneNumbers[type eq "werk"].waarde|Tekenreeks|
   |phoneNumbers[type eq "mobile"].value|Tekenreeks|
   |phoneNumbers[type eq "fax"].value|Tekenreeks|
   |extern id|Tekenreeks|
   |voorkeurTaal|Tekenreeks|
   |urn:ietf:params:scim:schema's:extension:enterprise:2.0:User:manager|Tekenreeks|
   |urn:ietf:params:scim:schema's:extension:enterprise:2.0:User:department|Tekenreeks|

10. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)

11. Als u de Azure AD-inrichtingsservice voor Workplace door Facebook wilt inschakelen, wijzigt u de **inrichtingsstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten op Workplace by Facebook door de gewenste waarden in **Scope** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

13. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste cyclus duurt langer om uit te voeren dan de volgende cycli, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Zodra u de inrichting hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te controleren:

1. Gebruik de [inrichtingslogboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) om te bepalen welke gebruikers met succes of zonder succes zijn ingericht
2. Controleer de [voortgangsbalk](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtingscyclus te bekijken en hoe dicht deze bij voltooiing is
3. Als de inrichtingsconfiguratie in een ongezonde status lijkt te zijn, wordt de toepassing in quarantaine geplaatst. Meer informatie over quarantainestaten [vindt u hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing
*  Als u een gebruiker ziet die niet succesvol is gemaakt en er een auditlogboekgebeurtenis is met de code "1789003" betekent dit dat de gebruiker afkomstig is van een niet-geverifieerd domein.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../manage-apps/check-status-user-account-provisioning.md)
