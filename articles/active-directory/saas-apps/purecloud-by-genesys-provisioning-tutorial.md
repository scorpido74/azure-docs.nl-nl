---
title: 'Zelf studie: PureCloud op Genesys configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts van Azure AD naar PureCloud door Genesys.
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
ms.openlocfilehash: b0b5147faf82fedb6dc3c2eea54dcff1b9343f7a
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087477"
---
# <a name="tutorial-configure-purecloud-by-genesys-for-automatic-user-provisioning"></a>Zelf studie: PureCloud configureren door Genesys voor automatische gebruikers inrichting

In deze zelf studie worden de stappen beschreven die u moet uitvoeren in zowel PureCloud als Genesys en Azure Active Directory (Azure AD) om het automatisch inrichten van gebruikers te configureren. Wanneer de configuratie is geconfigureerd, worden gebruikers en groepen door Azure AD automatisch ingericht en de [PureCloud door Genesys](https://www.genesys.com) met behulp van de Azure AD-inrichtings service. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen. 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in PureCloud door Genesys
> * Gebruikers in PureCloud verwijderen door Genesys wanneer ze geen toegang meer nodig hebben
> * Gebruikers kenmerken gesynchroniseerd laten tussen Azure AD en PureCloud door Genesys
> * Inrichtings groepen en groepslid maatschappen in PureCloud door Genesys
> * [Eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial) voor PureCloud door Genesys (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-Tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Een gebruikers account in azure AD met [toestemming](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) voor het configureren van inrichting (bijvoorbeeld toepassings beheerder, Cloud toepassings beheerder, eigenaar van de toepassing of globale beheerder). 
* Een PureCloud [-organisatie](https://help.mypurecloud.com/?p=81984).
* Een gebruiker met [machtigingen](https://help.mypurecloud.com/?p=24360) voor het maken van een OAuth-client.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Uw inrichtings implementatie plannen
1. Meer informatie over [de werking van de inrichtings service](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bepaal wie binnen het [bereik van de inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)valt.
3. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en PureCloud door Genesys](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-purecloud-by-genesys-to-support-provisioning-with-azure-ad"></a>Stap 2. PureCloud configureren door Genesys voor het ondersteunen van het inrichten met Azure AD

1. Maak een [OAuth-client](https://help.mypurecloud.com/?p=188023) die is geconfigureerd in uw PureCloud-organisatie.
2. Genereer een token [met uw OAuth-client](https://developer.mypurecloud.com/api/rest/authorization/use-client-credentials.html).
3. Als u het groepslid maatschap automatisch wilt inrichten binnen PureCloud, moet u [groepen maken](https://help.mypurecloud.com/?p=52397) in PureCloud met een identieke naam voor de groep in azure AD.

## <a name="step-3-add-purecloud-by-genesys-from-the-azure-ad-application-gallery"></a>Stap 3. PureCloud toevoegen door Genesys uit de Azure AD-toepassings galerie

Voeg PureCloud door Genesys toe vanuit de Azure AD-toepassings galerie om het beheer van de inrichting te starten op PureCloud door Genesys. Als u eerder PureCloud hebt ingesteld door Genesys voor SSO, kunt u dezelfde toepassing gebruiken. Het is echter raadzaam dat u een afzonderlijke app maakt wanneer u de integratie in eerste instantie test. Meer informatie over het toevoegen van een toepassing uit [de galerie.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie binnen het bereik van de inrichting valt 

Met de Azure AD-inrichtings service kunt u bereiken die worden ingericht op basis van de toewijzing aan de toepassing en of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van de toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen toe te wijzen aan de toepassing. Als u kiest voor het bereik dat alleen wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereik filter gebruiken zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan PureCloud door Genesys, moet u een andere rol dan de **standaard toegang**selecteren. Gebruikers met de rol standaard toegang worden uitgesloten van inrichting en worden gemarkeerd als niet effectief in de inrichtings Logboeken. Als de enige rol die beschikbaar is op de toepassing de standaard rol Access is, kunt u [het toepassings manifest bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Begin klein. Test met een klein aantal gebruikers en groepen voordat u naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen toe te wijzen aan de app. Wanneer bereik is ingesteld op alle gebruikers en groepen, kunt u een [kenmerk op basis van bereik filteren](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-purecloud-by-genesys"></a>Stap 5. Automatische gebruikers inrichting configureren voor PureCloud door Genesys 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in TestApp te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

### <a name="to-configure-automatic-user-provisioning-for-purecloud-by-genesys-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor PureCloud door Genesys in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **PureCloud by Genesys** in de lijst met toepassingen.

    ![De koppeling PureCloud by Genesys in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. Typ in het gedeelte **beheerders referenties** de PureCloud van de API-URL en het OAuth-token in de velden **Tenant-URL** en **geheime** token in respectievelijk. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met PureCloud door Genesys. Als de verbinding mislukt, controleert u of uw PureCloud door Genesys-account beheerders machtigingen heeft en probeer het opnieuw.

    ![inrichtings](./media/purecloud-by-genesys-provisioning-tutorial/provisioning.png)

6. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje **e-mail melding verzenden wanneer een fout optreedt** in.

    ![E-mail melding](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met PureCloud door Genesys**.

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar PureCloud door Genesys in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikers accounts in PureCloud door Genesys te vergelijken voor bijwerk bewerkingen. Als u ervoor kiest om het [overeenkomende doel kenmerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)te wijzigen, moet u ervoor zorgen dat het PureCloud door GENESYS-API het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

     |Kenmerk|Type|
     |---|---|
     |userName|Tekenreeks|
     |actief|Booleaans|
     |displayName|Tekenreeks|
     |e-mailberichten [type eq 'werk'] .value|Tekenreeks|
     |titel|Tekenreeks|
     |phoneNumbers [type eq 'mobiel'] .value|Tekenreeks|
     |phoneNumbers [type eq 'werk'] .value|Tekenreeks|
     |urn: IETF: params: scim: schemas: extension: Enter prise: 2.0: gebruiker: Department|Tekenreeks|
     |urn: IETF: params: scim: schemas: extensie: Enter prise: 2.0: gebruiker: Manager|Naslaginformatie|

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren op PureCloud door Genesys**.

11. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar PureCloud door Genesys in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om de groepen in PureCloud door Genesys te vergelijken voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren. PureCloud door Genesys biedt geen ondersteuning voor het maken of verwijderen van groepen en biedt alleen ondersteuning voor het bijwerken van een groep.

      |Kenmerk|Type|
      |---|---|
      |displayName|Tekenreeks|
      |externalId|Tekenreeks|
      |leden|Naslaginformatie|

12. Raadpleeg de volgende instructies in de [zelf studie](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

13. Als u de Azure AD-inrichtings service voor PureCloud door **Genesys wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor PureCloud door Genesys door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtings bereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de initiële synchronisatie cyclus gestart van alle gebruikers en groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . De eerste cyclus duurt langer dan volgende cycli, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende bronnen om uw implementatie te bewaken:

* De [inrichtings logboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) gebruiken om te bepalen welke gebruikers al dan niet met succes zijn ingericht
* Controleer de [voortgangs balk](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtings cyclus te bekijken en te bepalen hoe dicht deze is voltooid
* Als de inrichtings configuratie een slechte status heeft, gaat de toepassing in quarantaine. Meer informatie over de quarantaine statussen [vindt u hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Aanvullende resources

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../manage-apps/check-status-user-account-provisioning.md)
