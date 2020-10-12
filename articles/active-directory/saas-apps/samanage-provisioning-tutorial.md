---
title: 'Zelf studie: SolarWinds Service Desk (voorheen SAManage) configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts van Azure AD naar SolarWinds Service Desk (voorheen SAManage).
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: 0941c95ee6215a710ebb1bbc2fba9fae09ccf16d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90707263"
---
# <a name="tutorial-configure-solarwinds-service-desk-previously-samanage-for-automatic-user-provisioning"></a>Zelf studie: SolarWinds Service Desk (voorheen SAManage) configureren voor automatische gebruikers inrichting

In deze zelf studie worden de stappen beschreven die u moet uitvoeren in zowel SolarWinds Service Desk (voorheen SAManage) als Azure Active Directory (Azure AD) voor het configureren van automatische gebruikers inrichting. Wanneer de [service](https://www.samanage.com/pricing/) is geconfigureerd, worden gebruikers en groepen door Azure AD automatisch ingericht en ongedaan gemaakt met behulp van de Azure AD-inrichtings service. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="migrate-to-the-new-solarwinds-service-desk-application"></a>Migreren naar de nieuwe SolarWinds Service Desk-toepassing

Als u een bestaande integratie met SolarWinds Service Desk hebt, raadpleegt u de volgende sectie over aanstaande wijzigingen. Als u SolarWinds Service Desk voor de eerste keer instelt, kunt u deze sectie overs Laan en de **ondersteunde mogelijkheden**verplaatsen.

#### <a name="whats-changing"></a>Wat wordt er gewijzigd?

* Wijzigingen aan de kant van Azure AD: de autorisatie methode voor het inrichten van gebruikers in Samange heeft historische **basis verificatie**. U ziet binnenkort dat de autorisatie methode is gewijzigd in een **geheim bewaard-token**.


#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>Wat moet ik doen om mijn bestaande aangepaste integratie naar de nieuwe toepassing te migreren?

Als u een bestaande SolarWinds Service Desk-integratie met geldige beheerders referenties hebt, hoeft u **geen actie te ondernemen**. Klanten worden automatisch naar de nieuwe toepassing gemigreerd. Dit proces wordt volledig achter de schermen uitgevoerd. Als de bestaande referenties verlopen of als u de toegang tot de toepassing opnieuw moet verlenen, moet u een geheim token met een lange levens duur genereren. Als u een nieuw token wilt genereren, raadpleegt u stap 2 van dit artikel.


#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Hoe kan ik zien of mijn toepassing is gemigreerd? 

Wanneer uw toepassing wordt gemigreerd, worden de velden **beheerders naam** en **beheerders wachtwoord** vervangen door een veld met een enkel **geheim-token** in de sectie **beheerders referenties** .

## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden

> [!div class="checklist"]
> * Gebruikers maken in SolarWinds Service Desk
> * Gebruikers in SolarWinds Service Desk verwijderen wanneer ze geen toegang meer nodig hebben
> * Gebruikers kenmerken gesynchroniseerd laten tussen Azure AD en SolarWinds Service Desk
> * Inrichtings groepen en groepslid maatschappen in SolarWinds Service Desk
> * [Eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/saas-apps/samanage-tutorial) bij de Service Desk SolarWinds (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-Tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Een gebruikers account in azure AD met [toestemming](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) voor het configureren van inrichting (bijvoorbeeld toepassings beheerder, Cloud toepassings beheerder, eigenaar van de toepassing of globale beheerder). 
* Een [SolarWinds-Service Desk-Tenant](https://www.samanage.com/pricing/) met het Professional-pakket.
* Een gebruikers account in de SolarWinds-Service Desk met beheerders machtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen
1. Lees [hoe de inrichtingsservice werkt](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bepaal wie u wilt opnemen in het [bereik voor inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en SolarWinds Service Desk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-solarwinds-service-desk-to-support-provisioning-with-azure-ad"></a>Stap 2. SolarWinds Service Desk configureren ter ondersteuning van het inrichten met Azure AD

Zie [zelf studie tokens verificatie voor API-integratie](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657)voor het genereren van een geheim token voor authenticatie.

## <a name="step-3-add-solarwinds-service-desk-from-the-azure-ad-application-gallery"></a>Stap 3. SolarWinds Service Desk toevoegen vanuit de Azure AD-toepassings galerie

Voeg SolarWinds Service Desk toe vanuit de Azure AD-toepassings galerie om de inrichting van de SolarWinds-Service Desk te starten. Als u eerder SolarWinds Service Desk voor SSO hebt ingesteld, kunt u dezelfde toepassing gebruiken. U wordt echter aangeraden een afzonderlijke app te maken wanneer u de integratie voor het eerst test. Klik [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) voor meer informatie over het toevoegen van een toepassing uit de galerie. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting 

Met de Azure AD-inrichtingsservice kunt u bepalen wie worden ingericht op basis van toewijzing aan de toepassing en/of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen aan de toepassing toe te wijzen. Als u ervoor kiest om uitsluitend te bepalen wie wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereikfilter gebruiken zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan SolarWinds Service Desk, moet u een andere rol dan **standaard toegang**selecteren. Gebruikers met de rol Standaardtoegang worden uitgesloten van inrichting en worden gemarkeerd als niet-effectief gerechtigd in de inrichtingslogboeken. Als Standaardtoegang de enige beschikbare rol voor de toepassing is, kunt u [het manifest van de toepassing bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Begin klein. Test de toepassing met een kleine set gebruikers en groepen voordat u de toepassing naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer het bereik is ingesteld op alle gebruikers en groepen, kunt u een [bereikfilter op basis van kenmerken](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-solarwinds-service-desk"></a>Stap 5. Automatische gebruikers inrichting configureren voor SolarWinds Service Desk 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in TestApp te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

### <a name="to-configure-automatic-user-provisioning-for-solarwinds-service-desk-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor SolarWinds Service Desk in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** en vervolgens **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **SolarWinds Service Desk**.

3. Selecteer het tabblad **Inrichten**.

    ![Scherm afbeelding waarop het tabblad inrichten wordt weer gegeven.](common/provisioning.png)

4. Stel de **Inrichtingsmodus** in op **Automatisch**.

    ![Scherm opname van de inrichtings modus ingesteld op automatisch.](common/provisioning-automatic.png)

5. Selecteer in de sectie **beheerders referenties** de invoer `https://api.samanage.com` in de Tenant- **URL**.  Voer de geheime token waarde in die eerder is opgehaald in het **geheime token**. Selecteer **verbinding testen** om te zorgen dat Azure AD verbinding kan maken met SolarWinds Service Desk. Als de verbinding mislukt, controleert u of het account van de SolarWinds-Service Desk beheerders machtigingen heeft en probeer het opnieuw.

    ![Scherm afbeelding met de geselecteerde knop voor testen van de verbinding.](./media/samanage-provisioning-tutorial/provisioning.png)

6. Voer in het veld **E-mailadres voor meldingen** het e-mailadres in van een persoon of groep die de inrichtingsfoutmeldingen zou moeten ontvangen en schakel het selectievakje **Een e-mailmelding verzenden als een fout optreedt** in.

    ![E-mailadres voor meldingen](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met SolarWinds Service Desk**.

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar SolarWinds Service Desk in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in SolarWinds Service Desk voor bijwerk bewerkingen. Als u ervoor kiest om het [overeenkomende doel kenmerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)te wijzigen, moet u ervoor zorgen dat de SolarWinds Service Desk-API het filteren van gebruikers op basis van dat kenmerk kan ondersteunen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

      ![Samange-gebruikers toewijzingen](./media/samanage-provisioning-tutorial/user-attributes.png)

10. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren naar SolarWinds Service Desk**.

11. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar SolarWinds Service Desk in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om te voldoen aan de groepen in SolarWinds Service Desk voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

      ![Samange-groeps toewijzingen](./media/samanage-provisioning-tutorial/group-attributes.png)

12. Als u bereikfilters wilt configureren, raadpleegt u de volgende instructies in de [zelfstudie Bereikfilter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Als u de service desk van de Azure AD-inrichtings service voor SolarWinds wilt inschakelen, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtingsstatus ingeschakeld](common/provisioning-toggle-on.png)

14. Definieer de gebruikers en/of groepen die u wilt inrichten voor SolarWinds Service Desk door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtingsbereik](common/provisioning-scope.png)

15. Wanneer u klaar bent om in te richten, selecteert u **Opslaan**.

    ![Inrichtingsconfiguratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële cyclus duurt langer dan volgende cycli, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende resources om uw implementatie te bewaken:

1. Gebruik de [inrichtingslogboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangsbalk](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtingscyclus weer te geven en te zien of deze al bijna is voltooid
3. Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. [Klik hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) voor meer informatie over quarantainestatussen.

## <a name="connector-limitations"></a>Connectorbeperkingen

Als u de optie **alle gebruikers en groepen synchroniseren** selecteert en een waarde configureert voor het kenmerk SolarWinds Service Desk **roles** , moet de waarde onder de **standaard waarde** worden weer gegeven in de volgende notatie:

- {"displayName": "Role"}, waarbij Role de gewenste standaard waarde is.

## <a name="change-log"></a>Wijzigingenlogboek

* 09/14/2020-de bedrijfs naam in twee SaaS-zelf studies gewijzigd van SAManage naar SolarWinds Service Desk (voorheen SAManage) per https://github.com/ravitmorales .
* 04/22/2020-de autorisatie methode wordt bijgewerkt van de basis verificatie naar een lang bewaard geheim token.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../manage-apps/check-status-user-account-provisioning.md)
