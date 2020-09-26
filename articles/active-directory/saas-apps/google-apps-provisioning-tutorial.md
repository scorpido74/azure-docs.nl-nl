---
title: 'Zelf studie: G Suite configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts vanuit Azure AD naar G suite.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/06/2020
ms.author: Zhchia
ms.openlocfilehash: 3f2f62fe158b946e00c7f81d0cb7eeb0d8f09437
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91331124"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Zelf studie: G Suite configureren voor automatische gebruikers inrichting

In deze zelf studie worden de stappen beschreven die u moet uitvoeren in zowel G suite als Azure Active Directory (Azure AD) voor het configureren van automatische gebruikers inrichting. Wanneer deze is geconfigureerd, worden gebruikers en [groepen door Azure](https://gsuite.google.com/) AD automatisch ingericht en ongedaan gemaakt met behulp van de Azure AD-inrichtings service. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md). 

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).

> [!NOTE]
> De G suite-connector is onlangs bijgewerkt op oktober 2019. De volgende wijzigingen zijn aangebracht in de G suite-connector:
>
> * Er is ondersteuning toegevoegd voor aanvullende gebruikers-en groeps kenmerken van G suite.
> * De namen van de doel kenmerken van G suite zijn bijgewerkt zodat deze overeenkomen met wat [hier](https://developers.google.com/admin-sdk/directory)is gedefinieerd.
> * De standaard kenmerk toewijzingen zijn bijgewerkt.

## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in G suite
> * Gebruikers in G Suite verwijderen wanneer ze geen toegang meer nodig hebben
> * Gebruikers kenmerken gesynchroniseerd laten tussen Azure AD en G suite
> * Inrichtings groepen en groepslid maatschappen in G suite
> * [Eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial) bij G suite (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-Tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Een gebruikersaccount in Azure AD met [machtigingen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) voor het configureren van inrichting (bijvoorbeeld toepassingsbeheerder, cloud-toepassingsbeheerder, toepassingseigenaar of globale beheerder). 
* [Een G suite-Tenant](https://gsuite.google.com/pricing.html)
* Een gebruikers account op een G suite met beheerders machtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en G suite](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-g-suite-to-support-provisioning-with-azure-ad"></a>Stap 2. G Suite configureren ter ondersteuning van het inrichten met Azure AD

Voordat u G suite configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SCIM inrichten inschakelen op G suite.

1. Meld u aan bij de [G suite-beheer console](https://admin.google.com/) met uw beheerders account en selecteer vervolgens **beveiliging**. Als de koppeling niet wordt weer gegeven, kan deze worden verborgen onder het menu **meer besturings elementen** onder aan het scherm.

    ![G suite-beveiliging](./media/google-apps-provisioning-tutorial/gapps-security.png)

2. Selecteer op de pagina **beveiliging** **API-verwijzing**.

    ![G suite-API](./media/google-apps-provisioning-tutorial/gapps-api.png)

3. Selecteer **API-toegang inschakelen**.

    ![G suite API ingeschakeld](./media/google-apps-provisioning-tutorial/gapps-api-enabled.png)

    > [!IMPORTANT]
   > Voor elke gebruiker die u wilt inrichten voor G suite, **moet** de gebruikers naam in azure AD zijn gekoppeld aan een aangepast domein. Gebruikers namen die eruitzien, worden bijvoorbeeld bob@contoso.onmicrosoft.com niet geaccepteerd door G suite. Daarentegen bob@contoso.com wordt geaccepteerd. U kunt een bestaand gebruikers domein wijzigen door de instructies [hier](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)te volgen.

4. Nadat u de gewenste aangepaste domeinen hebt toegevoegd en geverifieerd met Azure AD, moet u ze opnieuw controleren met G suite. Raadpleeg de volgende stappen om domeinen in G suite te controleren:

    a. Selecteer in de [G suite-beheer console](https://admin.google.com/)de optie **domeinen**.

    ![G suite-domeinen](./media/google-apps-provisioning-tutorial/gapps-domains.png)

    b. Selecteer **een domein of domein alias toevoegen**.

    ![G suite toevoegen domein](./media/google-apps-provisioning-tutorial/gapps-add-domain.png)

    c. Selecteer **een ander domein toevoegen**en typ de naam van het domein dat u wilt toevoegen.

    ![G suite nog een toevoegen](./media/google-apps-provisioning-tutorial/gapps-add-another.png)

    d. Selecteer **door gaan en domein eigendom verifiëren**. Volg vervolgens de stappen om te controleren of u eigenaar bent van de domein naam. Zie [uw site eigendom controleren](https://support.google.com/webmasters/answer/35179)voor uitgebreide instructies voor het verifiëren van uw domein met Google.

    e. Herhaal de voor gaande stappen voor extra domeinen die u wilt toevoegen aan G suite.

5. Bepaal vervolgens welk beheerders account u wilt gebruiken voor het beheren van gebruikers inrichten in G suite. Navigeer naar **beheerders rollen**.

    ![G suite-beheerder](./media/google-apps-provisioning-tutorial/gapps-admin.png)

6. Voor de **beheerdersrol** van dat account bewerkt u de **bevoegdheden** voor die rol. Zorg ervoor dat u alle **beheer-API-bevoegdheden** inschakelt, zodat dit account kan worden gebruikt voor het inrichten.

    ![G suite-beheerders bevoegdheden](./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png)

## <a name="step-3-add-g-suite-from-the-azure-ad-application-gallery"></a>Stap 3. G suite toevoegen vanuit de Azure AD-toepassings galerie

Voeg G suite toe vanuit de Azure AD-toepassings galerie om het beheer van de inrichting te starten met G suite. Als u eerder een installatie van G suite voor SSO hebt, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan G suite, moet u een andere rol dan **standaard toegang**selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-g-suite"></a>Stap 5. Automatische gebruikers inrichting configureren voor G suite 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in TestApp te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

> [!NOTE]
> Raadpleeg [Directory API](https://developers.google.com/admin-sdk/directory)voor meer informatie over het Directory-API-eind punt van G suite.

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor G suite in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**. Gebruikers moeten zich aanmelden bij portal.azure.com en kunnen geen aad.portal.azure.com gebruiken

    ![De blade Bedrijfstoepassingen](./media/google-apps-provisioning-tutorial/enterprise-applications.png)

    ![Blade Alle toepassingen](./media/google-apps-provisioning-tutorial/all-applications.png)

2. Selecteer in de lijst toepassingen de optie **G suite**.

    ![De koppeling G Suite in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** . Klik op aan de **slag**.

    ![Scherm opname van de opties voor beheer met de inrichtings optie.](common/provisioning.png)

      ![De Blade aan de slag](./media/google-apps-provisioning-tutorial/get-started.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Scherm afbeelding van de vervolg keuzelijst voor de inrichtings modus met de automatische optie aangeroepen.](common/provisioning-automatic.png)

5. Klik onder de sectie **beheerders referenties** op **autoriseren**. U wordt doorgestuurd naar een dialoog venster voor Google-autorisatie in een nieuw browser venster.

      ![G suite autoriseren](./media/google-apps-provisioning-tutorial/authorize-1.png)

6. Bevestig dat u Azure AD-machtigingen wilt verlenen om wijzigingen aan te brengen in uw G suite-Tenant. Selecteer **Accepteren**.

     ![Verificatie van G suite-Tenant](./media/google-apps-provisioning-tutorial/gapps-auth.png)

7. Klik in de Azure Portal op **verbinding testen** om te controleren of Azure AD verbinding kan maken met G suite. Als de verbinding mislukt, controleert u of uw G suite-account beheerders machtigingen heeft en probeer het opnieuw. Probeer de stap vervolgens opnieuw te **autoriseren** .

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en schakel het selectievakje **Een e-mailmelding verzenden als een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers inrichten**.

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar G suite in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in G suite voor bijwerk bewerkingen. Als u ervoor kiest om het [overeenkomende doel kenmerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)te wijzigen, moet u ervoor zorgen dat de G suite-API het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |primaryEmail|Tekenreeks|
   |tussen. [type EQ "Manager"]. waarde|Tekenreeks|
   |name.familyName|Tekenreeks|
   |name.givenName|Tekenreeks|
   |gebroken|Tekenreeks|
   |externalIds. [type EQ "aangepast"]. waarde|Tekenreeks|
   |externalIds. [type EQ "organisatie"]. waarde|Tekenreeks|
   |komen. [type EQ "werk]. land|Tekenreeks|
   |komen. [type EQ "werk]. streetAddress|Tekenreeks|
   |komen. [type EQ "werk]. regio|Tekenreeks|
   |komen. [type EQ "werk]. locatie|Tekenreeks|
   |komen. [type EQ "werk]. post code|Tekenreeks|
   |eBay. [type EQ "werk]. adres|Tekenreeks|
   |organisaties. [type EQ "werk]. afdeling|Tekenreeks|
   |organisaties. [type EQ "werk]. titel|Tekenreeks|
   |phoneNumbers. [type EQ "werk]. waarde|Tekenreeks|
   |phoneNumbers. [type EQ "Mobile"]. waarde|Tekenreeks|
   |phoneNumbers. [type EQ "work_fax"]. waarde|Tekenreeks|
   |eBay. [type EQ "werk]. adres|Tekenreeks|
   |organisaties. [type EQ "werk]. afdeling|Tekenreeks|
   |organisaties. [type EQ "werk]. titel|Tekenreeks|
   |phoneNumbers. [type EQ "werk]. waarde|Tekenreeks|
   |phoneNumbers. [type EQ "Mobile"]. waarde|Tekenreeks|
   |phoneNumbers. [type EQ "work_fax"]. waarde|Tekenreeks|
   |komen. [type EQ "Home"]. land|Tekenreeks|
   |komen. [type EQ "Home"]. geformatteerd|Tekenreeks|
   |komen. [type EQ "Home"]. locatie|Tekenreeks|
   |komen. [type EQ "Home"]. post code|Tekenreeks|
   |komen. [type EQ "Home"]. regio|Tekenreeks|
   |komen. [type EQ "Home"]. streetAddress|Tekenreeks|
   |komen. [type EQ "Overig"]. land|Tekenreeks|
   |komen. [type EQ "other"]. opgemaakt|Tekenreeks|
   |komen. [type EQ "other"]. locatie|Tekenreeks|
   |komen. [type EQ "Overig"]. post code|Tekenreeks|
   |komen. [type EQ "other"]. regio|Tekenreeks|
   |komen. [type EQ "other"]. streetAddress|Tekenreeks|
   |komen. [type EQ "werk]. opgemaakt|Tekenreeks|
   |changePasswordAtNextLogin|Tekenreeks|
   |eBay. [type EQ "Home"]. adres|Tekenreeks|
   |eBay. [type EQ "other"]. adres|Tekenreeks|
   |externalIds. [type EQ "account"]. waarde|Tekenreeks|
   |externalIds. [type EQ "aangepast"]. customType|Tekenreeks|
   |externalIds. [type EQ "klant"]. waarde|Tekenreeks|
   |externalIds. [type EQ "login_id"]. waarde|Tekenreeks|
   |externalIds. [type EQ "netwerk"]. waarde|Tekenreeks|
   |geslacht. type|Tekenreeks|
   |GeneratedImmutableId|Tekenreeks|
   |Id|Tekenreeks|
   |IMS. [type EQ "Home"]. Protocol|Tekenreeks|
   |IMS. [type EQ "other"]. Protocol|Tekenreeks|
   |IMS. [type EQ "werk]. Protocol|Tekenreeks|
   |includeInGlobalAddressList|Tekenreeks|
   |ipWhitelisted|Tekenreeks|
   |organisaties. [type EQ "school"]. costCenter|Tekenreeks|
   |organisaties. [type EQ "school"]. afdeling|Tekenreeks|
   |organisaties. [type EQ "school"]. domein|Tekenreeks|
   |organisaties. [type EQ "school"]. fullTimeEquivalent|Tekenreeks|
   |organisaties. [type EQ "school"]. locatie|Tekenreeks|
   |organisaties. [type EQ "school"]. naam|Tekenreeks|
   |organisaties. [type EQ "school"]. Symbol|Tekenreeks|
   |organisaties. [type EQ "school"]. title|Tekenreeks|
   |organisaties. [type EQ "werk]. costCenter|Tekenreeks|
   |organisaties. [type EQ "werk]. domein|Tekenreeks|
   |organisaties. [type EQ "werk]. fullTimeEquivalent|Tekenreeks|
   |organisaties. [type EQ "werk]. locatie|Tekenreeks|
   |organisaties. [type EQ "werk]. naam|Tekenreeks|
   |organisaties. [type EQ "werk]. symbool|Tekenreeks|
   |OrgUnitPath|Tekenreeks|
   |phoneNumbers. [type EQ "Home"]. waarde|Tekenreeks|
   |phoneNumbers. [type EQ "other"]. waarde|Tekenreeks|
   |webpagina's. [type EQ "Home"]. waarde|Tekenreeks|
   |webpagina's. [type EQ "other"]. waarde|Tekenreeks|
   |webpagina's. [type EQ "werk]. waarde|Tekenreeks|
   

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen inrichten**.

11. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar G suite in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt voor het vergelijken van de groepen in G suite voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

      |Kenmerk|Type|
      |---|---|
      |e-mail|Tekenreeks|
      |Leden|Tekenreeks|
      |name|Tekenreeks|
      |description|Tekenreeks|

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Als u de Azure AD-inrichtings service voor G Suite wilt inschakelen, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor G suite door de gewenste waarden in het **bereik** in het gedeelte **instellingen** te kiezen.

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële cyclus duurt langer dan volgende cycli, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd.

> [!NOTE]
> Als de gebruikers al een bestaand persoonlijke/consument-account hebben dat het e-mail adres van de Azure AD-gebruiker heeft, kan dit een probleem veroorzaken dat kan worden opgelost met behulp van het hulp programma Google-overdracht voordat de Directory synchronisatie wordt uitgevoerd.

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

1. Gebruik de [inrichtingslogboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangsbalk](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid
3. Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) voor meer informatie over quarantainestatussen.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../manage-apps/check-status-user-account-provisioning.md)
