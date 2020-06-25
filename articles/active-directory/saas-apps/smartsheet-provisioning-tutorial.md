---
title: 'Zelf studie: Smart Sheet configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op Smart Sheet.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: 699eb37176d6737744fb0ba01f9f3f4a2d4e55b1
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85318744"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Zelf studie: Smart Sheet configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in Smart Sheet en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen in [Smart Sheet](https://www.smartsheet.com/pricing). Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen. 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in Smart Sheet
> * Gebruikers in Smart Sheet verwijderen wanneer ze niet meer toegang nodig hebben
> * Gebruikers kenmerken gesynchroniseerd laten tussen Azure AD en Smart Sheet
> * Eenmalige aanmelding bij Smart Sheet (aanbevolen)

> [!NOTE]
> Deze connector bevindt zich momenteel in de open bare preview. Zie [aanvullende gebruiksrecht overeenkomst voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de algemene Microsoft Azure gebruiksrecht overeenkomst voor preview-functies.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-Tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Een gebruikers account in azure AD met [toestemming](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) voor het configureren van inrichting (bijvoorbeeld toepassings beheerder, Cloud toepassings beheerder, eigenaar van de toepassing of globale beheerder).
* [Een Smart Sheet-Tenant](https://www.smartsheet.com/pricing).
* Een gebruikers account op een Smart Sheet Enter prise-of ENTER prise Premier-abonnement met systeembeheerders machtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Uw inrichtings implementatie plannen
1. Meer informatie over [de werking van de inrichtings service](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bepaal wie binnen het [bereik van de inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)valt.
3. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en Smart Sheet](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-smartsheet-to-support-provisioning-with-azure-ad"></a>Stap 2. Smart Sheet configureren voor ondersteuning bij het inrichten met Azure AD

Voordat u Smart Sheet configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u SCIM inrichten inschakelen op Smart Sheet.

1. Meld u aan als **sysadmin** in de **[Smart Sheet-Portal](https://app.smartsheet.com/b/home)** en navigeer naar **account beheerder**.

    ![Smart Sheet-account beheerder](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Ga naar **beveiligings controles > gebruikers automatisch inrichten > bewerken**.

    ![Smart Sheet-beveiligings controles](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Voeg en valideer de e-mail domeinen voor de gebruikers die u van plan bent vanuit Azure AD in te richten op Smart Sheet. Kies **niet ingeschakeld** om ervoor te zorgen dat alle inrichtings acties alleen afkomstig zijn van Azure AD en om er ook voor te zorgen dat uw Smart Sheet-gebruikers lijst wordt gesynchroniseerd met Azure AD-toewijzingen.

    ![Smart Sheet gebruikers inrichten](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Nadat de validatie is voltooid, moet u het domein activeren. 

    ![Domein Smart Sheet activeren](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Genereer het **geheime token** dat vereist is voor het configureren van automatische gebruikers inrichting met Azure AD door te navigeren naar **apps en integraties**.

    ![Smart Sheet-installatie](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Kies **API-toegang**. Klik op **nieuw toegangs token genereren**.

    ![Smart Sheet-installatie](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Definieer de naam van het API-toegangs token. Klik op **OK**.

    ![Smart Sheet-installatie](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Kopieer het API-toegangs token en sla het op omdat dit de enige keer is dat u deze kunt bekijken. Dit is vereist in het veld **geheime token** in azure AD.

    ![Smart Sheet-token](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="step-3-add-smartsheet-from-the-azure-ad-application-gallery"></a>Stap 3. Smart Sheet toevoegen vanuit de Azure AD-toepassings galerie

Voeg Smart Sheet toe vanuit de Azure AD-toepassings galerie om het beheren van de inrichting van Smart Sheet te starten. Als u eerder Smart Sheet voor SSO hebt ingesteld, kunt u dezelfde toepassing gebruiken. Het is echter raadzaam dat u een afzonderlijke app maakt wanneer u de integratie in eerste instantie test. Meer informatie over het toevoegen van een toepassing uit [de galerie.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie binnen het bereik van de inrichting valt 

Met de Azure AD-inrichtings service kunt u bereiken die worden ingericht op basis van de toewijzing aan de toepassing en of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van de toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen toe te wijzen aan de toepassing. Als u kiest voor het bereik dat alleen wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereik filter gebruiken zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan Smart Sheet, moet u een andere rol dan **standaard toegang**selecteren. Gebruikers met de rol standaard toegang worden uitgesloten van inrichting en worden gemarkeerd als niet effectief in de inrichtings Logboeken. Als de enige rol die beschikbaar is op de toepassing de standaard rol Access is, kunt u [het toepassings manifest bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Om ervoor te zorgen dat pariteit in gebruikersrol toewijzingen tussen Smart Sheet en Azure AD wordt gebruikt, is het raadzaam om dezelfde roltoewijzingen te gebruiken die zijn ingevuld in de volledige Smart Sheet-gebruikers lijst. Als u deze gebruikers lijst van Smart sheet wilt ophalen, gaat u naar **account beheerder > gebruikers beheer > meer acties > Download gebruikers lijst (CSV)**.

* Voor toegang tot bepaalde functies in de app vereist Smart Sheet dat een gebruiker meerdere rollen heeft. Ga voor meer informatie over gebruikers typen en machtigingen in Smart Sheet naar [gebruikers typen en machtigingen](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Als aan een gebruiker meerdere rollen zijn toegewezen in Smart Sheet, **moet** u ervoor zorgen dat deze roltoewijzingen worden gerepliceerd in azure AD om een scenario te vermijden waarin gebruikers de toegang tot Smart Sheet-objecten permanent kunnen verliezen. Elke unieke rol in Smart Sheet **moet** worden toegewezen aan een andere groep in azure AD. De gebruiker **moet** vervolgens worden toegevoegd aan elk van de groepen die overeenkomen met de gewenste rollen. 

* Begin klein. Test met een klein aantal gebruikers en groepen voordat u naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen toe te wijzen aan de app. Wanneer bereik is ingesteld op alle gebruikers en groepen, kunt u een [kenmerk op basis van bereik filteren](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)opgeven. 

## <a name="step-5-configure-automatic-user-provisioning-to-smartsheet"></a>Stap 5. Automatische gebruikers inrichting configureren voor Smart Sheet 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in Smart Sheet te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor Smart sheet in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Smart Sheet**.

    ![De koppeling Smart sheet in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. In het gedeelte **beheerders referenties** voert u de **scim 2,0-basis-URL en toegangs token** waarden in die eerder zijn opgehaald uit Smart sheet in de **Tenant-URL** en het **geheime token** . Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met Smart Sheet. Als de verbinding mislukt, zorg er dan voor dat uw Smart Sheet-account SysAdmin-machtigingen heeft en probeer het opnieuw.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in om **een e-mail bericht te verzenden wanneer er een fout optreedt**.

    ![E-mail melding](common/provisioning-notification-email.png)

7. Klik op **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Smart Sheet**.

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Smart sheet in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Smart Sheet voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |actief|Booleaans|
   |titel|Tekenreeks|
   |userName|Tekenreeks|
   |name. naam|Tekenreeks|
   |naam. familielid|Tekenreeks|
   |phoneNumbers [type EQ "werk]. waarde|Tekenreeks|
   |phoneNumbers [type EQ "Mobile"]. waarde|Tekenreeks|
   |phoneNumbers [type EQ "fax"]. waarde|Tekenreeks|
   |externalId|Tekenreeks|
   |rollen [Primary EQ "True"]. display|Tekenreeks|
   |rollen [Primary EQ "True"]. type|Tekenreeks|
   |rollen [Primary EQ "True"]. Value|Tekenreeks|
   |rolls|Tekenreeks|
   urn: IETF: params: scim: schemas: extension: Enter prise: 2.0: gebruiker: Department|Tekenreeks|
   |urn: IETF: params: scim: schemas: extensie: Enter prise: 2.0: gebruiker: deling|Tekenreeks|
   |urn: IETF: params: scim: schemas: extensie: Enter prise: 2.0: gebruiker: costCenter|Tekenreeks|
   |urn: IETF: params: scim: schemas: extensie: Enter prise: 2.0: gebruiker: Manager|Tekenreeks|


10. Raadpleeg de volgende instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

11. Als u de Azure AD-inrichtings service voor **Smart sheet wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten voor Smart Sheet door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtings bereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende bronnen om uw implementatie te bewaken:

1. De [inrichtings logboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) gebruiken om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangs balk](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtings cyclus te bekijken en te bepalen hoe dicht deze is voltooid
3. Als de inrichtings configuratie een slechte status heeft, gaat de toepassing in quarantaine. Meer informatie over de quarantaine statussen [vindt u hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="connector-limitations"></a>Connector beperkingen

* Smart Sheet biedt geen ondersteuning voor soft-verwijderingen. Wanneer het **actieve** kenmerk van een gebruiker is ingesteld op ONWAAR, wordt de gebruiker in Smart Sheet permanent verwijderd.

## <a name="change-log"></a>Wijzigingenlogboek

* 06/16/2020-ondersteuning toegevoegd voor bedrijfsextensie kenmerken ' Cost Center ', ' divisie ', ' Manager ' en ' afdeling ' voor gebruikers.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)
