---
title: 'Zelf studie: werk plek configureren via Facebook voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over de stappen die u moet uitvoeren op zowel de werk plek van Facebook als Azure Active Directory (Azure AD) voor het configureren van automatische gebruikers inrichting.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 04/28/2020
ms.author: jeedes
ms.openlocfilehash: c72ca7cb521c5c7e7ed33e9a0539de0df252ac92
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91321897"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Zelf studie: werk plek op Facebook configureren voor automatische gebruikers inrichting

In deze zelf studie worden de stappen beschreven die u moet uitvoeren op zowel de werk plek van Facebook als Azure Active Directory (Azure AD) voor het configureren van automatische gebruikers inrichting. Indien geconfigureerd, worden gebruikers en groepen door Azure AD automatisch ingericht en ongedaan gemaakt met behulp [van](https://work.workplace.com/) de Azure AD-inrichtings service. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers in werk plek maken op Facebook
> * Gebruikers in werk plek verwijderen via Facebook wanneer ze geen toegang meer nodig hebben
> * Gebruikers kenmerken gesynchroniseerd blijven tussen Azure AD en werk plek via Facebook
> * [Eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/saas-apps/workplacebyfacebook-tutorial) bij werk plek via Facebook (aanbevolen)

>[!VIDEO https://www.youtube.com/embed/oF7I0jjCfrY]

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-Tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Een gebruikers account in azure AD met [toestemming](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) voor het configureren van inrichting (bijvoorbeeld de toepassings beheerder, de beheerder van de Cloud toepassing, de eigenaar van de toepassing of de globale beheerder)
* Een werk plek met een Facebook-abonnement dat is ingeschakeld voor eenmalige aanmelding

> [!NOTE]
> Als u de stappen in deze zelfstudie wilt testen, is het raadzaam om niet de productieomgeving te gebruiken.

Volg deze aanbevelingen als u de stappen in deze zelfstudie wilt testen:

- Gebruik niet de productieomgeving, tenzij dit echt nodig is.
- Als u geen Azure AD-proef omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/)een proef versie van één maand krijgen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en werk plek via Facebook](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>Stap 2. Werk plek via Facebook configureren ter ondersteuning van de inrichting van Azure AD

Voordat u de inrichtings service configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw werk plek via Facebook app. Nadat u hebt besloten, kunt u deze gebruikers aan uw werk plek toewijzen via de Facebook-app door de volgende instructies te volgen:

*   U wordt aangeraden één Azure AD-gebruiker toe te wijzen op werk plek via Facebook om de inrichtings configuratie te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

*   Wanneer u een gebruiker toewijst aan werk plek via Facebook, moet u een geldige gebruikersrol selecteren. De rol ' standaard toegang ' werkt niet voor het inrichten.

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>Stap 3. Werk plek toevoegen via Facebook vanuit de Azure AD-toepassings galerie

Voeg werk plek toe via Facebook vanuit de Azure AD-toepassings galerie om het beheer van de inrichting op de werk plek via Facebook te starten. Als u de werk plek eerder hebt ingesteld via Facebook voor SSO, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) voor meer informatie over het toevoegen van een toepassing uit de galerie.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan werk plek via Facebook, moet u een andere rol dan **standaard toegang**selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) opgeven. 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Workplace by Facebook** in de lijst met toepassingen.

    ![De koppeling Workplace by Facebook in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Scherm opname van de opties voor beheer met de inrichtings optie.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Scherm afbeelding van de vervolg keuzelijst voor de inrichtings modus met de automatische optie aangeroepen.](common/provisioning-automatic.png)

5. Klik onder de sectie **beheerders referenties** op **autoriseren**. U wordt omgeleid naar werk plek op de autorisatie pagina van Facebook. Voer uw werk plek in op Facebook-gebruikers naam en klik op de knop **door gaan** . Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met werk plek via Facebook. Als de verbinding mislukt, zorg er dan voor dat uw werk plek op Facebook-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Scherm afbeelding toont het dialoog venster beheerders referenties met een optie voor autoriseren.](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![autoriseren](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en schakel het selectievakje **Een e-mailmelding verzenden als een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers op de werk plek synchroniseren met Facebook**.

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD op werk plek via Facebook in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om de gebruikers accounts in werk plek van Facebook te vergelijken voor update bewerkingen. Als u ervoor kiest om het [overeenkomende doel kenmerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)te wijzigen, moet u ervoor zorgen dat de werk plek van de Facebook-API het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |userName|Tekenreeks|
   |displayName|Tekenreeks|
   |actief|Booleaans|
   |title|Boolean-waarde|
   |emails[type eq "work"].value|Tekenreeks|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |naam. opgemaakt|Tekenreeks|
   |adressen [type EQ "werk]. opgemaakt|Tekenreeks|
   |adressen [type EQ "werk]. streetAddress|Tekenreeks|
   |adressen [type EQ "werk]. locatie|Tekenreeks|
   |adressen [type EQ "werk]. regio|Tekenreeks|
   |adressen [type EQ "werk]. land|Tekenreeks|
   |adressen [type EQ "werk]. post code|Tekenreeks|
   |adressen [type EQ "other"]. Format|Tekenreeks|
   |phoneNumbers[type eq "work"].value|Tekenreeks|
   |phoneNumbers[type eq "mobile"].value|Tekenreeks|
   |phoneNumbers [type EQ "fax"]. waarde|Tekenreeks|
   |externalId|Tekenreeks|
   |preferredLanguage|Tekenreeks|
   |urn:scim:schemas:extension:enterprise:1.0.manager|Tekenreeks|
   |urn:scim:schemas:extension:enterprise:1.0.department|Tekenreeks|
   |urn:scim:schemas:extension:enterprise:1.0.division|Tekenreeks|
   |urn:scim:schemas:extension:enterprise:1.0.organization|Tekenreeks|
   |urn:scim:schemas:extension:enterprise:1.0.costCenter|Tekenreeks|
   |urn:scim:schemas:extension:enterprise:1.0.employeeNumber|Tekenreeks|
   |urn: scim: schemas: extensie: Facebook: auth_method: 1.0: auth_method|Tekenreeks|
   |urn: scim: schemas: extensie: Facebook: Frontline: 1.0. is_frontline|Boolean-waarde|
   |urn: scim: schemas: extensie: Facebook: starttermdates: 1.0. start date|Geheel getal|


10. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Als u de Azure AD-inrichtings service voor werk plek wilt inschakelen op Facebook, wijzigt **u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten op werk plek via Facebook door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtingsbereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële cyclus duurt langer dan volgende cycli, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

1. Gebruik de [inrichtingslogboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangsbalk](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid
3. Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) voor meer informatie over quarantainestatussen.

## <a name="troubleshooting-tips"></a>Tips voor probleemoplossing
*  Als een gebruiker niet met succes is gemaakt en er een controle logboek gebeurtenis is met de code ' 1789003 ', betekent dit dat de gebruiker afkomstig is van een niet-geverifieerd domein.

## <a name="change-log"></a>Wijzigingenlogboek

* 09/10/2020-ondersteuning toegevoegd voor ondernemings kenmerken ' divisie ', ' organisatie ', ' costCenter ' en ' employeeNumber '. Er is ondersteuning toegevoegd voor aangepaste kenmerken "start date", "auth_method" en "Frontline"

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../manage-apps/check-status-user-account-provisioning.md)
