---
title: 'Zelf studie: zoomen configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van gebruikers accounts vanuit Azure AD om in te zoomen.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 06/3/2019
ms.author: Zhchia
ms.openlocfilehash: f5d76168b75d9352898d8f0abf80a6ccfb83af30
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91333440"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Zelf studie: zoomen configureren voor automatische gebruikers inrichting

In deze zelf studie worden de stappen beschreven die u moet uitvoeren in zowel in-/uitzoomen als Azure Active Directory (Azure AD) om het automatisch inrichten van gebruikers te configureren. Wanneer u deze configureert, worden gebruikers en groepen automatisch door Azure AD ingericht en ongedaan gemaakt om te [zoomen](https://zoom.us/pricing/) met behulp van de Azure AD-inrichtings service. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers in-/uitzoomen maken
> * Gebruikers in zoomen verwijderen wanneer ze niet meer toegang nodig hebben
> * Gebruikers kenmerken gesynchroniseerd laten tussen Azure AD en zoomen
> * [Eenmalige aanmelding om in](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-tutorial) te zoomen (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Een gebruikersaccount in Azure AD met [machtigingen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) voor het configureren van inrichting (bijvoorbeeld toepassingsbeheerder, cloud-toepassingsbeheerder, toepassingseigenaar of globale beheerder). 
* [Een zoom-Tenant](https://zoom.us/pricing).
* Een gebruikers account in-/uitzoomen met beheerders machtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en zoom](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-zoom-to-support-provisioning-with-azure-ad"></a>Stap 2. Inzoomen configureren voor ondersteuning van inrichting met Azure AD

1. Meld u aan bij de [Zoom-beheer console](https://zoom.us/signin). Navigeer naar **Advanced > app Marketplace** in het navigatie deel venster links.

    ![Zoom integraties](media/zoom-provisioning-tutorial/zoom01.png)

2. Navigeer naar **beheren** in de rechter bovenhoek van de pagina. 

    ![Scherm afbeelding van de zoom app Marketplace met de optie beheren.](media/zoom-provisioning-tutorial/zoom02.png)

3. Navigeer naar uw Azure AD-app die u hebt gemaakt. 
    
    ![Scherm opname van de sectie gemaakte apps met de Azure A D-app die is aangeroepen.](media/zoom-provisioning-tutorial/zoom03.png)

4. Selecteer **app-referenties** in het navigatie deel venster links.

    ![Scherm afbeelding van het navigatie deel venster links met de optie voor app-referenties gemarkeerd.](media/zoom-provisioning-tutorial/zoom04.png)

5. Kopieer het JWT- **token**en sla het op. Deze waarde wordt ingevoerd in het veld **geheime token** op het tabblad inrichten van de zoom toepassing in de Azure Portal. Als u een nieuw niet-verlopend token nodig hebt, moet u de verval tijd opnieuw configureren waarmee automatisch een nieuw token wordt gegenereerd. 

    ![Scherm afbeelding van de pagina met app-referenties.](media/zoom-provisioning-tutorial/zoom05.png)

## <a name="step-3-add-zoom-from-the-azure-ad-application-gallery"></a>Stap 3. Inzoomen toevoegen vanuit de Azure AD-toepassings galerie

U kunt inzoomen toevoegen vanuit de Azure AD-toepassings galerie om het beheer van de inrichting te starten om in te zoomen. Als u eerder inzoomen hebt ingesteld voor SSO, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst om in te zoomen, moet u een andere rol dan de **standaard toegang**selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-zoom"></a>Stap 5. Automatische gebruikers inrichting configureren om in te zoomen 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in TestApp te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

### <a name="to-configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor inzoomen in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Zoom** in de lijst met toepassingen.

    ![De koppeling Zoom in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **Inrichten**.

    ![Scherm opname van de opties voor beheer met de inrichtings optie.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Scherm afbeelding van de vervolg keuzelijst voor de inrichtings modus met de automatische optie aangeroepen.](common/provisioning-automatic.png)

5. Voer in het gedeelte **beheerders referenties** de `https://api.zoom.us/scim` URL van de **Tenant**in. Voer de **JWT-token** waarde in die eerder is opgehaald in het **geheime token**. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met Inzoomen. Als de verbinding mislukt, zorg er dan voor dat uw zoom account beheerders machtigingen heeft en probeer het opnieuw.

    ![Inzoom inrichting](./media/zoom-provisioning-tutorial/provisioning.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en schakel het selectievakje **Een e-mailmelding verzenden als een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **synchroniseren Azure Active Directory gebruikers om in te zoomen**.

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD om in te zoomen in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in zoomen voor bijwerk bewerkingen. Als u ervoor kiest om het [overeenkomende doel kenmerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)te wijzigen, moet u ervoor zorgen dat de zoom-API het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |userName|Tekenreeks|
   |actief|Booleaans|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |e-mail berichten [type EQ "werk]|Tekenreeks|
   |urn: IETF: params: scim: schemas: extension: Enter prise: 2.0: gebruiker: Department|Tekenreeks|

10. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Als u de Azure AD-inrichtings service voor inzoomen wilt inschakelen, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten om in te zoomen door de gewenste waarden in het **bereik** in het gedeelte **instellingen** te kiezen.

    ![Inrichtingsbereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële cyclus duurt langer dan volgende cycli, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

1. Gebruik de [inrichtingslogboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangsbalk](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid
3. Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) voor meer informatie over quarantainestatussen.  

## <a name="connector-limitations"></a>Connectorbeperkingen
* Als u inzoomt, worden er momenteel Maxi maal 9.999 basis gebruikers toegestaan.

## <a name="change-log"></a>Wijzigingenlogboek
* 05/14/2020-ondersteuning voor UPDATE bewerkingen toegevoegd voor e-mails [type EQ "werk] kenmerk.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../manage-apps/check-status-user-account-provisioning.md)
