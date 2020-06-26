---
title: 'Zelf studie: gebruikers inrichten voor een toegestane vertraging-Azure AD'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op toegestane vertraging.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8b7fa5aea835329be8f65a3bb1775ba5b0d97d4
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85389854"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Zelf studie: toegestane vertraging voor automatische gebruikers inrichting configureren

Het doel van deze zelf studie is om u te laten zien welke stappen u moet uitvoeren, en hoe u in azure AD automatisch gebruikers accounts van Azure AD kunt inrichten en de inrichting van een toegestane vertraging. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen. 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers in toegestane vertraging maken
> * Gebruikers in een toegestane vertraging verwijderen wanneer ze niet meer toegang nodig hebben
> * Gebruikers kenmerken gesynchroniseerd laten tussen Azure AD en toegestane vertraging
> * Inrichtings groepen en groepslid maatschappen in toegestane vertraging
> * [Eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-tutorial) voor toegestane vertraging (aanbevolen)


## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u de volgende items al hebt:

* [Een Azure AD-Tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Een gebruikers account in azure AD met [toestemming](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) voor het configureren van inrichting (bijvoorbeeld toepassings beheerder, Cloud toepassings beheerder, eigenaar van de toepassing of globale beheerder).
* Een toegestane vertragings Tenant met het [plus plan](https://aadsyncfabric.slack.com/pricing) of beter ingeschakeld.
* Een gebruikers account met een toegestane vertraging met team beheerders machtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Uw inrichtings implementatie plannen
1. Meer informatie over [de werking van de inrichtings service](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bepaal wie binnen het [bereik van de inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)valt.
3. Bepalen welke gegevens moeten worden [toegewezen tussen Azure AD en een toegestane vertraging](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-add-slack-from-the-azure-ad-application-gallery"></a>Stap 2. Toegestane vertraging toevoegen vanuit de Azure AD-toepassings galerie

Voeg een toegestane vertraging toe van de Azure AD-toepassings galerie om het beheer van de inrichting te starten met toegestane vertraging. Als u eerder een toegestane vertraging hebt ingesteld voor SSO, kunt u dezelfde toepassing gebruiken. Het is echter raadzaam dat u een afzonderlijke app maakt wanneer u de integratie in eerste instantie test. Meer informatie over het toevoegen van een toepassing uit [de galerie.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-3-define-who-will-be-in-scope-for-provisioning"></a>Stap 3. Definiëren wie binnen het bereik van de inrichting valt 

Met de Azure AD-inrichtings service kunt u bereiken die worden ingericht op basis van de toewijzing aan de toepassing en of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van de toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen toe te wijzen aan de toepassing. Als u kiest voor het bereik dat alleen wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereik filter gebruiken zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst op toegestane vertraging, moet u een andere rol dan **standaard toegang**selecteren. Gebruikers met de rol standaard toegang worden uitgesloten van inrichting en worden gemarkeerd als niet effectief in de inrichtings Logboeken. Als de enige rol die beschikbaar is op de toepassing de standaard rol Access is, kunt u [het toepassings manifest bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Begin klein. Test met een klein aantal gebruikers en groepen voordat u naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen toe te wijzen aan de app. Wanneer bereik is ingesteld op alle gebruikers en groepen, kunt u een [kenmerk op basis van bereik filteren](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)opgeven.

## <a name="step-4-configure-automatic-user-provisioning-to-slack"></a>Stap 4. Automatische gebruikers inrichting op toegestane vertraging configureren 

In deze sectie wordt u begeleid bij het verbinden van de API voor het inrichten van uw Azure AD-gebruikers account en het configureren van de inrichtings service om toegewezen gebruikers accounts in toegestane vertraging te maken, bij te werken en uit te scha kelen op basis van de gebruikers-en groeps toewijzing in azure AD.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Automatische toewijzing van gebruikers accounts configureren voor toegestane vertraging in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen de optie **Slack**.

    ![De Slack-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. Klik onder de sectie **beheerders referenties** op **autoriseren**. Hiermee opent u een dialoog venster voor een toegestane autorisatie in een nieuw browser venster.

    ![Autorisatie](media/slack-provisioning-tutorial/authorization.png)


6. Meld u in het nieuwe venster aan met de beheerders account van uw team. Selecteer in het dialoog venster resulterende autorisatie het uitstel team waarvoor u het inrichten wilt inschakelen en selecteer vervolgens **autoriseren**. Als u klaar bent, keert u terug naar de Azure Portal om de inrichtings configuratie te volt ooien.

    ![Autorisatie dialoogvenster](./media/slack-provisioning-tutorial/slackauthorize.png)

7. Klik in het Azure Portal op **verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met uw toegestane marge-app. Als de verbinding mislukt, zorg er dan voor dat uw toegestane account beheerders machtigingen voor het team heeft en probeer de stap ' autoriseren ' opnieuw uit te voeren.

8. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje **e-mail melding verzenden wanneer een fout optreedt** in.

    ![E-mail melding](common/provisioning-notification-email.png)

9. Selecteer **Opslaan**.

10. Selecteer in de sectie toewijzingen de optie **synchronisatie van Azure Active Directory gebruikers naar een toegestane vertraging**.

11. Controleer in de sectie **kenmerk toewijzingen** de gebruikers kenmerken die vanuit Azure AD worden gesynchroniseerd op toegestane vertraging. Houd er rekening mee dat de kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikers accounts in de toegestane vertraging voor update bewerkingen te vergelijken. Selecteer de knop Opslaan om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |actief|Booleaans|
   |externalId|Tekenreeks|
   |displayName|Tekenreeks|
   |naam. familielid|Tekenreeks|
   |name. naam|Tekenreeks|
   |titel|Tekenreeks|
   |e-mail berichten [type EQ "werk]. waarde|Tekenreeks|
   |userName|Tekenreeks|
   |Naam|Tekenreeks|
   |adressen [type EQ "geen type"]. streetAddress|Tekenreeks|
   |adressen [type EQ "]. locatie|Tekenreeks|
   |adressen [type EQ "]. Region|Tekenreeks|
   |adressen [type EQ "niet-getypt"]. post code|Tekenreeks|
   |adressen [type EQ "niet-getypt"]. land|Tekenreeks|
   |phoneNumbers [type EQ "Mobile"]. waarde|Tekenreeks|
   |phoneNumbers [type EQ "werk]. waarde|Tekenreeks|
   |rollen [Primary EQ "True"]. Value|Tekenreeks|
   |landinstelling|Tekenreeks|
   |naam. honorificPrefix|Tekenreeks|
   |Foto's [type EQ "Photo"]. Value|Tekenreeks|
   |profileUrl|Tekenreeks|
   |tijd zone|Tekenreeks|
   |User type|Tekenreeks|
   |urn: scim: schemas: extensie: Enter prise: 1.0. Department|Tekenreeks|
   |urn: scim: schemas: extensie: Enter prise: 1.0. Manager|Naslaginformatie|
   |urn: scim: schemas: extension: Enter prise: 1.0. employeeNumber|Tekenreeks|
   |urn: scim: schemas: extension: Enter prise: 1.0. costCenter|Tekenreeks|
   |urn: scim: schemas: extension: Enter prise: 1.0. Organization|Tekenreeks|
   |urn: scim: schemas: extensie: Enter prise: 1.0. divisie|Tekenreeks|

12. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren naar toegestane vertraging**.

13. Controleer in de sectie **kenmerk toewijzingen** de groeps kenmerken die worden gesynchroniseerd vanuit Azure AD naar een toegestane vertraging. Houd er rekening mee dat de kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, overeenkomen met de groepen in de toegestane vertraging voor bijwerk bewerkingen. Selecteer de knop Opslaan om eventuele wijzigingen door te voeren.

      |Kenmerk|Type|
      |---|---|
      |displayName|Tekenreeks|
      |leden|Naslaginformatie|

14. Raadpleeg de volgende instructies in de [zelf studie](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

15. Als u de Azure AD-inrichtings service wilt inschakelen voor een toegestane vertraging, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen**

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

16. Definieer de gebruikers en/of groepen die u wilt inrichten voor toegestane vertraging door de gewenste waarden in het **bereik** in de sectie **instellingen** te kiezen.

    ![Inrichtings bereik](common/provisioning-scope.png)

17. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de initiële synchronisatie cyclus gestart van alle gebruikers en groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . De eerste cyclus duurt langer dan volgende cycli, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. 

## <a name="step-5-monitor-your-deployment"></a>Stap 5. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende bronnen om uw implementatie te bewaken:

1. De [inrichtings logboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) gebruiken om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangs balk](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtings cyclus te bekijken en te bepalen hoe dicht deze is voltooid
3. Als de inrichtings configuratie een slechte status heeft, gaat de toepassing in quarantaine. Meer informatie over de quarantaine statussen [vindt u hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="connector-limitations"></a>Connector beperkingen

* Bij het configureren van het kenmerk **DisplayName** van de toegestane vertraging moet u rekening houden met het volgende gedrag:

  * Waarden zijn niet helemaal uniek (bijvoorbeeld 2 gebruikers kunnen dezelfde weergave naam hebben)

  * Ondersteunt niet-Engelse tekens, spaties en hoofd letters. 
  
  * Toegestane interpunctie omvat punten, onderstrepings tekens, afbreek streepjes, apostrofs, haakjes (bijvoorbeeld **[{}]**)) en scheidings tekens (bijvoorbeeld **/;**).
  
  * Alleen updates als deze twee instellingen zijn geconfigureerd in de werk plek/organisatie- **profiel synchronisatie is ingeschakeld** en **gebruikers kunnen de weergave naam niet wijzigen**.
  
* Het kenmerk van de **gebruikers naam** van de toegestane vertraging moet minder dan 21 tekens bevatten en moet een unieke waarde hebben.

* De toegestane vertraging kan alleen overeenkomen met de kenmerken **username** en **e-mail**.  

## <a name="change-log"></a>Wijzigingenlogboek

* 06/16/2020-gewijzigd kenmerk DisplayName wordt alleen bijgewerkt tijdens het maken van een nieuwe gebruiker.

## <a name="additional-resources"></a>Aanvullende resources

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../manage-apps/check-status-user-account-provisioning.md)