---
title: 'Zelf studie: een Webroot Security bewustzijns training configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts van Azure AD naar een Webroot-training voor beveiligings detectie.
services: active-directory
author: Zhchia
writer: Zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/06/2020
ms.author: Zhchia
ms.openlocfilehash: 9345aaac306c8c009d6fc0a01c57beed3a22b10b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88523470"
---
# <a name="tutorial-configure-webroot-security-awareness-training-for-automatic-user-provisioning"></a>Zelf studie: een Webroot Security bewustzijns training configureren voor automatische gebruikers inrichting

In deze zelf studie worden de stappen beschreven die u moet uitvoeren in zowel de Webroot Security bewustzijn-training als Azure Active Directory (Azure AD) voor het configureren van automatische gebruikers inrichting. Wanneer de configuratie is geconfigureerd, worden gebruikers en [groepen door Azure](https://www.webroot.com/) AD automatisch ingericht en ongedaan gemaakt met behulp van de Azure AD-inrichtings service. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen. 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers in Webroot Security bewustzijns training maken
> * Gebruikers in Webroot Security bewustzijns training verwijderen wanneer ze geen toegang meer nodig hebben
> * Gebruikers kenmerken gesynchroniseerd houden tussen Azure AD en de Webroot-training voor beveiligings bewustmaking
> * Inrichtings groepen en groepslid maatschappen in de cursus Webroot Security bewustzijn

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-Tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Een gebruikers account in azure AD met [toestemming](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) voor het configureren van inrichting (bijvoorbeeld toepassings beheerder, Cloud toepassings beheerder, eigenaar van de toepassing of globale beheerder).
* Een beheerde service provider console met een Webroot Security bewustzijns training ingeschakeld voor ten minste één van uw sites.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Uw inrichtings implementatie plannen
1. Meer informatie over [de werking van de inrichtings service](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bepaal wie binnen het [bereik van de inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)valt.
3. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en Webroot Security bewustzijns training](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-webroot-security-awareness-training-to-support-provisioning-with-azure-ad"></a>Stap 2. Webroot Security bewustzijns training configureren ter ondersteuning van het inrichten met Azure AD

### <a name="obtain-a-secret-token"></a>Een geheim token verkrijgen

Als u uw site wilt verbinden met Azure AD, moet u een **geheim token** voor die site verkrijgen in de Webroot-beheer console.

1. Meld u aan bij uw [Webroot-beheer console](https://identity.webrootanywhere.com/v1/Account/login#tab_customers)

2. Klik op het tabblad **sites** op het tandwiel pictogram in de kolom beveiligings gerichte training voor de site die u wilt verbinden met Azure AD.

    ![Tandwiel pictogram](./media/webroot-security-awareness-training-provisioning-tutorial/gear-icon.png)

3. Klik op de knop voor het **configureren van Azure AD-integratie**.

    ![Azure AD-integratie configureren](./media/webroot-security-awareness-training-provisioning-tutorial/configure-azure-ad-integration.png)

4. Kopieer het **geheim token**en sla het op. Deze waarde wordt ingevoerd in het veld geheime token op het tabblad inrichting van uw toepassing voor het inrichten van Webroot in de Azure Portal.

5. Klik op **Gereed**.

    ![Geheim token kopiëren](./media/webroot-security-awareness-training-provisioning-tutorial/copy-secret-token.png)

## <a name="step-3-add-webroot-security-awareness-training-from-the-azure-ad-application-gallery"></a>Stap 3. Een Webroot-training voor beveiligings bewustmaking toevoegen vanuit de Azure AD-toepassings galerie

Voeg een Webroot Security bewustzijns training toe vanuit de Azure AD-toepassings galerie om het beheer van de inrichting te starten voor een Webroot Security bewustzijns training. Meer informatie over het toevoegen van een toepassing uit [de galerie.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie binnen het bereik van de inrichting valt 

Met de Azure AD-inrichtings service kunt u bereiken die worden ingericht op basis van de toewijzing aan de toepassing en of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van de toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen toe te wijzen aan de toepassing. Als u kiest voor het bereik dat alleen wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereik filter gebruiken zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan een Webroot-training voor beveiligings bewustmaking, moet u een andere rol dan **standaard toegang**selecteren. Gebruikers met de rol standaard toegang worden uitgesloten van inrichting en worden gemarkeerd als niet effectief in de inrichtings Logboeken. Als de enige rol die beschikbaar is op de toepassing de standaard rol Access is, kunt u [het toepassings manifest bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Begin klein. Test met een klein aantal gebruikers en groepen voordat u naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen toe te wijzen aan de app. Wanneer bereik is ingesteld op alle gebruikers en groepen, kunt u een [kenmerk op basis van bereik filteren](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-webroot-security-awareness-training"></a>Stap 5. Automatische gebruikers inrichting configureren voor een Webroot Security bewustzijns training 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in TestApp te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

### <a name="to-configure-automatic-user-provisioning-for-webroot-security-awareness-training-in-azure-ad"></a>Voor het configureren van automatische gebruikers inrichting voor Webroot Security bewustzijns training in azure AD:

1. Meld u aan bij de [Microsoft Azure-portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Webroot Security bewustzijns training**.

    ![De koppeling Webroot Security bewustzijns training in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. Selecteer in de sectie **beheerders referenties** de invoer `https://awarenessapi.webrootanywhere.com/api/v2/scim` in de Tenant- **URL**. Voer de geheime token waarde in die eerder is opgehaald in het **geheime token**. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met de Webroot Security bewustzijns training. Als de verbinding mislukt, zorg er dan voor dat uw account voor de toegang tot de Webroot-beveiligings bewustmaking beheerders machtigingen heeft en probeer het opnieuw.

    ![inrichtings](./media/webroot-security-awareness-training-provisioning-tutorial/provisioning.png)

6. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje **e-mail melding verzenden wanneer een fout optreedt** in.

    ![E-mail melding](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers inrichten**.

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Webroot training voor beveiligings bewustmaking in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Webroot Security bewustzijn training voor update bewerkingen. Als u ervoor kiest om het [overeenkomende doel kenmerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)te wijzigen, moet u ervoor zorgen dat de Webroot Security bewustzijns-API filtering voor het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|Ondersteund voor filteren|
   |---|---|---|
   |externalId|Tekenreeks|&check;|
   |name. naam|Tekenreeks|
   |naam. familielid|Tekenreeks|
   |e-mail berichten [type EQ "werk]. waarde|Tekenreeks|

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen inrichten**.

11. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Webroot training voor beveiligings bewustmaking in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de groepen in de cursus Webroot Security bewustzijn voor update bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

      |Kenmerk|Type|Ondersteund voor filteren|
      |---|---|---|
      |displayName|Tekenreeks|&check;|
      |leden|Naslaginformatie|
      |externalId|Tekenreeks|

12. Raadpleeg de volgende instructies in de [zelf studie](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

13. Als u de Azure AD-inrichtings service wilt inschakelen voor een Webroot Security bewustzijns training, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor een Webroot Security bewustzijns training door de gewenste waarden in het **bereik** in het gedeelte **instellingen** te kiezen.

    ![Inrichtings bereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de initiële synchronisatie cyclus gestart van alle gebruikers en groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . De eerste cyclus duurt langer dan volgende cycli, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende bronnen om uw implementatie te bewaken:

1. De [inrichtings logboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) gebruiken om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangs balk](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtings cyclus te bekijken en te bepalen hoe dicht deze is voltooid
3. Als de inrichtings configuratie een slechte status heeft, gaat de toepassing in quarantaine. Meer informatie over de quarantaine statussen [vindt u hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../manage-apps/check-status-user-account-provisioning.md)
