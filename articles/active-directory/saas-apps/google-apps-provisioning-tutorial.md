---
title: 'Zelf studie: G Suite configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts vanuit Azure AD naar G suite.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2020
ms.author: Zhchia
ms.openlocfilehash: 5ecf8ca6e6790b218216aed9a6ff82c8a7ac9dd2
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85250142"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Zelf studie: G Suite configureren voor automatische gebruikers inrichting

In deze zelf studie worden de stappen beschreven die u moet uitvoeren in zowel G suite als Azure Active Directory (Azure AD) voor het configureren van automatische gebruikers inrichting. Wanneer deze is geconfigureerd, worden gebruikers en [groepen door Azure](https://gsuite.google.com/) AD automatisch ingericht en ongedaan gemaakt met behulp van de Azure AD-inrichtings service. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen. 

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.

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

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-Tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Een gebruikers account in azure AD met [toestemming](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) voor het configureren van inrichting (bijvoorbeeld toepassings beheerder, Cloud toepassings beheerder, eigenaar van de toepassing of globale beheerder). 
* [Een G suite-Tenant](https://gsuite.google.com/pricing.html)
* Een gebruikers account op een G suite met beheerders machtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Uw inrichtings implementatie plannen
1. Meer informatie over [de werking van de inrichtings service](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bepaal wie binnen het [bereik van de inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)valt.
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

Voeg G suite toe vanuit de Azure AD-toepassings galerie om het beheer van de inrichting te starten met G suite. Als u eerder een installatie van G suite voor SSO hebt, kunt u dezelfde toepassing gebruiken. Het is echter raadzaam dat u een afzonderlijke app maakt wanneer u de integratie in eerste instantie test. Meer informatie over het toevoegen van een toepassing uit [de galerie.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie binnen het bereik van de inrichting valt 

Met de Azure AD-inrichtings service kunt u bereiken die worden ingericht op basis van de toewijzing aan de toepassing en of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van de toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen toe te wijzen aan de toepassing. Als u kiest voor het bereik dat alleen wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereik filter gebruiken zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan G suite, moet u een andere rol dan **standaard toegang**selecteren. Gebruikers met de rol standaard toegang worden uitgesloten van inrichting en worden gemarkeerd als niet effectief in de inrichtings Logboeken. Als de enige rol die beschikbaar is op de toepassing de standaard rol Access is, kunt u [het toepassings manifest bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Begin klein. Test met een klein aantal gebruikers en groepen voordat u naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen toe te wijzen aan de app. Wanneer bereik is ingesteld op alle gebruikers en groepen, kunt u een [kenmerk op basis van bereik filteren](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-g-suite"></a>Stap 5. Automatische gebruikers inrichting configureren voor G suite 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in TestApp te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

> [!NOTE]
> Raadpleeg [Directory API](https://developers.google.com/admin-sdk/directory)voor meer informatie over het Directory-API-eind punt van G suite.

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor G suite in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**. Gebruikers moeten zich aanmelden bij portal.azure.com en kunnen geen aad.portal.azure.com gebruiken

    ![De blade Bedrijfstoepassingen](./media/google-apps-provisioning-tutorial/enterprise-applications.png)

    ![Blade Alle toepassingen](./media/google-apps-provisioning-tutorial/all-applications.png)

2. Selecteer in de lijst toepassingen de optie **G suite**.

    ![De koppeling G Suite in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** . Klik op aan de **slag**.

    ![Tabblad inrichten](common/provisioning.png)

      ![De Blade aan de slag](./media/google-apps-provisioning-tutorial/get-started.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. Klik onder de sectie **beheerders referenties** op **autoriseren**. U wordt doorgestuurd naar een dialoog venster voor Google-autorisatie in een nieuw browser venster.

      ![G suite autoriseren](./media/google-apps-provisioning-tutorial/authorize-1.png)

6. Bevestig dat u Azure AD-machtigingen wilt verlenen om wijzigingen aan te brengen in uw G suite-Tenant. Selecteer **Accepteren**.

     ![Verificatie van G suite-Tenant](./media/google-apps-provisioning-tutorial/gapps-auth.png)

7. Klik in de Azure Portal op **verbinding testen** om te controleren of Azure AD verbinding kan maken met G suite. Als de verbinding mislukt, controleert u of uw G suite-account beheerders machtigingen heeft en probeer het opnieuw. Probeer de stap vervolgens opnieuw te **autoriseren** .

6. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje **e-mail melding verzenden wanneer een fout optreedt** in.

    ![E-mail melding](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers inrichten**.

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar G suite in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in G suite voor bijwerk bewerkingen. Als u ervoor kiest om het [overeenkomende doel kenmerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)te wijzigen, moet u ervoor zorgen dat de G suite-API het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |primaryEmail|Tekenreeks|
   |tussen. [type EQ "Manager"]. waarde|Tekenreeks|
   |naam. familielid|Tekenreeks|
   |name. naam|Tekenreeks|
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
      |naam|Tekenreeks|
      |description|Tekenreeks|

12. Raadpleeg de volgende instructies in de [zelf studie](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

13. Als u de Azure AD-inrichtings service voor G Suite wilt inschakelen, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor G suite door de gewenste waarden in het **bereik** in het gedeelte **instellingen** te kiezen.

    ![Inrichtings bereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de initiële synchronisatie cyclus gestart van alle gebruikers en groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . De eerste cyclus duurt langer dan volgende cycli, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd.

> [!NOTE]
> Als de gebruikers al een bestaand persoonlijke/consument-account hebben dat het e-mail adres van de Azure AD-gebruiker heeft, kan dit een probleem veroorzaken dat kan worden opgelost met behulp van het hulp programma Google-overdracht voordat de Directory synchronisatie wordt uitgevoerd.

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende bronnen om uw implementatie te bewaken:

1. De [inrichtings logboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) gebruiken om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangs balk](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtings cyclus te bekijken en te bepalen hoe dicht deze is voltooid
3. Als de inrichtings configuratie een slechte status heeft, gaat de toepassing in quarantaine. Meer informatie over de quarantaine statussen [vindt u hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../manage-apps/check-status-user-account-provisioning.md)
