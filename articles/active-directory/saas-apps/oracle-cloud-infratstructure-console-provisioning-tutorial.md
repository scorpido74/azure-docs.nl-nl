---
title: 'Zelf studie: de Oracle Cloud Infrastructure console configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts van Azure AD naar de Oracle Cloud Infrastructure-console.
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
ms.openlocfilehash: 94f5be93d95cc1c524e6db464cac68879ed0b0e7
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926787"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>Zelf studie: Oracle Cloud Infrastructure console configureren voor automatische gebruikers inrichting

In deze zelf studie worden de stappen beschreven die u moet uitvoeren in zowel de Oracle Cloud Infrastructure console als Azure Active Directory (Azure AD) voor het configureren van automatische gebruikers inrichting. Wanneer de configuratie is geconfigureerd, worden gebruikers en groepen door Azure AD automatisch ingericht en ongedaan gemaakt in [Oracle Cloud Infrastructure console](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) met behulp van de Azure AD-inrichtings service. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen. 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in de Oracle Cloud Infrastructure-console
> * Gebruikers verwijderen in de Oracle Cloud Infrastructure-console wanneer ze geen toegang meer nodig hebben
> * Gebruikers kenmerken gesynchroniseerd laten tussen Azure AD en Oracle Cloud Infrastructure console
> * Inrichtings groepen en groepslid maatschappen in de Oracle Cloud Infrastructure console
> * [Eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial) bij de Oracle-Cloud infrastructuur console (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-Tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Een gebruikers account in azure AD met [toestemming](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) voor het configureren van inrichting (bijvoorbeeld toepassings beheerder, Cloud toepassings beheerder, eigenaar van de toepassing of globale beheerder). 
* Een Oracle Cloud Infrastructure control- [Tenant](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton).
* Een gebruikers account in Oracle Cloud Infrastructure Control met beheerders machtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Uw inrichtings implementatie plannen
1. Meer informatie over [de werking van de inrichtings service](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bepaal wie binnen het [bereik van de inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)valt.
3. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en Oracle Cloud Infrastructure console](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>Stap 2. De Oracle Cloud Infrastructure console configureren ter ondersteuning van het inrichten met Azure AD

1. Meld u aan bij de beheer portal van de Oracle Cloud Infrastructure-console. Navigeer in de linkerbovenhoek van het scherm naar **identiteit > Federatie**.

    ![Oracle-beheerder](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. Klik op de URL die wordt weer gegeven op de pagina naast Oracle Identity Cloud service console.

    ![Oracle-URL](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. Klik op **ID-provider toevoegen** om een nieuwe ID-provider te maken. Sla de IdP-id op die moet worden gebruikt als onderdeel van de Tenant-URL. Klik op het plus pictogram naast het tabblad **toepassingen** om een OAuth-client te maken en IDCS-identiteit domein beheerder AppRole te verlenen.

    ![Pictogram Oracle-Cloud](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. Volg de onderstaande scherm afbeeldingen om uw toepassing te configureren. Zodra de configuratie is voltooid, klikt u op **Opslaan**.

    ![Oracle-configuratie](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![Uitgifte beleid voor Oracle-tokens](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. Vouw op het tabblad configuraties van de toepassing de optie **algemene gegevens** uit om de client-id en het client geheim op te halen.

    ![Oracle-tokens genereren](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. Als u een geheim token base64 wilt genereren, moet u de client-ID en het client geheim in de notatie **client-id: client geheim**coderen. Sla het geheim token op. Deze waarde wordt ingevoerd in het veld **geheime token** op het tabblad inrichten van de Oracle Cloud Infrastructure console-toepassing in de Azure Portal.

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>Stap 3. Een Oracle Cloud Infrastructure console toevoegen vanuit de Azure AD-toepassings galerie

Voeg de Oracle Cloud Infrastructure-console toe vanuit de Azure AD-toepassings galerie om het beheer van de inrichting van de Oracle Cloud Infrastructure console te starten. Als u eerder Oracle Cloud Infrastructure console voor SSO hebt ingesteld, kunt u dezelfde toepassing gebruiken. Het is echter raadzaam dat u een afzonderlijke app maakt wanneer u de integratie in eerste instantie test. Meer informatie over het toevoegen van een toepassing uit [de galerie.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie binnen het bereik van de inrichting valt 

Met de Azure AD-inrichtings service kunt u bereiken die worden ingericht op basis van de toewijzing aan de toepassing en of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van de toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen toe te wijzen aan de toepassing. Als u kiest voor het bereik dat alleen wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereik filter gebruiken zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan de Oracle Cloud Infrastructure-console, moet u een andere rol dan **standaard toegang**selecteren. Gebruikers met de rol standaard toegang worden uitgesloten van inrichting en worden gemarkeerd als niet effectief in de inrichtings Logboeken. Als de enige rol die beschikbaar is op de toepassing de standaard rol Access is, kunt u [het toepassings manifest bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Begin klein. Test met een klein aantal gebruikers en groepen voordat u naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen toe te wijzen aan de app. Wanneer bereik is ingesteld op alle gebruikers en groepen, kunt u een [kenmerk op basis van bereik filteren](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>Stap 5. Automatische gebruikers inrichting configureren voor de Oracle Cloud Infrastructure-console 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in TestApp te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor de Oracle Cloud Infrastructure-console in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Oracle Cloud Infrastructure console**.

    ![De koppeling Oracle Cloud Infrastructure console in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. Geef in de sectie **beheerders referenties** de **Tenant-URL** op in de indeling `https://<IdP ID>.identity.oraclecloud.com/admin/v1` . Bijvoorbeeld `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`. Voer de geheime token waarde in die eerder is opgehaald in het **geheime token**. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met de Oracle Cloud Infrastructure-console. Als de verbinding mislukt, zorg er dan voor dat het account van de Oracle Cloud Infrastructure-console beheerders machtigingen heeft en probeer het opnieuw.

    ![inrichtings](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje **e-mail melding verzenden wanneer een fout optreedt** in.

    ![E-mail melding](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met de Oracle Cloud Infrastructure-console**.

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar de Oracle Cloud Infrastructure-console in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de gebruikers accounts in Oracle Cloud Infrastructure console te vergelijken voor bijwerk bewerkingen. Als u ervoor kiest om het [overeenkomende doel kenmerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)te wijzigen, moet u ervoor zorgen dat de API van de Oracle Cloud Infrastructure console het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

      |Kenmerk|Type|
      |---|---|
      |displayName|Tekenreeks|
      |userName|Tekenreeks|
      |actief|Booleaans|
      |titel|Tekenreeks|
      |e-mail berichten [type EQ "werk]. waarde|Tekenreeks|
      |preferredLanguage|Tekenreeks|
      |name. naam|Tekenreeks|
      |naam. familielid|Tekenreeks|
      |adressen [type EQ "werk]. opgemaakt|Tekenreeks|
      |adressen [type EQ "werk]. locatie|Tekenreeks|
      |adressen [type EQ "werk]. regio|Tekenreeks|
      |adressen [type EQ "werk]. post code|Tekenreeks|
      |adressen [type EQ "werk]. land|Tekenreeks|
      |adressen [type EQ "werk]. streetAddress|Tekenreeks|
      |urn: IETF: params: scim: schemas: extensie: Enter prise: 2.0: gebruiker: employeeNumber|Tekenreeks|
      |urn: IETF: params: scim: schemas: extension: Enter prise: 2.0: gebruiker: Department|Tekenreeks|
      |urn: IETF: params: scim: schemas: extensie: Enter prise: 2.0: gebruiker: costCenter|Tekenreeks|
      |urn: IETF: params: scim: schemas: extensie: Enter prise: 2.0: gebruiker: deling|Tekenreeks|
      |urn: IETF: params: scim: schemas: extensie: Enter prise: 2.0: gebruiker: Manager|Naslaginformatie|
      |urn: IETF: params: scim: schemas: extensie: Enter prise: 2.0: gebruiker: organisatie|Tekenreeks|
      |urn: IETF: params: scim: schemas: Oracle: IDCS: extension: gebruiker: gebruiker: bypassNotification|Booleaans|
      |urn: IETF: params: scim: schemas: Oracle: IDCS: extension: gebruiker: gebruiker: isFederatedUser|Booleaans|

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren met Oracle Cloud Infrastructure console**.

11. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar de Oracle Cloud Infrastructure console in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt voor het vergelijken van de groepen in de Oracle Cloud Infrastructure console voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

      |Kenmerk|Type|
      |---|---|
      |displayName|Tekenreeks|
      |externalId|Tekenreeks|
      |leden|Naslaginformatie|

12. Raadpleeg de volgende instructies in de [zelf studie](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

13. Als u de Azure AD Provisioning Service voor Oracle Cloud Infrastructure console wilt inschakelen, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor de Oracle-Cloud infrastructuur console door de gewenste waarden in het **bereik** in het gedeelte **instellingen** te kiezen.

    ![Inrichtings bereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de initiële synchronisatie cyclus gestart van alle gebruikers en groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . De eerste cyclus duurt langer dan volgende cycli, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende bronnen om uw implementatie te bewaken:

* De [inrichtings logboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) gebruiken om te bepalen welke gebruikers al dan niet met succes zijn ingericht
* Controleer de [voortgangs balk](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtings cyclus te bekijken en te bepalen hoe dicht deze is voltooid
* Als de inrichtings configuratie een slechte status heeft, gaat de toepassing in quarantaine. Meer informatie over de quarantaine statussen [vindt u hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../manage-apps/check-status-user-account-provisioning.md)
