---
title: 'Zelf studie: Medius flow configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts van Azure AD naar Medius flow.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 04/30/2020
ms.author: Zhchia
ms.openlocfilehash: e8a64b3f42e52363f70d536963594bb6083a0992
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548069"
---
# <a name="tutorial-configure-mediusflow-for-automatic-user-provisioning"></a>Zelf studie: Medius flow configureren voor automatische gebruikers inrichting

In deze zelf studie worden de stappen beschreven die u moet uitvoeren in zowel Medius flow als Azure Active Directory (Azure AD) voor het configureren van automatische gebruikers inrichting. Wanneer de configuratie is geconfigureerd, worden gebruikers en groepen door Azure AD automatisch ingericht en [Gemedius flowd](https://www.mediusflow.com/) met behulp van de Azure AD-inrichtings service. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen. 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in Medius flow
> * Gebruikers in Medius flow verwijderen wanneer ze niet meer toegang nodig hebben
> * Gebruikers kenmerken gesynchroniseerd laten tussen Azure AD en Medius flow
> * Inrichtings groepen en groepslid maatschappen in Medius flow
> * Eenmalige aanmelding bij Medius flow (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-Tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Een gebruikers account in azure AD met [toestemming](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) voor het configureren van inrichting (bijvoorbeeld toepassings beheerder, Cloud toepassings beheerder, eigenaar van de toepassing of globale beheerder). 
* Een actief Medius flow-abonnement met een kwaliteits garantie of productie Tenant.
* Een gebruikers account in Medius flow met beheerders toegangs rechten om de configuratie in Medius flow te kunnen uitvoeren.
* De bedrijven die zijn toegevoegd aan de Medius flow-Tenant waarbij de gebruikers moeten worden ingericht.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Uw inrichtings implementatie plannen
1. Meer informatie over [de werking van de inrichtings service](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bepaal wie binnen het [bereik van de inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)valt.
3. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en Medius flow](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-mediusflow-to-support-provisioning-with-azure-ad"></a>Stap 2. Medius flow configureren voor ondersteuning bij het inrichten met Azure AD

### <a name="activate-the-microsoft-365-app-within-mediusflow"></a>De Microsoft 365-app activeren binnen Medius flow
Begin met het inschakelen van de toegang tot de Azure AD-aanmelding en de Azure AD-configuratie functie in Medius Flow door de volgende stappen uit te voeren:

#### <a name="user-login"></a>Gebruikers aanmelding
Als u de aanmeldings stroom naar Microsoft 365/Azure AD wilt inschakelen, raadpleegt u [dit] ( https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-login-setup) artikel.

#### <a name="user-transfer-configuration"></a>Configuratie van gebruikers overdracht
Raadpleeg [Dit](
https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-sync-setup) artikel voor het inschakelen van de configuratie portal van de gebruikers die u wilt inrichten vanuit Azure AD.

#### <a name="configure-user-provisioning"></a>Gebruikersinrichting configureren

1.  Meld u aan bij de [Medius flow-beheer console](https://office365.cloudapp.mediusflow.com/) door de Tenant-id op te geven.

    ![Verifiëren](./media/mediusflow-provisioning-tutorial/1-auth.png)

2. Controleer de verbinding met Medius flow.

    ![Verifiëren](./media/mediusflow-provisioning-tutorial/2-verify-connection.png)

3. Geef de Azure AD-Tenant-ID op.

    ![Tenant-ID opgeven](./media/mediusflow-provisioning-tutorial/3-provide-azuread-tenantid.png)

   Meer informatie vindt u in de [Veelgestelde vragen](https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#how-do-i-get-the-azure-tenant-id) over hoe u deze kunt vinden.

4. Sla de configuratie op.

    ![Verifiëren](./media/mediusflow-provisioning-tutorial/4-save-config.png)

5. Selecteer gebruikers inrichten en klik op **OK**.

    ![Verifiëren](./media/mediusflow-provisioning-tutorial/5-select-user-provisioning.png)

6. Klik op **geheime sleutel genereren**. Kopieer deze waarde en sla deze op. Deze waarde wordt ingevoerd in het veld **geheime token** op het tabblad **inrichten** van uw Medius flow-toepassing in de Azure Portal.

    ![Verifiëren](./media/mediusflow-provisioning-tutorial/6-create-secret-1.png)

7. Klik op **OK**.

    ![Verifiëren](./media/mediusflow-provisioning-tutorial/7-confirm-secret.png)

8. Als u wilt dat de gebruikers worden geïmporteerd met een vooraf gedefinieerde set rollen, bedrijven en andere algemene configuraties in Medius flow, moet u deze eerst configureren. Begin door de configuratie toe te voegen door te klikken op **nieuwe configuratie toevoegen**.

    ![Gebruikers configuratie](./media/mediusflow-provisioning-tutorial/8-configure-user-configuration-1.png)

9. Geef de standaard instellingen voor de gebruikers op. In deze weer gave kunt u het standaard kenmerk instellen. Als de standaard instellingen in orde zijn, is het voldoende om alleen een geldige bedrijfs naam op te geven. Omdat deze configuratie-instellingen zijn opgehaald van Medius flow, moeten ze eerst worden geconfigureerd. Zie de sectie **vereisten** van dit artikel voor meer informatie.

    ![Gebruikers configuratie](./media/mediusflow-provisioning-tutorial/9-configure-user-config-detail-1.png)

10. Klik op **Opslaan** om de gebruikers configuratie op te slaan.

    ![Gebruikers configuratie](./media/mediusflow-provisioning-tutorial/10-done-1.png)

11. Als u de koppeling voor het inrichten van gebruikers wilt ophalen, klikt u op de **koppeling kopiëren van scim**. Kopieer deze waarde en sla deze op. Deze waarde wordt ingevoerd in het veld **Tenant-URL** op het tabblad **inrichten** van uw Medius flow-toepassing in de Azure Portal.
 
    ![Gebruikers configuratie](./media/mediusflow-provisioning-tutorial/11-get-scim-link.png)

## <a name="step-3-add-mediusflow-from-the-azure-ad-application-gallery"></a>Stap 3. Medius flow toevoegen vanuit de Azure AD-toepassings galerie

Voeg Medius flow toe vanuit de Azure AD-toepassings galerie om het beheren van de inrichting van Medius flow te starten. Als u eerder Medius flow voor SSO hebt ingesteld, kunt u dezelfde toepassing gebruiken. Het is echter raadzaam dat u een afzonderlijke app maakt wanneer u de integratie in eerste instantie test. Meer informatie over het toevoegen van een toepassing uit [de galerie.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie binnen het bereik van de inrichting valt 

Met de Azure AD-inrichtings service kunt u bereiken die worden ingericht op basis van de toewijzing aan de toepassing en of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van de toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen toe te wijzen aan de toepassing. Als u kiest voor het bereik dat alleen wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereik filter gebruiken zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan Medius flow, moet u een andere rol dan **standaard toegang**selecteren. Gebruikers met de rol standaard toegang worden uitgesloten van inrichting en worden gemarkeerd als niet effectief in de inrichtings Logboeken. Als de enige rol die beschikbaar is op de toepassing de standaard rol Access is, kunt u [het toepassings manifest bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Begin klein. Test met een klein aantal gebruikers en groepen voordat u naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen toe te wijzen aan de app. Wanneer bereik is ingesteld op alle gebruikers en groepen, kunt u een [kenmerk op basis van bereik filteren](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-mediusflow"></a>Stap 5. Automatische gebruikers inrichting configureren voor Medius flow 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in TestApp te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

### <a name="to-configure-automatic-user-provisioning-for-mediusflow-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor Medius flow in azure AD:

1. Meld u aan bij de [Microsoft Azure-portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Medius flow**.

    ![De koppeling Medius flow in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. Geef in het gedeelte **beheerders referenties** de waarde voor de TENANT-URL op die u eerder hebt opgehaald in de **Tenant-URL**. Voer de geheime token waarde in die eerder is opgehaald in het **geheime token**. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met Medius flow. Als de verbinding mislukt, zorg er dan voor dat uw Medius flow-account beheerders machtigingen heeft en probeer het opnieuw.

      ![inrichtings](./media/mediusflow-provisioning-tutorial/provisioning.png)

6. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje **e-mail melding verzenden wanneer een fout optreedt** in.

    ![E-mail melding](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Medius flow**.

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Medius flow in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Medius flow voor bijwerk bewerkingen. Als u ervoor kiest om het [overeenkomende doel kenmerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)te wijzigen, moet u ervoor zorgen dat de MEDIUS flow-API het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |userName|Tekenreeks|
   |e-mail berichten [type EQ "werk]. waarde|Tekenreeks|
   |naam. displayName|Tekenreeks|
   |actief|Boolean-waarde|
   |name. naam|Tekenreeks|
   |naam. familielid|Tekenreeks|
   |naam. opgemaakt|Tekenreeks|
   |externalID|Tekenreeks|
   |urn: IETF: params: scim: schemas: extensie: Enter prise: 2.0: gebruiker: Manager|Naslaginformatie|


10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren met Medius flow**.

11. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Medius flow in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de groepen in Medius flow te vergelijken voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

      |Kenmerk|Type|
      |---|---|
      |displayName|Tekenreeks|
      |externalID|Tekenreeks|
      |leden|Naslaginformatie|

12. Raadpleeg de volgende instructies in de [zelf studie](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

13. Als u de Azure AD-inrichtings service voor **Medius flow wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor Medius Flow door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

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
