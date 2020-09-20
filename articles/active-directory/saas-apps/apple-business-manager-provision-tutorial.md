---
title: 'Zelf studie: Apple Business Manager configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts vanuit Azure AD naar Apple Business Manager.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 4ad30031-9904-4ac3-a4d2-e8c28d44f319
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2020
ms.author: Zhchia
ms.openlocfilehash: c4ac4a17e577ea69f4359e1e9b321e5fcc839697
ms.sourcegitcommit: 4ce82b6df65ebd81157b6168d3aa4e7323355022
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2020
ms.locfileid: "90761497"
---
# <a name="tutorial-configure-apple-business-manager-for-automatic-user-provisioning"></a>Zelf studie: Apple Business Manager configureren voor automatische gebruikers inrichting



In deze zelf studie worden de stappen beschreven die u moet uitvoeren in zowel Apple Business Manager als Azure Active Directory (Azure AD) voor het configureren van automatische gebruikers inrichting. Wanneer de configuratie is geconfigureerd, worden gebruikers door Azure AD automatisch ingericht en ongedaan [gemaakt met behulp van](https://business.apple.com/) de Azure AD-inrichtings service. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md). 

## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in Apple Business Manager
> * Gebruikers in Apple Business Manager verwijderen wanneer ze geen toegang meer nodig hebben
> * Gebruikers kenmerken gesynchroniseerd laten tussen Azure AD en Apple Business Manager

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-Tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Een gebruikers account in azure AD met [toestemming](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) voor het configureren van inrichting (bijvoorbeeld toepassings beheerder, Cloud toepassings beheerder, eigenaar van de toepassing of globale beheerder).
* Een Apple Business Manager-account met de rol van beheerder of personen Manager.

> [!NOTE]
> Token overdracht naar Azure AD en het tot stand brengen van een geslaagde verbinding moet in 4 kalender dagen worden voltooid of het proces moet opnieuw worden gestart.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Uw inrichtings implementatie plannen
1. Meer informatie over [de werking van de inrichtings service](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bepaal wie binnen het [bereik van de inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)valt.
3. Bepalen welke gegevens moeten worden [toegewezen tussen Azure AD en Apple Business Manager](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-apple-business-manager-to-support-provisioning-with-azure-ad"></a>Stap 2. Apple Business Manager configureren voor ondersteuning bij het inrichten met Azure AD

1. Meld u in Apple Business Manager aan met een account dat de rol beheerder of personen Manager heeft.
2. Klik op instellingen onder aan de zijbalk op gegevens bron onder organisatie-instellingen en klik vervolgens op verbinding maken met gegevens bron.
3. Klik op verbinding maken naast SCIM, lees zorgvuldig de waarschuwing, klik op kopiëren en klik vervolgens op sluiten.
[Het venster verbinding maken met SCIM, dat een token en een Kopieer knop bevat.] Sluit dit venster om de Tenant-URL te kopiëren van Apple Business Manager naar Azure AD. Dit is: https://federation.apple.com/feeds/business/scim

    ![Apple Business Manager](media/applebusinessmanager-provisioning-tutorial/scim-token.png)

> [!NOTE]
> Het geheime token mag niet worden gedeeld met iemand anders dan de Azure AD-beheerder.

## <a name="step-3-add-apple-business-manager-from-the-azure-ad-application-gallery"></a>Stap 3. Apple Business Manager toevoegen vanuit de Azure AD-toepassings galerie

Voeg Apple Business Manager toe vanuit de Azure AD-toepassings galerie om het beheer van de inrichting van Apple Business Manager te starten. Als u voor het eerst Apple Business Manager voor SSO hebt ingesteld, kunt u dezelfde toepassing gebruiken. Het is echter raadzaam dat u een afzonderlijke app maakt wanneer u de integratie in eerste instantie test. Meer informatie over het toevoegen van een toepassing uit [de galerie.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie binnen het bereik van de inrichting valt 

Met de Azure AD-inrichtings service kunt u bereiken die worden ingericht op basis van de toewijzing aan de toepassing en of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van de toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen toe te wijzen aan de toepassing. Als u kiest voor het bereik dat alleen wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereik filter gebruiken zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)wordt beschreven. 

* Wanneer u gebruikers toewijst aan Apple Business Manager, moet u een andere rol dan **standaard toegang**selecteren. Gebruikers met de rol standaard toegang worden uitgesloten van inrichting en worden gemarkeerd als niet effectief in de inrichtings Logboeken. Als de enige rol die beschikbaar is op de toepassing de standaard rol Access is, kunt u [het toepassings manifest bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Begin klein. Test met een klein aantal gebruikers en groepen voordat u naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen toe te wijzen aan de app. Wanneer bereik is ingesteld op alle gebruikers en groepen, kunt u een [kenmerk op basis van bereik filteren](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)opgeven. 

## <a name="step-5-configure-automatic-user-provisioning-to-apple-business-manager"></a>Stap 5. Automatische gebruikers inrichting configureren voor Apple Business Manager

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Apple Business Manager**.

    ![Apple Business Manager in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Tabblad inrichten automatisch](common/provisioning-automatic.png)

5. In het gedeelte **beheerders referenties** voert u de **scim 2,0-basis-URL en toegangs token** waarden in die respectievelijk zijn opgehaald van Apple Business Manager in de **Tenant-URL** en het **geheime token** . Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met Apple Business Manager. Als de verbinding mislukt, controleert u of uw Apple Business Manager-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

> [!NOTE]
>Als de verbinding tot stand is gebracht, toont Apple Business Manager de SCIM-verbinding als actief. Dit proces kan Maxi maal 60 seconden duren voordat Apple Business Manager de nieuwste verbindings status weerspiegelt.

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en vink het vakje **Een e-mailmelding verzenden als een fout optreedt** aan.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Apple Business Manager**.

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Apple Business Manager in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Apple Business Manager voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |actief|Booleaans|
   |userName|Tekenreeks|
   |name. naam|Tekenreeks|
   |naam. familielid|Tekenreeks|
   |name. naam|Tekenreeks|
   |externalId|Tekenreeks|
   |landinstelling|Tekenreeks|
   |tijd zone|Tekenreeks|
   |urn: IETF: params: scim: schemas: extensie: Enter prise: 2.0: gebruiker: employeeNumber|Tekenreeks|
   |urn: IETF: params: scim: schemas: extensie: Enter prise: 2.0: gebruiker: costCenter|Tekenreeks|
   |urn: IETF: params: scim: schemas: extensie: Enter prise: 2.0: gebruiker: deling|Tekenreeks|
   |urn: IETF: params: scim: schemas: extension: Enter prise: 2.0: gebruiker: Department|Tekenreeks|

10. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Als u de Azure AD-inrichtings service voor Apple Business Manager wilt inschakelen, **wijzigt u de** **inrichtings status** in in het gedeelte instellingen.

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten voor Apple Business Manager door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtingsbereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële synchronisatie duurt langer dan volgende synchronisaties, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd.

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende bronnen om uw implementatie te bewaken:

1. De [inrichtings logboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) gebruiken om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangs balk](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtings cyclus te bekijken en te bepalen hoe dicht deze is voltooid
3. Als de inrichtings configuratie een slechte status heeft, gaat de toepassing in quarantaine. Meer informatie over de quarantaine statussen [vindt u hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
* [SCIM-vereisten voor Apple Business Manager controleren](URL=https://support.apple.com/guide/apple-business-manager/apdd88331cd6)
* [Hoe een persoons-ID wordt gebruikt in Apple Business Manager](URL=https://support.apple.com/guide/apple-business-manager/apd69e1e48e9)
* [SCIM gebruiken om gebruikers te importeren in Apple Business Manager](URL=https://support.apple.com/guide/apple-business-manager/apd3ec7b95ad)
* [Conflicten met SCIM-gebruikers accounts oplossen in Apple Business Manager](URL=https://support.apple.com/guide/apple-business-manager/apd313013d12)
* [Azure AD-accounts verwijderen die worden weer gegeven in Apple Business Manager](URL=https://support.apple.com/guide/apple-business-manager/apdaa5798fbe)
* [SCIM-activiteit in Apple Business Manager weer geven](URL=https://support.apple.com/guide/apple-business-manager/apd1bfd8dfde)
* [Bestaande SCIM-token en-verbindingen beheren in Apple Business Manager](URL=https://support.apple.com/guide/apple-business-manager/apdc9a823611)
* [De scim-verbinding verbreken in Apple Business Manager](URL=https://support.apple.com/guide/apple-business-manager/apd609be3a61) *  [Bestaande scim-token en-verbindingen beheren in Apple Business Manager](URL=https://support.apple.com/guide/apple-business-manager/apdc9a8236e9)
* [Problemen met de SCIM-verbinding in Apple Business Manager oplossen](URL=https://support.apple.com/guide/apple-business-manager/apd403a0f3bd)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../manage-apps/check-status-user-account-provisioning.md)

