---
title: 'Zelfstudie: RingCentral configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het automatisch inrichten en de-inrichten van gebruikersaccounts van Azure AD naar RingCentral.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 976ac071-fa7e-4f31-aed1-d174942860d4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2019
ms.author: Zhchia
ms.openlocfilehash: 052223f69fc1c1d59ec5f1bcbeb3746ef7122c86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087058"
---
# <a name="tutorial-configure-ringcentral-for-automatic-user-provisioning"></a>Zelfstudie: RingCentral configureren voor automatische gebruikersinrichting

In deze zelfstudie worden de stappen beschreven die u moet uitvoeren in zowel RingCentral als Azure Active Directory (Azure AD) om automatische gebruikersinrichting te configureren. Wanneer azure AD is geconfigureerd, worden gebruikers en groepen automatisch in- en uitvoorzieningen van Azure AD [voorzien](https://www.ringcentral.com/office/plansandpricing.html) met behulp van de Azure AD Provisioning-service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in RingCentral
> * Gebruikers verwijderen in RingCentral wanneer ze geen toegang meer nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en RingCentral
> * [Eenmalige aanmelding bij](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial) RingCentral (aanbevolen)

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* [Een Azure AD-tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Een gebruikersaccount in Azure AD met [toestemming](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) om provisioning te configureren (bijvoorbeeld toepassingsbeheerder, cloudtoepassingsbeheerder, toepassingseigenaar of globale beheerder). 
* [Een RingCentral huurder](https://www.ringcentral.com/office/plansandpricing.html)
* Een gebruikersaccount in RingCentral met beheerdersmachtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Step 1. Uw inrichtingsimplementatie plannen
1. Meer informatie over [hoe de inprovisioningservice werkt.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Bepaal wie in de ruimte voor [de inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)zal zijn .
3. Bepaal welke gegevens u wilt [toewijzen tussen Azure AD en RingCentral](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-ringcentral-to-support-provisioning-with-azure-ad"></a>Stap 2. RingCentral configureren om de inrichting met Azure AD te ondersteunen

1. Meld u aan bij uw [RingCentral-beheerconsole](https://login.ringcentral.com/sw.html). Navigeer naar **Tools > Directory-integratie**.

    ![RingCentrale-beheerdersconsole](media/ringcentral-provisioning-tutorial/admin.png)

2.  Kies **SCIM** onder **Directoryprovider selecteren**. (In de toekomst is er een optie genaamd Azure Active Directory). Klik **op SCIM-service inschakelen**.

    ![RingCentral Voeg SCIM toe](media/ringcentral-provisioning-tutorial/scim.png)

3.  Neem contact op matthew.hunt@ringcentral.com met het ondersteuningsteam van RingCentral voor een **SCIM-verificatietoken**. Deze waarde wordt ingevoerd in het veld Geheim token op het tabblad Provisioning van uw RingCentral-toepassing in de Azure-portal.

> [!NOTE]
> Als u licenties aan gebruikers wilt toewijzen, raadpleegt u [hier](https://support.ringcentral.com/s/article/5-10-Adding-Extensions-via-Web?language)de videokoppeling.

## <a name="step-3-add-ringcentral-from-the-azure-ad-application-gallery"></a>Stap 3. RingCentral toevoegen vanuit de Azure AD-toepassingsgalerie

Voeg RingCentral toe vanuit de Azure AD-toepassingsgalerie om de inrichting aan RingCentral te beheren. Als u RingCentral voor SSO eerder hebt ingesteld, u dezelfde toepassing gebruiken. Het wordt echter aanbevolen om een aparte app te maken bij het testen van de integratie in eerste instantie. Meer informatie over het toevoegen van een toepassing uit de galerie [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Bepalen wie in het vermogen van de inrichting 

Met de Azure AD-inrichtingsservice u scopen die worden ingericht op basis van toewijzing aan de toepassing en of op basis van kenmerken van de gebruiker /groep. Als u ervoor kiest om het bereik te bepalen wie op basis van toewijzing aan uw app wordt toegewezen, u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om scope die zal worden ingericht uitsluitend op basis van attributen van de gebruiker of groep, u gebruik maken van een scoping filter zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)beschreven. 

* Wanneer u gebruikers en groepen aan RingCentral toewijst, moet u een andere rol dan **Standaardtoegang**selecteren. Gebruikers met de functie Standaardtoegang zijn uitgesloten van inrichten en worden gemarkeerd als niet effectief gerechtigd in de inrichtingslogboeken. Als de enige rol die beschikbaar is in de toepassing de standaardtoegangsrol is, u [het toepassingsmanifest bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Begin klein. Test met een kleine set gebruikers en groepen voordat u naar iedereen uitrolt. Wanneer de inrichtingsruimte is ingesteld op toegewezen gebruikers en groepen, u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, u een [op kenmerken gebaseerd scopingfilter](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-ringcentral"></a>Stap 5. Automatische gebruikersvoorziening configureren voor RingCentral 

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen in TestApp te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-ringcentral-in-azure-ad"></a>Ga als een te meer met de automatische gebruikersvoorziening voor RingCentral in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **RingCentral**in de lijst met toepassingen .

    ![De koppeling RingCentral in de lijst Toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie `https://platform.ringcentral.com/scim/v2` **Beheerdersreferenties** invoer in **Tenant-URL**in . Voer de waarde van de **SCIM-verificatietoken** in die eerder in **Secret Token**is opgehaald. Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met RingCentral. Als de verbinding mislukt, moet u ervoor zorgen dat uw RingCentral-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Tabblad Inrichten](./media/ringcentral-provisioning-tutorial/provisioning.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fout moet ontvangen en schakel het selectievakje **Een e-mailmelding verzenden in wanneer er een fout optreedt.**

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer Azure **Active Directory-gebruikers synchroniseren met RingCentral**onder de sectie **Toewijzingen** .

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar RingCentral in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in RingCentral te matchen voor updatebewerkingen. Als u ervoor kiest het [overeenkomende doelkenmerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)te wijzigen, moet u ervoor zorgen dat de RingCentral-API filteringgebruikers ondersteunt op basis van dat kenmerk. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |userName|Tekenreeks|
   |extern id|Tekenreeks|
   |actief|Booleaans|
   |displayName|Tekenreeks|
   |titel|Tekenreeks|
   |e-mails[type eq "werk"].waarde|Tekenreeks|
   |adressen[type eq "werk"].land|Tekenreeks|
   |adressen[type eq "werk"].regio|Tekenreeks|
   |adressen[type eq "werk"].plaats|Tekenreeks|
   |adressen[type eq "werk"].postcode|Tekenreeks|
   |adressen[type eq "werk"].streetAddress|Tekenreeks|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |phoneNumbers[type eq "mobile"].value|Tekenreeks|
   |urn:ietf:params:scim:schema's:extension:enterprise:2.0:User:department|Tekenreeks|
   |urn:ietf:params:scim:schema's:extension:enterprise:2.0:User:manager|Naslaginformatie|

10. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)

11. Als u de Azure AD-inrichtingsservice voor RingCentral wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u aan RingCentral wilt inrichten door de gewenste waarden in **Bereik** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

13. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste cyclus duurt langer om uit te voeren dan de volgende cycli, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Zodra u de inrichting hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te controleren:

1. Gebruik de [inrichtingslogboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) om te bepalen welke gebruikers met succes of zonder succes zijn ingericht
2. Controleer de [voortgangsbalk](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtingscyclus te bekijken en hoe dicht deze bij voltooiing is
3. Als de inrichtingsconfiguratie in een ongezonde status lijkt te zijn, wordt de toepassing in quarantaine geplaatst. Meer informatie over quarantainestaten [vindt u hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../manage-apps/check-status-user-account-provisioning.md)