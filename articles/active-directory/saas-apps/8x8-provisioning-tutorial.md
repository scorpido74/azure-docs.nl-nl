---
title: 'Zelf studie: 8X8 configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts van Azure AD naar 8X8.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 81b4cde7-4938-4a1a-8495-003c06239b27
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2020
ms.author: Zhchia
ms.openlocfilehash: d1359ffb28cb98c0e46a6b7eff32c6faa7d00ba4
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2020
ms.locfileid: "87924765"
---
# <a name="tutorial-configure-8x8-for-automatic-user-provisioning"></a>Zelf studie: 8X8 configureren voor automatische gebruikers inrichting

In deze zelf studie worden de stappen beschreven die u moet uitvoeren in zowel 8X8 Configuration Manager als Azure Active Directory (Azure AD) om het automatisch inrichten van gebruikers te configureren. Wanneer de configuratie is geconfigureerd, worden gebruikers en groepen door Azure AD automatisch ingericht en [ge8x8d](https://www.8x8.com) met behulp van de Azure AD-inrichtings service. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen. 

## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden
> [!div class="checklist"]
> * Gebruikers maken in 8X8
> * Gebruikers in 8X8 verwijderen wanneer ze niet meer toegang nodig hebben
> * Gebruikers kenmerken gesynchroniseerd laten tussen Azure AD en 8X8
> * [Eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/saas-apps/8x8virtualoffice-tutorial) bij 8X8 (aanbevolen)

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-Tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Een gebruikers account in azure AD met [toestemming](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) voor het configureren van inrichting (bijvoorbeeld toepassings beheerder, Cloud toepassings beheerder, eigenaar van de toepassing of globale beheerder).
* Een 8X8 X-serie abonnement van elk niveau.
* Een 8X8-gebruikers account met Administrator-machtiging in [Configuration Manager](https://vo-cm.8x8.com).
* [Eenmalige aanmelding met Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/8x8virtualoffice-tutorial) is al geconfigureerd.

## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Uw inrichtings implementatie plannen
1. Meer informatie over [de werking van de inrichtings service](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Bepaal wie binnen het [bereik van de inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)valt.
3. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en 8X8](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-8x8-to-support-provisioning-with-azure-ad"></a>Stap 2. 8X8 configureren voor ondersteuning bij het inrichten met Azure AD

In deze sectie wordt u begeleid bij de stappen voor het configureren van 8X8 ter ondersteuning van het inrichten met Azure AD.

### <a name="to-configure-a-user-provisioning-access-token-in-8x8-configuration-manager"></a>Een toegangs token voor het inrichten van gebruikers configureren in 8X8 Configuration Manager:

1. Meld u aan bij [Configuration Manager](https://vo-cm.8x8.com). Selecteer **identiteits beheer**.

   ![Identiteits beheer](./media/8x8-provisioning-tutorial/8x8-identity-management.png)

2. Klik op de koppeling **gebruikers inrichtings informatie weer geven** om een token te genereren.

   ![Gebruikers inrichten weer geven](./media/8x8-provisioning-tutorial/8x8-show-user-provisioning.png)

3. Kopieer de **8X8-URL** en de **8X8-API-token** waarden. Deze waarden worden respectievelijk in de velden **Tenant-URL** en **geheim** vermeld op het tabblad inrichten van uw 8X8-toepassing in de Azure Portal.

   ![URL en Token kopiëren](./media/8x8-provisioning-tutorial/8x8-copy-url-token.png)

## <a name="step-3-add-8x8-from-the-azure-ad-application-gallery"></a>Stap 3. 8X8 toevoegen vanuit de Azure AD-toepassings galerie

Voeg 8X8 toe vanuit de Azure AD-toepassings galerie om het beheren van de inrichting van 8X8 te starten. Als u eerder 8X8 voor SSO hebt ingesteld, kunt u dezelfde toepassing gebruiken. Het is echter raadzaam dat u een afzonderlijke app maakt wanneer u de integratie in eerste instantie test. Meer informatie over het toevoegen van een toepassing uit [de galerie.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie binnen het bereik van de inrichting valt

Met de Azure AD-inrichtings service kunt u bereiken die worden ingericht op basis van de toewijzing aan de toepassing en of op basis van kenmerken van de gebruiker/groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van de toewijzing, kunt u de volgende [stappen](../manage-apps/assign-user-or-group-access-portal.md) gebruiken om gebruikers en groepen toe te wijzen aan de toepassing. Dit is de eenvoudigste optie en wordt gebruikt door de meeste mensen.

Als u kiest voor het bereik dat alleen wordt ingericht op basis van kenmerken van de gebruiker of groep, kunt u een bereik filter gebruiken zoals [hier](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)wordt beschreven. 

* Wanneer u gebruikers en groepen toewijst aan 8X8, moet u een andere rol dan **standaard toegang**selecteren. Gebruikers met de rol standaard toegang worden uitgesloten van inrichting en worden gemarkeerd als niet effectief in de inrichtings Logboeken. Als de enige rol die beschikbaar is op de toepassing de standaard rol Access is, kunt u [het toepassings manifest bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om extra rollen toe te voegen. 

* Begin klein. Test met een klein aantal gebruikers en groepen voordat u naar iedereen uitrolt. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u dit beheren door een of twee gebruikers of groepen toe te wijzen aan de app. Wanneer bereik is ingesteld op alle gebruikers en groepen, kunt u een [kenmerk op basis van bereik filteren](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)opgeven. 

## <a name="step-5-configure-automatic-user-provisioning-to-8x8"></a>Stap 5. Automatische gebruikers inrichting configureren voor 8X8 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in 8X8 te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

### <a name="to-configure-automatic-user-provisioning-for-8x8-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor 8X8 in azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](./media/8x8-provisioning-tutorial/enterprise-applications.png)

    ![Blade Alle toepassingen](./media/8x8-provisioning-tutorial/all-applications.png)

2. Selecteer **8x8** in de lijst met toepassingen.

    ![De koppeling 8X8 in de lijst met toepassingen](common/all-applications.png)

3. Selecteer het tabblad **inrichten** . Klik op aan de **slag**.

    ![Tabblad inrichten](common/provisioning.png)

   ![De Blade aan de slag](./media/8x8-provisioning-tutorial/get-started.png)

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Tabblad inrichten](common/provisioning-automatic.png)

5. Kopieer in het gedeelte **beheerders referenties** de **8X8-URL** van Configuration Manager naar de **Tenant-URL**. Kopieer het **8X8 API-token** van Configuration Manager naar een **geheim token**. Klik op **verbinding testen** om te controleren of Azure AD verbinding kan maken met 8X8. Als de verbinding mislukt, zorg er dan voor dat uw 8X8-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Inrichten](./media/8x8-provisioning-tutorial/provisioning.png)

6. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje **e-mail melding verzenden wanneer een fout optreedt** in.

    ![E-mail melding](common/provisioning-notification-email.png)

7. Selecteer **Opslaan**.

8. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers inrichten**.

9. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar 8X8 in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in 8X8 voor bijwerk bewerkingen. Als u ervoor kiest om het [overeenkomende doel kenmerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)te wijzigen, moet u ervoor zorgen dat de 8X8-API het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|Opmerkingen|
   |---|---|---|
   |userName|Tekenreeks|Gebruikers naam en Federatie-ID instellen|
   |externalId|Tekenreeks||
   |actief|Booleaans||
   |titel|Tekenreeks||
   |e-mail berichten [type EQ "werk]. waarde|Tekenreeks||
   |name. naam|Tekenreeks||
   |naam. familielid|Tekenreeks||
   |phoneNumbers [type EQ "Mobile"]. waarde|Tekenreeks|Persoonlijk contact nummer|
   |phoneNumbers [type EQ "werk]. waarde|Tekenreeks|Persoonlijk contact nummer|
   |urn: IETF: params: scim: schemas: extension: Enter prise: 2.0: gebruiker: Department|Tekenreeks||
   |urn: IETF: params: scim: schemas: extensie: 8X8:1,1: gebruiker: site|Tekenreeks|Kan niet worden bijgewerkt nadat de gebruiker is gemaakt|
   |landinstelling|Tekenreeks|Niet standaard toegewezen|
   |tijd zone|Tekenreeks|Niet standaard toegewezen|

10. Raadpleeg de volgende instructies in de [zelf studie](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor het configureren van bereik filters.

11. Als u de Azure AD-inrichtings service voor **8X8 wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

    ![Inrichtings status inschakelt op](common/provisioning-toggle-on.png)

12. Definieer de gebruikers en/of groepen die u wilt inrichten voor 8X8 door de gewenste waarden in het **bereik** te kiezen in de sectie **instellingen** .

    ![Inrichtings bereik](common/provisioning-scope.png)

13. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

    ![Inrichtings configuratie opslaan](common/provisioning-configuration-save.png)

Met deze bewerking wordt de initiële synchronisatie cyclus gestart van alle gebruikers en groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . De eerste cyclus duurt langer dan volgende cycli, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. 

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken
Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende bronnen om uw implementatie te bewaken:

1. Gebruik de [inrichtings logboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) om te bepalen welke gebruikers al dan niet met succes zijn ingericht.
2. Controleer de [voortgangs balk](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtings cyclus weer te geven en te zien hoe sluiten deze moet worden voltooid.
3. Als de inrichtings configuratie een slechte status heeft, gaat de toepassing in quarantaine. Meer informatie over de quarantaine statussen [vindt u hier](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../manage-apps/check-status-user-account-provisioning.md)
