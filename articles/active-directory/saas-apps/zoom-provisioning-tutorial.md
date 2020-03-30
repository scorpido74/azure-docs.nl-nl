---
title: 'Zelfstudie: Zoom configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het automatisch inrichten en de-provisionen van gebruikersaccounts van Azure AD naar Zoom.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/3/2019
ms.author: Zhchia
ms.openlocfilehash: 94c261da0c935cb7a41dde768069099b4e5ed251
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384072"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Zelfstudie: Zoom configureren voor automatische gebruikersinrichting

In deze zelfstudie worden de stappen beschreven die u moet uitvoeren in zowel Zoom- als Azure Active Directory (Azure AD) om automatische gebruikersinrichting te configureren. Wanneer azure AD is geconfigureerd, worden gebruikers en groepen automatisch ingericht en de-bepalingen ingericht om in te [zoomen](https://zoom.us/pricing/) met behulp van de Azure AD Provisioning-service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in Zoom
> * Gebruikers verwijderen in Zoom wanneer ze geen toegang meer nodig hebben
> * Gebruikerskenmerken gesynchroniseerd houden tussen Azure AD en Zoom
> * [Eén aanmelding bij](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-tutorial) Zoom (aanbevolen)

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* [Een Azure AD-tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Een gebruikersaccount in Azure AD met [toestemming](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) om provisioning te configureren (bijvoorbeeld toepassingsbeheerder, cloudtoepassingsbeheerder, toepassingseigenaar of globale beheerder). 
* [Een Zoomtenant](https://zoom.us/pricing).
* Een gebruikersaccount in Zoom met beheerdersmachtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Step 1. Uw inrichtingsimplementatie plannen
1. Meer informatie over [hoe de inprovisioningservice werkt.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)
2. Bepaal wie in de ruimte voor [de inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)zal zijn .
3. Bepaal welke gegevens u wilt [toewijzen tussen Azure AD en Zoom](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-zoom-to-support-provisioning-with-azure-ad"></a>Stap 2. Zoom configureren om provisioning met Azure AD te ondersteunen

1. Meld u aan bij uw [zoombeheerconsole](https://zoom.us/signin). Navigeer naar **Geavanceerde > Zoom voor ontwikkelaars** in het linkernavigatiedeelvenster.

    ![Zoom-integraties](media/zoom-provisioning-tutorial/zoom01.png)

2. Navigeer naar **Beheren** in de rechterbovenhoek van de pagina. 

    ![Zoominstallatie](media/zoom-provisioning-tutorial/zoom02.png)

3. Navigeer naar de gemaakte Azure AD-app. 
    
    ![Zoom-app](media/zoom-provisioning-tutorial/zoom03.png)

4. Selecteer **App-referenties** in het linkernavigatiedeelvenster.

    ![Zoom-app](media/zoom-provisioning-tutorial/zoom04.png)

5. Kopieer en sla het **JWT-token op.** Deze waarde wordt ingevoerd in het veld **Geheim token** op het tabblad Provisioning van uw Zoom-toepassing in de Azure-portal. Als u een nieuw niet-verlopend token nodig hebt, moet u de vervaldatum opnieuw configureren die automatisch een nieuw token genereert. 

    ![Zoominstallatie](media/zoom-provisioning-tutorial/zoom05.png)

## <a name="step-3-add-zoom-from-the-azure-ad-application-gallery"></a>Stap 3. Zoom toevoegen vanuit de Azure AD-toepassingsgalerie

Zoom toevoegen vanuit de Azure AD-toepassingsgalerie om de inrichting in Zoom te beheren. Als u zoomvoor SSO eerder hebt ingesteld, u dezelfde toepassing gebruiken. Het wordt echter aanbevolen om een aparte app te maken bij het testen van de integratie in eerste instantie. Meer informatie over het toevoegen van een toepassing uit de galerie [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Bepalen wie in het vermogen van de inrichting 

Met de Azure AD-inrichtingsservice u scopen die worden ingericht op basis van toewijzing aan de toepassing en of op basis van kenmerken van de gebruiker /groep. Als u ervoor kiest om het bereik te bepalen wie op basis van toewijzing aan uw app wordt toegewezen, u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om scope die zal worden ingericht uitsluitend op basis van attributen van de gebruiker of groep, u gebruik maken van een scoping filter zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)beschreven. 

* Wanneer u gebruikers en groepen aan Zoomen toewijst, moet u een andere rol dan **Standaardtoegang**selecteren. Gebruikers met de functie Standaardtoegang zijn uitgesloten van inrichten en worden gemarkeerd als niet effectief gerechtigd in de inrichtingslogboeken. Als de enige rol die beschikbaar is in de toepassing de standaardtoegangsrol is, u [het toepassingsmanifest bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Begin klein. Test met een kleine set gebruikers en groepen voordat u naar iedereen uitrolt. Wanneer de inrichtingsruimte is ingesteld op toegewezen gebruikers en groepen, u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, u een [op kenmerken gebaseerd scopingfilter](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-zoom"></a>Stap 5. Automatische gebruikersinrichting configureren in Zoom 

In deze sectie u de azure AD-inrichtingsservice configureren om gebruikers en/of groepen in TestApp te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Ga als een automatisch beheer van de gebruiker voor Zoomen in Azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Zoomen**in de lijst met toepassingen .

    ![De koppeling Zoom in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten.**

    ![Tabblad Inrichten](common/provisioning.png)

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Tabblad Inrichten](common/provisioning-automatic.png)

5. Voer onder de sectie `https://api.zoom.us/scim` **Beheerdersreferenties** de **URL van tenant**in . Voer de **waarde JWT-token** in die eerder in **Secret Token**is opgehaald. Klik **op Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met Zoom. Als de verbinding mislukt, moet u ervoor zorgen dat uw Zoom-account beheerdersmachtigingen heeft en het opnieuw proberen.

    ![Zoominrichting](./media/zoom-provisioning-tutorial/provisioning.png)

6. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fout moet ontvangen en schakel het selectievakje **Een e-mailmelding verzenden in wanneer er een fout optreedt.**

    ![E-mail met meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer azure **Active Directory-gebruikers synchroniseren om in te zoomen**onder de sectie **Toewijzingen** .

9. Controleer de gebruikerskenmerken die zijn gesynchroniseerd vanuit Azure AD naar Zoomen in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **Eigenschappen matching** worden gebruikt om de gebruikersaccounts in Zoom te matchen voor updatebewerkingen. Als u ervoor kiest het [overeenkomende doelkenmerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)te wijzigen, moet u ervoor zorgen dat de Zoom-API filteringgebruikers ondersteunt op basis van dat kenmerk. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |userName|Tekenreeks|
   |actief|Booleaans|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |e-mails[type eq "werk"]|Tekenreeks|
   |urn:ietf:params:scim:schema's:extension:enterprise:2.0:User:department|Tekenreeks|

10. Als u scopingfilters wilt configureren, raadpleegt u de volgende instructies in de zelfstudie van het [Scoping-filter.](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)

11. Als u de Azure AD-inrichtingsservice voor Zoom wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen.**

    ![Status inrichten ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten om in te zoomen door de gewenste waarden in **Bereik** te kiezen in de sectie **Instellingen.**

    ![Inrichtingskader](common/provisioning-scope.png)

13. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

    ![Configuratie van het opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste cyclus duurt langer om uit te voeren dan de volgende cycli, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Zodra u de inrichting hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te controleren:

1. Gebruik de [inrichtingslogboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) om te bepalen welke gebruikers met succes of zonder succes zijn ingericht
2. Controleer de [voortgangsbalk](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtingscyclus te bekijken en hoe dicht deze bij voltooiing is
3. Als de inrichtingsconfiguratie in een ongezonde status lijkt te zijn, wordt de toepassing in quarantaine geplaatst. Meer informatie over quarantainestaten [vindt u hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="connector-limitations"></a>Verbindingsbeperkingen
* Zoom staat vandaag de dag maximaal 9.999 basisgebruikers toe.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../manage-apps/check-status-user-account-provisioning.md)
