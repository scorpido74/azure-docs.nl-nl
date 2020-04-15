---
title: 'Zelfstudie: Oracle Cloud Infrastructure Console configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het automatisch inrichten en de-provisionen van gebruikersaccounts van Azure AD naar Oracle Cloud Infrastructure Console.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: e22c8746-7638-4a0f-ae08-7db0c477cf52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: Zhchia
ms.openlocfilehash: 5aa33529a1957b6e7728b3a87bacf6bb91d987ae
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378949"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>Zelfstudie: Oracle Cloud Infrastructure Console configureren voor automatische gebruikersinrichting

In deze zelfstudie worden de stappen beschreven die u moet uitvoeren in zowel Oracle Cloud Infrastructure Console als Azure AD (Azure AD) om automatische gebruikersinrichting te configureren. Wanneer azure AD is geconfigureerd, worden gebruikers en groepen automatisch ingericht en de-bepalingen voor [Oracle Cloud Infrastructure Console](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) met behulp van de Azure AD Provisioning-service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in Oracle Cloud Infrastructure Console
> * Gebruikers verwijderen in Oracle Cloud Infrastructure Console wanneer ze geen toegang meer nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en Oracle Cloud Infrastructure Console
> * Groepen en groepslidmaatschappen in Oracle Cloud Infrastructure Console
> * [Eén aanmelding bij](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial) Oracle Cloud Infrastructure Console (aanbevolen)

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* [Een Azure AD-tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Een gebruikersaccount in Azure AD met [toestemming](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) om provisioning te configureren (bijvoorbeeld toepassingsbeheerder, cloudtoepassingsbeheerder, toepassingseigenaar of globale beheerder). 
* Een Oracle Cloud Infrastructure [Control-tenant](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton).
* Een gebruikersaccount in Oracle Cloud Infrastructure Control met beheerdersmachtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Step 1. Uw inrichtingsimplementatie plannen
1. Meer informatie over [hoe de inprovisioningservice werkt.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Bepaal wie in de ruimte voor [de inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)zal zijn .
3. Bepaal welke gegevens [u wilt toewijzen tussen Azure AD en Oracle Cloud Infrastructure Console.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>Stap 2. Oracle Cloud Infrastructure Console configureren om voorzieningen met Azure AD te ondersteunen

1. Meld u aan bij de beheerdersportal van Oracle Cloud Infrastructure Console. Navigeer linksboven in het scherm naar **Identity > Federation**.

    ![Oracle-beheerder](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. Klik op de URL die op de pagina naast Oracle Identity Cloud Service Console wordt weergegeven.

    ![Oracle-URL](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. Klik op **Identiteitsprovider toevoegen** om een nieuwe identiteitsprovider te maken. Sla de IdP-id op om te worden gebruikt als onderdeel van de URL van de tenant. Klik op het pluspictogram naast het tabblad **Toepassingen** om een OAuth-client te maken en IDCS-approle voor identiteitsdomeinbeheerder toe te kennen.

    ![Pictogram Oracle Cloud](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. Volg de screenshots hieronder om uw toepassing te configureren. Zodra de configuratie is gedaan klik op **Opslaan**.

    ![Oracle-configuratie](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![Oracle Token-uitgiftebeleid](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. Vouw onder het tabblad Configuraties van uw toepassing de optie **Algemene informatie** uit om de client-id en het clientgeheim op te halen.

    ![Oracle-tokengeneratie](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. Als u een geheim token Base64 wilt genereren, codeert u de client-id en het clientgeheim in de indeling **client-ID:Client Secret**. Sla het geheime token op. Deze waarde wordt ingevoerd in het veld **Geheim token** op het tabblad Inrichting van uw Oracle Cloud Infrastructure Console-toepassing in de Azure-portal.

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>Stap 3. Oracle Cloud Infrastructure Console toevoegen vanuit de Azure AD-toepassingsgalerie

Voeg Oracle Cloud Infrastructure Console toe vanuit de Azure AD-toepassingsgalerie om de inrichting aan Oracle Cloud Infrastructure Console te beheren. Als u eerder Oracle Cloud Infrastructure Console voor SSO hebt ingesteld, u dezelfde toepassing gebruiken. Het wordt echter aanbevolen om een aparte app te maken bij het testen van de integratie in eerste instantie. Meer informatie over het toevoegen van een toepassing uit de galerie [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Bepalen wie in het vermogen van de inrichting 

Met de Azure AD-inrichtingsservice u scopen die worden ingericht op basis van toewijzing aan de toepassing en of op basis van kenmerken van de gebruiker /groep. Als u ervoor kiest om het bereik te bepalen wie op basis van toewijzing aan uw app wordt toegewezen, u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om scope die zal worden ingericht uitsluitend op basis van attributen van de gebruiker of groep, u gebruik maken van een scoping filter zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)beschreven. 

* Wanneer u gebruikers en groepen toewijs aan Oracle Cloud Infrastructure Console, moet u een andere rol dan **Standaardtoegang**selecteren. Gebruikers met de functie Standaardtoegang zijn uitgesloten van inrichten en worden gemarkeerd als niet effectief gerechtigd in de inrichtingslogboeken. Als de enige rol die beschikbaar is in de toepassing de standaardtoegangsrol is, u [het toepassingsmanifest bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Begin klein. Test met een kleine set gebruikers en groepen voordat u naar iedereen uitrolt. Wanneer de inrichtingsruimte is ingesteld op toegewezen gebruikers en groepen, u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, u een [op kenmerken gebaseerd scopingfilter](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>Stap 5. Automatische gebruikersvoorziening configureren voor Oracle Cloud Infrastructure Console 

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen in TestApp te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>Ga als het gaat om het configureren van automatische gebruikersinrichting voor Oracle Cloud Infrastructure Console in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer Oracle Cloud **Infrastructure Console**in de lijst met toepassingen .

    ![De Oracle Cloud Infrastructure Console-koppeling in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie **Beheerdersreferenties** de URL `https://<IdP ID>.identity.oraclecloud.com/admin/v1` van de **tenant** in de indeling in. Bijvoorbeeld `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`. Voer de geheime tokenwaarde in die eerder in **Secret Token**is opgehaald. Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Oracle Cloud Infrastructure Console. Als de verbinding mislukt, moet u ervoor zorgen dat uw Oracle Cloud Infrastructure Console-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Provisioning](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fout moet ontvangen en schakel het selectievakje **Een e-mailmelding verzenden in wanneer er een fout optreedt.**

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer Azure **Active Directory-gebruikers synchroniseren met Oracle Cloud Infrastructure Console**in de sectie **Toewijzingen** .

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Oracle Cloud Infrastructure Console in de sectie **Attribute-Mapping.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Oracle Cloud Infrastructure Console te matchen voor updatebewerkingen. Als u ervoor kiest het [overeenkomende doelkenmerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)te wijzigen, moet u ervoor zorgen dat de Oracle Cloud Infrastructure Console API filteringgebruikers ondersteunt op basis van dat kenmerk. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

      |Kenmerk|Type|
      |---|---|
      |displayName|Tekenreeks|
      |userName|Tekenreeks|
      |actief|Booleaans|
      |titel|Tekenreeks|
      |e-mails[type eq "werk"].waarde|Tekenreeks|
      |voorkeurTaal|Tekenreeks|
      |name.givenName|Tekenreeks|
      |name.familyName|Tekenreeks|
      |adressen[type eq "werk"].opgemaakt|Tekenreeks|
      |adressen[type eq "werk"].plaats|Tekenreeks|
      |adressen[type eq "werk"].regio|Tekenreeks|
      |adressen[type eq "werk"].postcode|Tekenreeks|
      |adressen[type eq "werk"].land|Tekenreeks|
      |adressen[type eq "werk"].streetAddress|Tekenreeks|
      |urn:ietf:params:scim:schema's:extension:enterprise:2.0:User:employeeNumber|Tekenreeks|
      |urn:ietf:params:scim:schema's:extension:enterprise:2.0:User:department|Tekenreeks|
      |urn:ietf:params:scim:schema's:extension:enterprise:2.0:User:costCenter|Tekenreeks|
      |urn:ietf:params:scim:schema's:extension:enterprise:2.0:User:division|Tekenreeks|
      |urn:ietf:params:scim:schema's:extension:enterprise:2.0:User:manager|Naslaginformatie|
      |urn:ietf:params:scim:schema's:extension:enterprise:2.0:User:organization|Tekenreeks|
      |urn:ietf:params:scim:schema's:oracle:idcs:extension:user:User:bypassNotification|Booleaans|
      |urn:ietf:params:scim:schema's:oracle:idcs:extension:user:User:isFederatedUser|Booleaans|

10. Selecteer Azure **Active Directory-groepen synchroniseren met Oracle Cloud Infrastructure Console**in de sectie **Toewijzingen** .

11. Controleer de groepskenmerken die zijn gesynchroniseerd van Azure AD naar Oracle Cloud Infrastructure Console in de sectie **Attribute-Mapping.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de groepen in Oracle Cloud Infrastructure Console te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

      |Kenmerk|Type|
      |---|---|
      |displayName|Tekenreeks|
      |extern id|Tekenreeks|
      |leden|Naslaginformatie|

12. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)

13. Als u de Azure AD-inrichtingsservice voor Oracle Cloud Infrastructure Console wilt inschakelen, wijzigt u de **inrichtingsstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten op Oracle Cloud Infrastructure Console door de gewenste waarden in **Scope** te kiezen in de sectie **Instellingen.**

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
