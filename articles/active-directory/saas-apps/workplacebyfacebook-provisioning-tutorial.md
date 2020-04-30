---
title: 'Zelf studie: werk plek configureren via Facebook voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Workplace by Facebook configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/28/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99103c9994b240e2f45b66acf269b320c90e5135
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231727"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Zelf studie: werk plek op Facebook configureren voor automatische gebruikers inrichting

In deze zelf studie worden de stappen beschreven die u moet uitvoeren op zowel de werk plek van Facebook als Azure Active Directory (Azure AD) voor het configureren van automatische gebruikers inrichting. Indien geconfigureerd, worden gebruikers en groepen door Azure AD automatisch ingericht en ongedaan gemaakt met behulp [van](https://work.workplace.com/) de Azure AD-inrichtings service. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.

## <a name="migrating-to-the-new-workplace-by-facebook-application"></a>Migreren naar de nieuwe werk plek via Facebook-toepassing
Als u een bestaande integratie met behulp van Facebook hebt, raadpleegt u de onderstaande sectie over de wijzigingen die worden weer gegeven. Als u de werk plek voor de eerste keer instelt op Facebook, kunt u deze sectie overs Laan en naar de ondersteunde mogelijkheden gaan. 

#### <a name="whats-changing"></a>Wat wordt er gewijzigd?
* Wijzigingen aan de kant van Azure AD: de autorisatie methode voor het inrichten van gebruikers op de werk plek heeft in het verleden een geheim token met een lange levens duur. U ziet binnenkort dat de autorisatie methode is gewijzigd in de toekenning van OAuth-autorisatie. 
* Wijzigingen aan de werk plek: voorheen was de Azure AD-app een aangepaste integratie in werk plek via Facebook. Nu ziet u Azure AD in de map werk plek integratie als een toepassing van derden. 

#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>Wat moet ik doen om mijn bestaande aangepaste integratie naar de nieuwe toepassing te migreren?
Als u een bestaande werk plek hebt geïntegreerd met een geldig token, is er geen actie vereist. **Vanaf 04/28/2020 zijn alle toepassingen die niet in quarantaine zijn geplaatst automatisch gemigreerd als gevolg van ongeldige referenties.**
 
#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Hoe kan ik zien of mijn toepassing is gemigreerd? 
* In azure portal: wanneer uw toepassing wordt gemigreerd, wordt de banner in het gedeelte autorisatie van aanstaande wijzigingen verwijderd en wordt het veld geheim token vervangen door een knop met een blauwe machtiging. 
* Bekijk de Azure AD-app op de werk plek van de Facebook-Portal om te controleren of deze is goedgekeurd.  

#### <a name="the-admin-credentials-section-is-greyed-out-on-my-application-and-i-cant-save-why"></a>De sectie beheerders referenties is grijs weer gegeven in mijn toepassing en kan niet worden opgeslagen. Hoe komt dat?
De sectie beheerders referenties is vergrendeld voor werk plek-klanten die niet zijn gemigreerd. Gebruik de volgende URL als de sectie beheerders referenties grijs wordt weer gegeven en u de toegang opnieuw moet verlenen. **? Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride = True** (https://portal.azure.com/?Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=true)


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers in werk plek maken op Facebook
> * Gebruikers in werk plek verwijderen via Facebook wanneer ze geen toegang meer nodig hebben
> * Gebruikers kenmerken gesynchroniseerd blijven tussen Azure AD en werk plek via Facebook
> * [Eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/saas-apps/workplacebyfacebook-tutorial) bij werk plek via Facebook (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-Tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Een gebruikers account in azure AD met [toestemming](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) voor het configureren van inrichting (bijvoorbeeld de toepassings beheerder, de beheerder van de Cloud toepassing, de eigenaar van de toepassing of de globale beheerder)
* Een werk plek met een Facebook-abonnement dat is ingeschakeld voor eenmalige aanmelding

> [!NOTE]
> Als u de stappen in deze zelfstudie wilt testen, is het raadzaam om niet de productieomgeving te gebruiken.

Volg deze aanbevelingen als u de stappen in deze zelfstudie wilt testen:

- Gebruik niet de productieomgeving, tenzij dit echt nodig is.
- Als u geen Azure AD-proef omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/)een proef versie van één maand krijgen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Step 1. Uw inrichtings implementatie plannen
1. Meer informatie over [de werking van de inrichtings service](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bepaal wie binnen het [bereik van de inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)valt.
3. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en werk plek via Facebook](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>Stap 2. Werk plek via Facebook configureren ter ondersteuning van de inrichting van Azure AD

Voordat u de inrichtings service configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw werk plek via Facebook app. Nadat u hebt besloten, kunt u deze gebruikers aan uw werk plek toewijzen via de Facebook-app door de volgende instructies te volgen:

*   U wordt aangeraden één Azure AD-gebruiker toe te wijzen op werk plek via Facebook om de inrichtings configuratie te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

*   Wanneer u een gebruiker toewijst aan werk plek via Facebook, moet u een geldige gebruikersrol selecteren. De rol ' standaard toegang ' werkt niet voor het inrichten.

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>Stap 3. Werk plek toevoegen via Facebook vanuit de Azure AD-toepassings galerie

Voeg werk plek toe via Facebook vanuit de Azure AD-toepassings galerie om het beheer van de inrichting op de werk plek via Facebook te starten. Als u de werk plek eerder hebt ingesteld via Facebook voor SSO, kunt u dezelfde toepassing gebruiken. Het is echter raadzaam dat u een afzonderlijke app maakt wanneer u de integratie in eerste instantie test. Meer informatie over het toevoegen van een toepassing uit [de galerie.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie binnen het bereik van de inrichting valt 

Met de Azure AD-inrichtings service kunt u bereiken die worden ingericht op basis van de toewijzing aan de toepassing en of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van de toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen toe te wijzen aan de toepassing. Als u kiest voor het bereik dat alleen wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereik filter gebruiken zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan werk plek via Facebook, moet u een andere rol dan **standaard toegang**selecteren. Gebruikers met de rol standaard toegang worden uitgesloten van inrichting en worden gemarkeerd als niet effectief in de inrichtings Logboeken. Als de enige rol die beschikbaar is op de toepassing de standaard rol Access is, kunt u [het toepassings manifest bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Begin klein. Test met een klein aantal gebruikers en groepen voordat u naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen toe te wijzen aan de app. Wanneer bereik is ingesteld op alle gebruikers en groepen, kunt u een [kenmerk op basis van bereik filteren](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)opgeven. 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **werk plek op Facebook**.

    ![De koppeling Workplace by Facebook in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. Klik onder de sectie **beheerders referenties** op **autoriseren**. U wordt omgeleid naar werk plek op de autorisatie pagina van Facebook. Voer uw werk plek in op Facebook-gebruikers naam en klik op de knop **door gaan** . Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met werk plek via Facebook. Als de verbinding mislukt, zorg er dan voor dat uw werk plek op Facebook-account beheerders machtigingen heeft en probeer het opnieuw.

    ![inrichtings](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![autoriseren](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje **e-mail melding verzenden wanneer een fout optreedt** in.

    ![E-mail melding](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers op de werk plek synchroniseren met Facebook**.

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD op werk plek via Facebook in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om de gebruikers accounts in werk plek van Facebook te vergelijken voor update bewerkingen. Als u ervoor kiest om het [overeenkomende doel kenmerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)te wijzigen, moet u ervoor zorgen dat de werk plek van de Facebook-API het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |userName|Tekenreeks|
   |displayName|Tekenreeks|
   |actief|Booleaans|
   |titel|Booleaans|
   |e-mail berichten [type EQ "werk]. waarde|Tekenreeks|
   |name. naam|Tekenreeks|
   |naam. familielid|Tekenreeks|
   |naam. opgemaakt|Tekenreeks|
   |adressen [type EQ "werk]. opgemaakt|Tekenreeks|
   |adressen [type EQ "werk]. streetAddress|Tekenreeks|
   |adressen [type EQ "werk]. locatie|Tekenreeks|
   |adressen [type EQ "werk]. regio|Tekenreeks|
   |adressen [type EQ "werk]. land|Tekenreeks|
   |adressen [type EQ "werk]. post code|Tekenreeks|
   |adressen [type EQ "other"]. Format|Tekenreeks|
   |phoneNumbers [type EQ "werk]. waarde|Tekenreeks|
   |phoneNumbers [type EQ "Mobile"]. waarde|Tekenreeks|
   |phoneNumbers [type EQ "fax"]. waarde|Tekenreeks|
   |externalId|Tekenreeks|
   |preferredLanguage|Tekenreeks|
   |urn: IETF: params: scim: schemas: extensie: Enter prise: 2.0: gebruiker: Manager|Tekenreeks|
   |urn: IETF: params: scim: schemas: extension: Enter prise: 2.0: gebruiker: Department|Tekenreeks|

10. Raadpleeg de volgende instructies in de [zelf studie](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

11. Als u de Azure AD-inrichtings service voor werk plek wilt inschakelen op Facebook, wijzigt **u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten op werk plek via Facebook door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtings bereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de initiële synchronisatie cyclus gestart van alle gebruikers en groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . De eerste cyclus duurt langer dan volgende cycli, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende bronnen om uw implementatie te bewaken:

1. De [inrichtings logboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) gebruiken om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangs balk](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtings cyclus te bekijken en te bepalen hoe dicht deze is voltooid
3. Als de inrichtings configuratie een slechte status heeft, gaat de toepassing in quarantaine. Meer informatie over de quarantaine statussen [vindt u hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing
*  Als een gebruiker niet met succes is gemaakt en er een controle logboek gebeurtenis is met de code ' 1789003 ', betekent dit dat de gebruiker afkomstig is van een niet-geverifieerd domein.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../manage-apps/check-status-user-account-provisioning.md)
