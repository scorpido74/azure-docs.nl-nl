---
title: 'Zelf studie: Tic-Tac Mobile configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts van Azure AD op Tic-Tac Mobile.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d0f24e81-fecf-4e71-bd8a-ab911366fdf5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: Zhchia
ms.openlocfilehash: 99fe2f8356b53b5d8e459dbf62534ddb4f0019b2
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357263"
---
# <a name="tutorial-configure-tic-tac-mobile-for-automatic-user-provisioning"></a>Zelf studie: Tic-Tac Mobile configureren voor automatische gebruikers inrichting

In deze zelf studie worden de stappen beschreven die u moet uitvoeren in zowel Tic-Tac Mobile als Azure Active Directory (Azure AD) voor het configureren van automatische gebruikers inrichting. Wanneer deze is geconfigureerd, worden gebruikers en groepen door Azure AD automatisch ingericht en ongedaan gemaakt op [boter-TAC-mobiel](https://www.tictacmobile.com/) met behulp van de Azure AD-inrichtings service. Zie voor meer informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen gebruikers automatisch inrichten en ongedaan maken van de [inrichting van SaaS-toepassingen (Software as a Service) met Azure AD](../manage-apps/user-provisioning.md).


## <a name="capabilities-supported"></a>Ondersteunde mogelijkheden

> [!div class="checklist"]
> * Maak gebruikers in Tic-Tac mobiel.
> * Gebruikers in Tic-Tac Mobile verwijderen wanneer ze niet meer toegang nodig hebben.
> * Behoud gebruikers kenmerken gesynchroniseerd tussen Azure AD en Tic-Tac Mobile.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelfstudie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* [Een Azure AD-tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Een gebruikers account in azure AD met [toestemming](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) voor het configureren van de inrichting. Voor beelden zijn toepassings beheerder, Cloud toepassings beheerder, eigenaar van de toepassing of globale beheerder.
* Een [boter-TAC Mobile-](https://www.tictacmobile.com/) account met de rol Super beheerder.


## <a name="step-1-plan-your-provisioning-deployment"></a>Stap 1. Implementatie van de inrichting plannen

1. Lees [hoe de inrichtingsservice werkt](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
1. Bepaal wie u wilt opnemen in het [bereik voor inrichting](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
1. Bepaal welke gegevens moeten worden [toegewezen tussen Azure AD en Tic-Tac Mobile](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).

## <a name="step-2-configure-tic-tac-mobile-to-support-provisioning-with-azure-ad"></a>Stap 2. Tic-Tac Mobile configureren voor de ondersteuning van inrichting met Azure AD

Neem contact support@tictacmobile.com op om de **TENANT-URL** en het **geheime token** op te halen. U moet een super beheerdersrol hebben in Tic-Tac Mobile om een token te ontvangen. Het token wordt ingevoerd in het vak **geheim** op het tabblad **inrichten** van uw Tic-Tac mobiele toepassing in de Azure Portal.

## <a name="step-3-add-tic-tac-mobile-from-the-azure-ad-application-gallery"></a>Stap 3. Tic-Tac Mobile toevoegen vanuit de Azure AD-toepassings galerie

Voeg Tic-Tac Mobile toe vanuit de Azure AD-toepassings galerie om het beheer van de inrichting te starten op Tic-Tac mobiel. Als u Tic-Tac Mobile eerder hebt ingesteld voor eenmalige aanmelding, kunt u dezelfde toepassing gebruiken. Wanneer u de integratie in eerste instantie wilt testen, maakt u een afzonderlijke app. Zie voor meer informatie over het toevoegen van een toepassing uit de galerie op [kenmerken gebaseerde toepassing inrichten met bereik filters](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Stap 4. Definiëren wie u wilt opnemen in het bereik voor inrichting

Met de Azure AD-inrichtings service kunt u bereiken die worden ingericht op basis van de toewijzing aan de toepassing of op basis van de kenmerken van de gebruiker of groep. Als u ervoor kiest om te bepalen wie wordt ingericht voor uw app op basis van toewijzing, volgt u de stappen in [gebruikers toewijzing beheren voor een app in azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) om gebruikers en groepen toe te wijzen aan de toepassing. Als u ervoor kiest om te bepalen wie alleen wordt ingericht op basis van kenmerken van de gebruiker of groep, gebruikt u een bereik filter zoals beschreven in [toepassings inrichting op basis van kenmerken met bereik filters](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

* Wanneer u gebruikers en groepen toewijst aan Tic-Tac Mobile, moet u een andere rol dan de **standaard toegang** selecteren. Gebruikers met de rol standaard toegang worden uitgesloten van inrichting en worden gemarkeerd als niet effectief in de inrichtings Logboeken. Als de enige rol die beschikbaar is op de toepassing de standaard rol Access is, kunt u [het toepassings manifest bijwerken](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) om meer rollen toe te voegen.
* Begin klein. Test met een klein aantal gebruikers en groepen voordat u naar iedereen implementeert. Wanneer het bereik voor inrichting is ingesteld op toegewezen gebruikers en groepen, kunt u het besturings element behouden door een of twee gebruikers of groepen aan de app toe te wijzen. Wanneer bereik is ingesteld op alle gebruikers en groepen, kunt u een [op kenmerken gebaseerd bereik filter](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)opgeven.

## <a name="step-5-configure-automatic-user-provisioning-to-tic-tac-mobile"></a>Stap 5. Automatische gebruikers inrichting configureren voor Tic-Tac Mobile

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers of groepen in TestApp te maken, bij te werken en uit te scha kelen op basis van gebruikers-of groeps toewijzingen in azure AD.

### <a name="configure-automatic-user-provisioning-for-tic-tac-mobile-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor Tic-Tac Mobile in azure AD

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **Bedrijfstoepassingen** > **Alle toepassingen**.

    ![Scherm opname van het deel venster bedrijfs toepassingen.](common/enterprise-applications.png)

1. Selecteer in de lijst toepassingen de optie **boter-TAC-mobiel**.

    ![Scherm opname van de Tic-Tac mobiele koppeling in de lijst met toepassingen.](common/all-applications.png)

1. Selecteer het tabblad **Inrichten**.

    ![Scherm opname van het tabblad inrichten.](common/provisioning.png)

1. Stel de **inrichtings modus** in op **automatisch**.

    ![Scherm afbeelding met de automatische optie voor het inrichten van het tabblad.](common/provisioning-automatic.png)

1. Geef in de sectie **beheerders referenties** de Tic-Tac mobiele **Tenant-URL** en het **geheime token** op. Selecteer **verbinding testen** om te zorgen dat Azure AD verbinding kan maken met Tic-Tac mobiel. Als de verbinding mislukt, controleert u of uw Tic-Tac Mobile-account beheerders machtigingen heeft en probeer het opnieuw.

    ![Scherm opname van het vak geheime token.](common/provisioning-testconnection-tenanturltoken.png)

1. Voer in het vak **e-mail bericht** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen. Schakel het selectie vakje **e-mail melding verzenden wanneer een fout optreedt** in.

    ![Scherm opname van het vak e-mail melding.](common/provisioning-notification-email.png)

1. Selecteer **Opslaan**.

1. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met Tic-Tac mobiel**.

1. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar Tic-Tac Mobile in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in Tic-Tac Mobile voor update bewerkingen. Als u het [overeenkomende doel kenmerk](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)wijzigt, moet u ervoor zorgen dat de Tic-Tac Mobile API het filteren van gebruikers op basis van dat kenmerk ondersteunt. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

   |Kenmerk|Type|
   |---|---|
   |userName|Tekenreeks|
   |name.givenName|Tekenreeks|
   |name.familyName|Tekenreeks|
   |externalId|Tekenreeks|
   |title|Tekenreeks|
   |e-mail berichten [type EQ "werk]. waarde|Tekenreeks|
   |preferredLanguage|Tekenreeks|
   |externalId|Tekenreeks|
   |userType|Tekenreeks|
   |landinstelling|Tekenreeks|
   |timezone|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|Tekenreeks|
   |urn: IETF: params: scim: schemas: extensie: Enter prise: 2.0: gebruiker: costCenter|Tekenreeks|
   |urn: IETF: params: scim: schemas: extensie: Enter prise: 2.0: gebruiker: organisatie|Tekenreeks|
   |urn: IETF: params: scim: schemas: extensie: Enter prise: 2.0: gebruiker: deling|Tekenreeks|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Tekenreeks|

1. Zie de instructies in de [zelf studie](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)voor het filteren op bereik voor meer informatie over het configureren van bereik filters.

1. Als u de Azure AD-inrichtings service voor Tic-Tac Mobile wilt inschakelen, moet **u de** **inrichtings status** wijzigen in in het gedeelte **instellingen** .

    ![Scherm opname van de inrichtings status in-of uitgeschakeld.](common/provisioning-toggle-on.png)

1. Definieer de gebruikers of groepen die u wilt inrichten Tic-Tac mobiel door de gewenste waarden in het **bereik** te selecteren in het gedeelte **instellingen** .

    ![Scherm opname van het inrichtings bereik.](common/provisioning-scope.png)

1. Wanneer u klaar bent om in te richten, selecteert u **Opslaan**.

    ![Scherm opname van het opslaan van de inrichtings configuratie.](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatiecyclus gestart van alle gebruikers en groepen die zijn gedefinieerd onder **Bereik** in de sectie **Instellingen**. De initiële cyclus duurt langer dan volgende cycli, die ongeveer om de 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd.

## <a name="step-6-monitor-your-deployment"></a>Stap 6. Uw implementatie bewaken

Nadat u het inrichten hebt geconfigureerd, gebruikt u de volgende bronnen om uw implementatie te controleren.

1. Gebruik de [inrichtings logboeken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) om te bepalen welke gebruikers al dan niet met succes zijn ingericht.
1. Controleer de [voortgangs balk](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) om de status van de inrichtings cyclus weer te geven en te zien hoe sluiten deze moet worden voltooid.
1. Als het configureren van de inrichting een foutieve status lijkt te hebben, wordt de toepassing in quarantaine geplaatst. Zie [toepassings inrichting in quarantaine status](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)voor meer informatie over quarantaine Staten.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccounts inrichten voor zakelijke apps](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van logboeken en het ophalen van rapporten over de inrichtingsactiviteit](../manage-apps/check-status-user-account-provisioning.md)
