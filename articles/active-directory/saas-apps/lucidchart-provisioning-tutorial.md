---
title: 'Zelf studie: gebruikers inrichten voor LucidChart-Azure AD'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op LucidChart.
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
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057325"
---
# <a name="tutorial-configure-lucidchart-for-automatic-user-provisioning"></a>Zelf studie: LucidChart configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is om u te laten zien welke stappen u moet uitvoeren in LucidChart en Azure AD om gebruikers accounts van Azure AD automatisch in te richten en te deactiveren naar LucidChart. 

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u de volgende items al hebt:

* Een Azure Active Directory-Tenant
* Een LucidChart-Tenant met het [ondernemings plan](https://www.lucidchart.com/user/117598685#/subscriptionLevel) of beter ingeschakeld
* Een gebruikers account in LucidChart met beheerders machtigingen

## <a name="assigning-users-to-lucidchart"></a>Gebruikers toewijzen aan LucidChart

Azure Active Directory gebruikt een concept met de naam ' toewijzingen ' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers accounts worden alleen de gebruikers en groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u de inrichtings service configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw LucidChart-app. Nadat u hebt besloten, kunt u deze gebruikers toewijzen aan uw LucidChart-app door de volgende instructies te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-lucidchart"></a>Belang rijke tips voor het toewijzen van gebruikers aan LucidChart

* U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan LucidChart om de inrichtings configuratie te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan LucidChart, moet u **de gebruikersrol** of een andere geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. De **standaard-Access** -rol werkt niet voor inrichten en deze gebruikers worden overgeslagen.

## <a name="configuring-user-provisioning-to-lucidchart"></a>Gebruikers inrichten configureren voor LucidChart

In deze sectie vindt u instructies voor het verbinden van uw Azure AD-LucidChart en het configureren van de inrichtings service om toegewezen gebruikers accounts in LucidChart te maken, bij te werken en uit te scha kelen op basis van de gebruikers-en groeps toewijzing in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te scha kelen voor LucidChart, volgens de instructies in [Azure Portal](https://portal.azure.com). Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, maar deze twee functies gelden voor elkaar.

### <a name="configure-automatic-user-account-provisioning-to-lucidchart-in-azure-ad"></a>Automatische toewijzing van gebruikers accounts configureren voor LucidChart in azure AD

1. Blader in het [Azure Portal](https://portal.azure.com)naar het gedeelte **Azure Active Directory > Enter prise-apps > alle toepassingen** .

2. Als u LucidChart al hebt geconfigureerd voor eenmalige aanmelding, zoekt u naar uw instantie van LucidChart met behulp van het zoek veld. Als dat niet het geval is, selecteert u **toevoegen** en zoeken naar **LucidChart** in de toepassings galerie. Selecteer LucidChart in de zoek resultaten en voeg deze toe aan uw lijst met toepassingen.

3. Selecteer uw exemplaar van LucidChart en selecteer vervolgens het tabblad **inrichten** .

4. Stel de **inrichtings modus** in op **automatisch**.

    ![LucidChart-inrichting](./media/lucidchart-provisioning-tutorial/LucidChart1.png)

5. Voer in het gedeelte **beheerders referenties** het **geheime token** in dat is gegenereerd door het account van uw LucidChart (u kunt het token vinden onder uw account: **team** > **app Integration** > **scim**).

    ![LucidChart-inrichting](./media/lucidchart-provisioning-tutorial/LucidChart2.png)

6. Klik in het Azure Portal op **verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met uw LucidChart-app. Als de verbinding mislukt, zorgt u ervoor dat uw LucidChart-account beheerders machtigingen heeft en voert u stap 5 opnieuw uit.

7. Voer het e-mail adres in van een persoon of groep die in het veld met de **meldings-e-mail** inrichtings fout meldingen moet ontvangen en schakel het selectie vakje e-mail meldingen verzenden als er een fout optreedt.

8. Klik op **Opslaan**.

9. Selecteer in de sectie toewijzingen de optie **Azure Active Directory gebruikers synchroniseren met LucidChart**.

10. Controleer in de sectie **kenmerk toewijzingen** de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar LucidChart. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in LucidChart voor bijwerk bewerkingen. Selecteer de knop Opslaan om door te voeren van eventuele wijzigingen.

11. Als u de Azure AD-inrichtings service voor LucidChart wilt inschakelen, wijzigt u de **inrichtings status** **in in het** gedeelte **instellingen**

12. Klik op **Opslaan**.

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn toegewezen aan LucidChart in de sectie gebruikers en groepen. De eerste synchronisatie langer duren om uit te voeren dan het volgende wordt gesynchroniseerd, die ongeveer elke 40 minuten optreden als de service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen te volgen naar activiteiten logboeken voor inrichtingen, die alle acties beschrijven die door de inrichtings service worden uitgevoerd.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)
