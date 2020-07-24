---
title: 'Zelf studie: sneeuw configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en het ongedaan maken van de inrichting van gebruikers accounts op sneeuw.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f9ce85f4-0992-4bc6-8276-4b2efbce8dcb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 46ebb122b0165d469b1c40871d5939e50a8595c9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87016290"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Zelf studie: sneeuw configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in sneeuw en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen op [sneeuw](https://www.Snowflake.com/pricing/). Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen. 


> [!NOTE]
> Deze connector bevindt zich momenteel in de open bare preview. Zie [aanvullende gebruiksrecht overeenkomst voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over de algemene Microsoft Azure gebruiksrecht overeenkomst voor preview-functies.

## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken met sneeuw vlokken
> * Gebruikers in sneeuw verwijderen wanneer ze niet meer toegang nodig hebben
> * Gebruikers kenmerken gesynchroniseerd laten tussen Azure AD en sneeuw vlokken
> * Inrichtings groepen en groepslid maatschappen in sneeuw
> * [Eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial) bij sneeuw (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-Tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Een gebruikers account in azure AD met [toestemming](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) voor het configureren van inrichting (bijvoorbeeld toepassings beheerder, Cloud toepassings beheerder, eigenaar van de toepassing of globale beheerder).
* [Een sneeuw-Tenant](https://www.Snowflake.com/pricing/).
* Een gebruikers account in sneeuw met beheerders machtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Uw inrichtings implementatie plannen
1. Meer informatie over [de werking van de inrichtings service](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bepaal wie binnen het [bereik van de inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)valt.
3. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en sneeuw](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-snowflake-to-support-provisioning-with-azure-ad"></a>Stap 2. Sneeuw configureren ter ondersteuning van inrichting met Azure AD

Voordat u een sneeuw gaat configureren voor automatische gebruikers inrichting met Azure AD, moet u SCIM inrichten inschakelen op sneeuw vlokken.

1. Meld u aan bij uw sneeuw-beheer console. Voer de query in die hieronder wordt weer gegeven in het werk blad en klik op **uitvoeren**.

    ![Sneeuw-beheer console](media/Snowflake-provisioning-tutorial/image00.png)

2.  Er wordt een SCIM-toegangs token gegenereerd voor uw sneeuw-Tenant. Als u deze wilt ophalen, klikt u op de gemarkeerde koppeling hieronder.

    ![Sneeuw SCIM toevoegen](media/Snowflake-provisioning-tutorial/image01.png)

3. Kopieer de gegenereerde token waarde en klik op **gereed**. Deze waarde wordt ingevoerd in het veld **geheime token** op het tabblad inrichten van uw sneeuw-toepassing in de Azure Portal.

    ![Sneeuw SCIM toevoegen](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="step-3-add-snowflake-from-the-azure-ad-application-gallery"></a>Stap 3. Sneeuw toevoegen vanuit de Azure AD-toepassings galerie

Voeg sneeuw toe vanuit de Azure AD-toepassings galerie om het beheer van de inrichting op sneeuw te starten. Als u nog een keer hebt ingesteld dat sneeuw voor SSO is geïnstalleerd, kunt u dezelfde toepassing gebruiken. Het is echter raadzaam dat u een afzonderlijke app maakt wanneer u de integratie in eerste instantie test. Meer informatie over het toevoegen van een toepassing uit [de galerie.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie binnen het bereik van de inrichting valt 

Met de Azure AD-inrichtings service kunt u bereiken die worden ingericht op basis van de toewijzing aan de toepassing en of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van de toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen toe te wijzen aan de toepassing. Als u kiest voor het bereik dat alleen wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereik filter gebruiken zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan sneeuw, moet u een andere rol dan **standaard toegang**selecteren. Gebruikers met de rol standaard toegang worden uitgesloten van inrichting en worden gemarkeerd als niet effectief in de inrichtings Logboeken. Als de enige rol die beschikbaar is op de toepassing de standaard rol Access is, kunt u [het toepassings manifest bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Begin klein. Test met een klein aantal gebruikers en groepen voordat u naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen toe te wijzen aan de app. Wanneer bereik is ingesteld op alle gebruikers en groepen, kunt u een [kenmerk op basis van bereik filteren](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-snowflake"></a>Stap 5. Automatische gebruikers inrichting configureren voor sneeuw 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in sneeuw te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>Automatische gebruikers inrichting voor sneeuw configureren in azure AD:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **sneeuw vlokken**.

    ![De koppeling naar Snowflake in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. In het gedeelte beheerders referenties voert u de **SCIM 2,0-basis-URL en verificatie token** waarden in die respectievelijk eerder zijn opgehaald in de **Tenant-URL** en de velden met **geheime tokens** . Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met sneeuw vlokken. Als de verbinding mislukt, zorg er dan voor dat uw sneeuw-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)

7. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in om **een e-mail bericht te verzenden wanneer er een fout optreedt**.

    ![E-mail melding](common/provisioning-notification-email.png)

8. Klik op **Opslaan**.

9. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren naar sneeuw vlokken**.

10. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar sneeuw vlokken in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in sneeuw voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |actief|Boolean|
   |displayName|Tekenreeks|
   |e-mail berichten [type EQ "werk]. waarde|Tekenreeks|
   |userName|Tekenreeks|
   |name. naam|Tekenreeks|
   |naam. familielid|Tekenreeks|
   |urn: IETF: params: scim: schemas: extensie: Enter prise: 2.0: gebruiker: defaultRole|Tekenreeks|
   |urn: IETF: params: scim: schemas: extensie: Enter prise: 2.0: gebruiker: defaultWarehouse|Tekenreeks|

11. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren met sneeuw vlokken**.

12. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD op sneeuw vlokken in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de groepen in sneeuw te vergelijken voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

      |Kenmerk|Type|
      |---|---|
      |displayName|Tekenreeks|
      |leden|Verwijzing|

13. Raadpleeg de volgende instructies in de [zelf studie](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

14. Als u de Azure AD-inrichtings service voor sneeuw wilt inschakelen, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

15. Definieer de gebruikers en/of groepen die u wilt inrichten voor sneeuw door de gewenste waarden in het **bereik** in het gedeelte **instellingen** te kiezen. Als deze optie niet beschikbaar is, configureert u de vereiste velden onder beheerders referenties, klikt u op **Opslaan** en de pagina vernieuwen. 

    ![Inrichtings bereik](common/provisioning-scope.png)

16. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

    Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd.

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende bronnen om uw implementatie te bewaken:

1. De [inrichtings logboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) gebruiken om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangs balk](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtings cyclus te bekijken en te bepalen hoe dicht deze is voltooid
3. Als de inrichtings configuratie een slechte status heeft, gaat de toepassing in quarantaine. Meer informatie over de quarantaine statussen [vindt u hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="connector-limitations"></a>Connector beperkingen

* Gegenereerde SCIM-tokens verlopen in 6 maanden. Houd er rekening mee dat deze moeten worden vernieuwd voordat deze verloopt om de inrichtings synchronisaties te kunnen blijven gebruiken. 

## <a name="change-log"></a>Wijzigingen logboek

* 07/21/2020-zacht verwijderen ingeschakeld voor alle gebruikers (via het actieve kenmerk).

## <a name="additional-resources"></a>Aanvullende resources

* Het [inrichten van een gebruikers account voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over inrichtings activiteiten](../app-provisioning/check-status-user-account-provisioning.md).
