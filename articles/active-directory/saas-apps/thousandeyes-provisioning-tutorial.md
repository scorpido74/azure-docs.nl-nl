---
title: 'Zelfstudie: Gebruikersinrichting voor ThousandEyes - Azure AD'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor ThousandEyes.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: d87cffce636146eac3e557670ffc4fb2fc34ae38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062877"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>Zelfstudie: ThousandEyes configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om u de stappen te laten zien die u moet uitvoeren in ThousandEyes en Azure AD om gebruikersaccounts automatisch in te richten en te de-provisionen van Azure AD naar ThousandEyes. 

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende items hebt:

* Een Azure Active directory tenant
* Een ThousandEyes-tenant met het [standaardplan](https://www.thousandeyes.com/pricing) of beter ingeschakeld 
* Een gebruikersaccount in ThousandEyes met beheerdersmachtigingen 

> [!NOTE]
> De Azure AD-integratie is gebaseerd op de [ThousandEyes SCIM-API](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK), die beschikbaar is voor ThousandEyes-teams op het standaardplan of beter.

## <a name="assigning-users-to-thousandeyes"></a>Gebruikers toewijzen aan ThousandEyes

Azure Active Directory gebruikt een concept genaamd 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van automatische gebruikersaccountinrichting worden alleen de gebruikers en groepen die zijn 'toegewezen' aan een toepassing in Azure AD gesynchroniseerd. 

Voordat u de inrichtingsservice configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw ThousandEyes-app. Eenmaal besloten, u deze gebruikers toewijzen aan uw ThousandEyes app door het volgen van de instructies hier:

[Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>Belangrijke tips voor het toewijzen van gebruikers aan ThousandEyes

* Het wordt aanbevolen dat één Azure AD-gebruiker aan ThousandEyes wordt toegewezen om de inrichtingsconfiguratie te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan ThousandEyes toewijst, moet u de rol **Gebruiker** of een andere geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. De functie **Standaardtoegang** werkt niet voor inrichten en deze gebruikers worden overgeslagen.

## <a name="configuring-user-provisioning-to-thousandeyes"></a>Gebruikersvoorziening configureren voor ThousandEyes 

Met deze sectie u uw Azure AD verbinden met de API voor het inrichten van het gebruikersaccount van ThousandEyes en de inrichtingsservice configureren om toegewezen gebruikersaccounts in ThousandEyes te maken, bij te werken en uit te schakelen op basis van gebruikers- en groepstoewijzing in Azure AD .

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-On voor ThousandEyes in te schakelen, volgens de instructies in [Azure portal.](https://portal.azure.com) Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>Automatische gebruikersaccountvoorziening configureren voor ThousandEyes in Azure AD

1. Blader in de [Azure-portal](https://portal.azure.com)naar de sectie **Azure Active Directory > Enterprise Apps > Alle toepassingen.**

2. Als u ThousandEyes al hebt geconfigureerd voor eenmalige aanmelding, zoekt u naar uw voorbeeld van ThousandEyes met behulp van het zoekveld. Selecteer anders **ThousandEyes** **toevoegen** en zoeken in de toepassingsgalerie. Selecteer ThousandEyes in de zoekresultaten en voeg deze toe aan uw lijst met toepassingen.

3. Selecteer uw exemplaar van ThousandEyes en selecteer vervolgens het tabblad **Inrichten.**

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![ThousandEyes Provisioning](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)

5. Voer onder de sectie **Beheerdersreferenties** het **OAuth-dragertoken** in dat wordt gegenereerd door het account van je ThousandEyes (je een token vinden en of genereren onder de sectie ThousandEyes-accountprofiel). **Profile**

    ![ThousandEyes Provisioning](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. Klik in de Azure-portal op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met uw ThousandEyes-app. Als de verbinding mislukt, moet u ervoor zorgen dat uw ThousandEyes-account beheerdersmachtigingen heeft en stap 5 opnieuw proberen.

7. Voer het e-mailadres in van een persoon of groep die meldingen van provisioning-fout moet ontvangen in het veld **Meldingse e-mail** en schakel het selectievakje 'Een e-mailmelding verzenden wanneer er een fout optreedt' in.

8. Klik op **Opslaan**.

9. Selecteer Azure **Active Directory-gebruikers synchroniseren met ThousandEyes**onder de sectie Toewijzingen .

10. Controleer in de sectie **Toewijzingen van kenmerken** de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar ThousandEyes. De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in ThousandEyes te matchen voor updatebewerkingen. Selecteer de knop Opslaan om wijzigingen door te voeren.

11. Als u de Azure AD-inrichtingsservice voor ThousandEyes wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen**

12. Klik op **Opslaan**.

Met deze bewerking wordt de eerste synchronisatie gestart van gebruikers en/of groepen die zijn toegewezen aan ThousandEyes in de sectie Gebruikers en groepen. De eerste synchronisatie duurt langer om uit te voeren dan de daaropvolgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de service wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het inrichten van activiteitslogboeken te volgen, waarin alle acties van de inrichtingsservice worden beschreven.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)
