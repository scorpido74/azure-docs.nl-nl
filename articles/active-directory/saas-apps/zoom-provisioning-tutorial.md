---
title: 'Zelf studie: zoomen configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van gebruikers accounts vanuit Azure AD om in te zoomen.
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
ms.date: 06/3/2019
ms.author: Zhchia
ms.openlocfilehash: 94c261da0c935cb7a41dde768069099b4e5ed251
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80384072"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Zelf studie: zoomen configureren voor automatische gebruikers inrichting

In deze zelf studie worden de stappen beschreven die u moet uitvoeren in zowel in-/uitzoomen als Azure Active Directory (Azure AD) om het automatisch inrichten van gebruikers te configureren. Wanneer u deze configureert, worden gebruikers en groepen automatisch door Azure AD ingericht en ongedaan gemaakt om te [zoomen](https://zoom.us/pricing/) met behulp van de Azure AD-inrichtings service. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen. 


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers in-/uitzoomen maken
> * Gebruikers in zoomen verwijderen wanneer ze niet meer toegang nodig hebben
> * Gebruikers kenmerken gesynchroniseerd laten tussen Azure AD en zoomen
> * [Eenmalige aanmelding om in](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-tutorial) te zoomen (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-Tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Een gebruikers account in azure AD met [toestemming](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) voor het configureren van inrichting (bijvoorbeeld toepassings beheerder, Cloud toepassings beheerder, eigenaar van de toepassing of globale beheerder). 
* [Een zoom-Tenant](https://zoom.us/pricing).
* Een gebruikers account in-/uitzoomen met beheerders machtigingen.

## <a name="step-1-plan-your-provisioning-deployment"></a>Step 1. Uw inrichtings implementatie plannen
1. Meer informatie over [de werking van de inrichtings service](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bepaal wie binnen het [bereik van de inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)valt.
3. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en zoom](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-zoom-to-support-provisioning-with-azure-ad"></a>Stap 2. Inzoomen configureren voor ondersteuning van inrichting met Azure AD

1. Meld u aan bij de [Zoom-beheer console](https://zoom.us/signin). Navigeer naar **geavanceerd > zoomen voor ontwikkel aars** in het navigatie deel venster links.

    ![Zoom integraties](media/zoom-provisioning-tutorial/zoom01.png)

2. Navigeer naar **beheren** in de rechter bovenhoek van de pagina. 

    ![Zoom installatie](media/zoom-provisioning-tutorial/zoom02.png)

3. Navigeer naar uw Azure AD-app die u hebt gemaakt. 
    
    ![Zoom-app](media/zoom-provisioning-tutorial/zoom03.png)

4. Selecteer **app-referenties** in het navigatie deel venster links.

    ![Zoom-app](media/zoom-provisioning-tutorial/zoom04.png)

5. Kopieer het JWT- **token**en sla het op. Deze waarde wordt ingevoerd in het veld **geheime token** op het tabblad inrichten van de zoom toepassing in de Azure Portal. Als u een nieuw niet-verlopend token nodig hebt, moet u de verval tijd opnieuw configureren waarmee automatisch een nieuw token wordt gegenereerd. 

    ![Zoom installatie](media/zoom-provisioning-tutorial/zoom05.png)

## <a name="step-3-add-zoom-from-the-azure-ad-application-gallery"></a>Stap 3. Inzoomen toevoegen vanuit de Azure AD-toepassings galerie

U kunt inzoomen toevoegen vanuit de Azure AD-toepassings galerie om het beheer van de inrichting te starten om in te zoomen. Als u eerder inzoomen hebt ingesteld voor SSO, kunt u dezelfde toepassing gebruiken. Het is echter raadzaam dat u een afzonderlijke app maakt wanneer u de integratie in eerste instantie test. Meer informatie over het toevoegen van een toepassing uit [de galerie.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie binnen het bereik van de inrichting valt 

Met de Azure AD-inrichtings service kunt u bereiken die worden ingericht op basis van de toewijzing aan de toepassing en of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van de toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen toe te wijzen aan de toepassing. Als u kiest voor het bereik dat alleen wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereik filter gebruiken zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst om in te zoomen, moet u een andere rol dan de **standaard toegang**selecteren. Gebruikers met de rol standaard toegang worden uitgesloten van inrichting en worden gemarkeerd als niet effectief in de inrichtings Logboeken. Als de enige rol die beschikbaar is op de toepassing de standaard rol Access is, kunt u [het toepassings manifest bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Begin klein. Test met een klein aantal gebruikers en groepen voordat u naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen toe te wijzen aan de app. Wanneer bereik is ingesteld op alle gebruikers en groepen, kunt u een [kenmerk op basis van bereik filteren](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)opgeven. 


## <a name="step-5-configure-automatic-user-provisioning-to-zoom"></a>Stap 5. Automatische gebruikers inrichting configureren om in te zoomen 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in TestApp te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

### <a name="to-configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor inzoomen in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Inzoomen**.

    ![De koppeling Zoom in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** .

    ![Tabblad inrichten](common/provisioning.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. Voer `https://api.zoom.us/scim` in het gedeelte **beheerders referenties** de URL van de **Tenant**in. Voer de **JWT-token** waarde in die eerder is opgehaald in het **geheime token**. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met Inzoomen. Als de verbinding mislukt, zorg er dan voor dat uw zoom account beheerders machtigingen heeft en probeer het opnieuw.

    ![Inzoom inrichting](./media/zoom-provisioning-tutorial/provisioning.png)

6. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje **e-mail melding verzenden wanneer een fout optreedt** in.

    ![E-mail melding](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **synchroniseren Azure Active Directory gebruikers om in te zoomen**.

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD om in te zoomen in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in zoomen voor bijwerk bewerkingen. Als u ervoor kiest om het [overeenkomende doel kenmerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)te wijzigen, moet u ervoor zorgen dat de zoom-API het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |userName|Tekenreeks|
   |actief|Booleaans|
   |name. naam|Tekenreeks|
   |naam. familielid|Tekenreeks|
   |e-mail berichten [type EQ "werk]|Tekenreeks|
   |urn: IETF: params: scim: schemas: extension: Enter prise: 2.0: gebruiker: Department|Tekenreeks|

10. Raadpleeg de volgende instructies in de [zelf studie](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

11. Als u de Azure AD-inrichtings service voor inzoomen wilt inschakelen, **wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten om in te zoomen door de gewenste waarden in het **bereik** in het gedeelte **instellingen** te kiezen.

    ![Inrichtings bereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de initiële synchronisatie cyclus gestart van alle gebruikers en groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . De eerste cyclus duurt langer dan volgende cycli, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende bronnen om uw implementatie te bewaken:

1. De [inrichtings logboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) gebruiken om te bepalen welke gebruikers al dan niet met succes zijn ingericht
2. Controleer de [voortgangs balk](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtings cyclus te bekijken en te bepalen hoe dicht deze is voltooid
3. Als de inrichtings configuratie een slechte status heeft, gaat de toepassing in quarantaine. Meer informatie over de quarantaine statussen [vindt u hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="connector-limitations"></a>Connector beperkingen
* Als u inzoomt, worden er momenteel Maxi maal 9.999 basis gebruikers toegestaan.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../manage-apps/check-status-user-account-provisioning.md)
