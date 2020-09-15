---
title: 'Zelf studie: RingCentral configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts van Azure AD naar RingCentral.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 10/30/2019
ms.author: Zhchia
ms.openlocfilehash: aaaa073f798998ed97be1bd3cd66795d7b3dfb23
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2020
ms.locfileid: "90069659"
---
# <a name="tutorial-configure-ringcentral-for-automatic-user-provisioning"></a>Zelf studie: RingCentral configureren voor automatische gebruikers inrichting

In deze zelf studie worden de stappen beschreven die u moet uitvoeren in zowel RingCentral als Azure Active Directory (Azure AD) voor het configureren van automatische gebruikers inrichting. Wanneer de configuratie is geconfigureerd, worden gebruikers en groepen door Azure AD automatisch ingericht en [GeRingCentrald](https://www.ringcentral.com/office/plansandpricing.html) met behulp van de Azure AD-inrichtings service. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in RingCentral
> * Gebruikers in RingCentral verwijderen wanneer ze niet meer toegang nodig hebben
> * Gebruikers kenmerken gesynchroniseerd laten tussen Azure AD en RingCentral
> * [Eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial) bij RingCentral (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-Tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Een gebruikers account in azure AD met [toestemming](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) voor het configureren van inrichting (bijvoorbeeld toepassings beheerder, Cloud toepassings beheerder, eigenaar van de toepassing of globale beheerder). 
* [Een RingCentral-Tenant](https://www.ringcentral.com/office/plansandpricing.html)
* Een gebruikers account in RingCentral met beheerders machtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Uw inrichtings implementatie plannen
1. Meer informatie over [de werking van de inrichtings service](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bepaal wie binnen het [bereik van de inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)valt.
3. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en RingCentral](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-ringcentral-to-support-provisioning-with-azure-ad"></a>Stap 2. RingCentral configureren voor ondersteuning bij het inrichten met Azure AD

1. Meld u aan bij de [RingCentral-beheer console](https://login.ringcentral.com/sw.html). Navigeer naar **Hulpprogram ma's > Directory-integratie**.

    ![RingCentral-beheer console](media/ringcentral-provisioning-tutorial/admin.png)

2.  Kies **scim** onder **Directory provider selecteren**. (In de toekomst is er een optie met de naam Azure Active Directory). Klik op **scim-service inschakelen**.

    ![SCIM RingCentral toevoegen](media/ringcentral-provisioning-tutorial/scim.png)

3.  Neem contact op met het ondersteunings team van RingCentral op matthew.hunt@ringcentral.com een **scim-verificatie token**. Deze waarde wordt ingevoerd in het veld geheime token op het tabblad inrichten van uw RingCentral-toepassing in de Azure Portal.

> [!NOTE]
> Om licenties aan gebruikers toe te wijzen, raadpleegt u [de koppeling naar](https://support.ringcentral.com/s/article/5-10-Adding-Extensions-via-Web?language)de video.

## <a name="step-3-add-ringcentral-from-the-azure-ad-application-gallery"></a>Stap 3. RingCentral toevoegen vanuit de Azure AD-toepassings galerie

Voeg RingCentral toe vanuit de Azure AD-toepassings galerie om het beheren van de inrichting van RingCentral te starten. Als u eerder RingCentral voor SSO hebt ingesteld, kunt u dezelfde toepassing gebruiken. Het is echter raadzaam dat u een afzonderlijke app maakt wanneer u de integratie in eerste instantie test. Meer informatie over het toevoegen van een toepassing uit [de galerie.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie binnen het bereik van de inrichting valt 

Met de Azure AD-inrichtings service kunt u bereiken die worden ingericht op basis van de toewijzing aan de toepassing en of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van de toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen toe te wijzen aan de toepassing. Als u kiest voor het bereik dat alleen wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereik filter gebruiken zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan RingCentral, moet u een andere rol dan **standaard toegang**selecteren. Gebruikers met de rol standaard toegang worden uitgesloten van inrichting en worden gemarkeerd als niet effectief in de inrichtings Logboeken. Als de enige rol die beschikbaar is op de toepassing de standaard rol Access is, kunt u [het toepassings manifest bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Begin klein. Test met een klein aantal gebruikers en groepen voordat u naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen toe te wijzen aan de app. Wanneer bereik is ingesteld op alle gebruikers en groepen, kunt u een [kenmerk op basis van bereik filteren](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-ringcentral"></a>Stap 5. Automatische gebruikers inrichting configureren voor RingCentral 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in TestApp te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

### <a name="to-configure-automatic-user-provisioning-for-ringcentral-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor RingCentral in azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **RingCentral** in de lijst met toepassingen.

    ![De koppeling RingCentral in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. Selecteer in de sectie **beheerders referenties** de invoer `https://platform.ringcentral.com/scim/v2` in de Tenant- **URL**. Voer de waarde voor het **scim-verificatie token** in die eerder is opgehaald in het **geheime token**. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met RingCentral. Als de verbinding mislukt, zorg er dan voor dat uw RingCentral-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Tabblad inrichten](./media/ringcentral-provisioning-tutorial/provisioning.png)

6. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje **e-mail melding verzenden wanneer een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met RingCentral**.

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar RingCentral in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in RingCentral voor bijwerk bewerkingen. Als u ervoor kiest om het [overeenkomende doel kenmerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)te wijzigen, moet u ervoor zorgen dat de RINGCENTRAL-API het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |userName|Tekenreeks|
   |externalId|Tekenreeks|
   |actief|Booleaans|
   |title|Tekenreeks|
   |e-mail berichten [type EQ "werk]. waarde|Tekenreeks|
   |adressen [type EQ "werk]. land|Tekenreeks|
   |adressen [type EQ "werk]. regio|Tekenreeks|
   |adressen [type EQ "werk]. locatie|Tekenreeks|
   |adressen [type EQ "werk]. post code|Tekenreeks|
   |adressen [type EQ "werk]. streetAddress|Tekenreeks|
   |name. naam|Tekenreeks|
   |naam. familielid|Tekenreeks|
   |phoneNumbers [type EQ "Mobile"]. waarde|Tekenreeks|
   |urn: IETF: params: scim: schemas: extension: Enter prise: 2.0: gebruiker: Department|Tekenreeks|

10. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Als u de Azure AD-inrichtings service voor **RingCentral wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten voor RingCentral door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtingsbereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de initiële synchronisatie cyclus gestart van alle gebruikers en groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . De eerste cyclus duurt langer dan volgende cycli, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende bronnen om uw implementatie te bewaken:

1. De [inrichtings logboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) gebruiken om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangs balk](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtings cyclus te bekijken en te bepalen hoe dicht deze is voltooid
3. Als de inrichtings configuratie een slechte status heeft, gaat de toepassing in quarantaine. Meer informatie over de quarantaine statussen [vindt u hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="change-log"></a>Wijzigingenlogboek

* 09/10/2020-ondersteuning voor kenmerken displayName en Manager verwijderd.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../manage-apps/check-status-user-account-provisioning.md)