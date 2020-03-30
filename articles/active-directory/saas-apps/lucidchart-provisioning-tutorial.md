---
title: 'Zelfstudie: Gebruikersinrichting voor LucidChart - Azure AD'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen in LucidChart.
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
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5d946c6e257c7676178f9bc3c234f66ba6fe622
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057325"
---
# <a name="tutorial-configure-lucidchart-for-automatic-user-provisioning"></a>Zelfstudie: LucidChart configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om u de stappen te laten zien die u moet uitvoeren in LucidChart en Azure AD om gebruikersaccounts automatisch in te richten en te de-provisionen van Azure AD naar LucidChart. 

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende items hebt:

* Een Azure Active directory tenant
* Een LucidChart-tenant met het [Enterprise-plan](https://www.lucidchart.com/user/117598685#/subscriptionLevel) of beter ingeschakeld
* Een gebruikersaccount in LucidChart met beheerdersmachtigingen

## <a name="assigning-users-to-lucidchart"></a>Gebruikers toewijzen aan LucidChart

Azure Active Directory gebruikt een concept genaamd 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van automatische gebruikersaccountinrichting worden alleen de gebruikers en groepen die zijn 'toegewezen' aan een toepassing in Azure AD gesynchroniseerd.

Voordat u de inrichtingsservice configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw LucidChart-app. Eenmaal besloten, u deze gebruikers toewijzen aan uw LucidChart app door het volgen van de instructies hier:

[Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-lucidchart"></a>Belangrijke tips voor het toewijzen van gebruikers aan LucidChart

* Het wordt aanbevolen dat één Azure AD-gebruiker aan LucidChart wordt toegewezen om de inrichtingsconfiguratie te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan LucidChart toewijst, moet u de rol **Gebruiker** of een andere geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. De functie **Standaardtoegang** werkt niet voor inrichten en deze gebruikers worden overgeslagen.

## <a name="configuring-user-provisioning-to-lucidchart"></a>Gebruikersvoorziening configureren voor LucidChart

In deze sectie u uw Azure AD verbinden met de API voor het inrichten van het gebruikersaccount van LucidChart en de inrichtingsservice configureren om toegewezen gebruikersaccounts in LucidChart te maken, bij te werken en uit te schakelen op basis van gebruikers- en groepstoewijzing in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-On voor LucidChart in te schakelen, volgens de instructies in [Azure portal.](https://portal.azure.com) Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="configure-automatic-user-account-provisioning-to-lucidchart-in-azure-ad"></a>Automatische gebruikersaccountvoorziening configureren voor LucidChart in Azure AD

1. Blader in de [Azure-portal](https://portal.azure.com)naar de sectie **Azure Active Directory > Enterprise Apps > Alle toepassingen.**

2. Als u LucidChart al hebt geconfigureerd voor eenmalige aanmelding, zoekt u naar uw instantie van LucidChart met behulp van het zoekveld. Selecteer anders **Toevoegen** en zoeken naar **LucidChart** in de toepassingsgalerie. Selecteer LucidChart in de zoekresultaten en voeg deze toe aan uw lijst met toepassingen.

3. Selecteer uw instantie van LucidChart en selecteer vervolgens het tabblad **Inrichten.**

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![LucidChart Provisioning](./media/lucidchart-provisioning-tutorial/LucidChart1.png)

5. Voer onder de sectie **Beheerdersreferenties** het **geheime token** in dat wordt gegenereerd door het account van uw LucidChart (u het token onder uw account vinden: **Team** > **App Integration** > **SCIM**).

    ![LucidChart Provisioning](./media/lucidchart-provisioning-tutorial/LucidChart2.png)

6. Klik in de Azure-portal op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met uw LucidChart-app. Als de verbinding mislukt, moet u ervoor zorgen dat uw LucidChart-account beheerdersmachtigingen heeft en stap 5 opnieuw proberen.

7. Voer het e-mailadres in van een persoon of groep die meldingen van provisioning-fout moet ontvangen in het veld **Meldingse e-mail** en schakel het selectievakje 'Een e-mailmelding verzenden wanneer er een fout optreedt' in.

8. Klik op **Opslaan**.

9. Selecteer Azure **Active Directory-gebruikers synchroniseren met LucidChart**onder de sectie Toewijzingen .

10. Controleer in de sectie **Kenmerktoewijzingen** de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar LucidChart. De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in LucidChart te matchen voor updatebewerkingen. Selecteer de knop Opslaan om wijzigingen door te voeren.

11. Als u de Azure AD-inrichtingsservice voor LucidChart wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen**

12. Klik op **Opslaan**.

Met deze bewerking wordt de eerste synchronisatie gestart van gebruikers en/of groepen die zijn toegewezen aan LucidChart in de sectie Gebruikers en groepen. De eerste synchronisatie duurt langer om uit te voeren dan de daaropvolgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de service wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het inrichten van activiteitslogboeken te volgen, waarin alle acties van de inrichtingsservice worden beschreven.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)
