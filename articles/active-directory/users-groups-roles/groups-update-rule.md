---
title: Een dynamische groeps regel bijwerken en beheren en problemen oplossen met lidmaatschap-Azure Active Directory | Microsoft Docs
description: Een regel voor groepslid maatschap maken in de Azure Portal, status controleren.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/30/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c387e2d78adcaebc430073a2a45818c4a0928b9f
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422366"
---
# <a name="update-a-dynamic-group-to-manage-membership-in-azure-active-directory"></a>Een dynamische groep bijwerken voor het beheren van lidmaatschappen in Azure Active Directory

In Azure Active Directory (Azure AD) kunt u regels gebruiken om het groepslid maatschap te bepalen op basis van eigenschappen van gebruikers of apparaten. In dit artikel leest u hoe u een regel instelt voor een dynamische groep in de Azure Portal.
Dynamische lidmaatschappen worden ondersteund voor beveiligings groepen of Office 365-groepen. Wanneer een regel voor groepslid maatschap wordt toegepast, worden de kenmerken van gebruikers en apparaten geëvalueerd voor overeenkomsten met de lidmaatschaps regel. Wanneer een kenmerk wordt gewijzigd voor een gebruiker of apparaat, worden alle regels voor de dynamische groep in de organisatie verwerkt voor wijzigingen in het lidmaatschap. Gebruikers en apparaten worden toegevoegd of verwijderd als ze voldoen aan de voor waarden voor een groep.

## <a name="rule-builder-in-the-azure-portal"></a>De opbouw functie voor regels in de Azure Portal

Azure AD biedt een regel bouwer om uw belang rijke regels sneller te maken en bij te werken. De opbouw functie voor regels ondersteunt de bouw Maxi maal vijf expressies. Met de opbouw functie voor regels kunt u eenvoudig een regel maken met enkele eenvoudige expressies, maar deze kan niet worden gebruikt om elke regel te reproduceren. Als de regel functie geen ondersteuning biedt voor de regel die u wilt maken, kunt u het tekstvak gebruiken.

Hier volgen enkele voor beelden van geavanceerde regels of syntaxis voor het maken van het gebruik van het tekstvak:

- Regel met meer dan vijf expressies
- De regel voor directe rapporten
- De [prioriteit van Opera tors](groups-dynamic-membership.md#operator-precedence) instellen
- [Regels met complexe expressies](groups-dynamic-membership.md#rules-with-complex-expressions); bijvoorbeeld `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> De opbouw functie voor regels kan mogelijk geen regels weer geven die zijn gemaakt in het tekstvak. Mogelijk wordt er een bericht weer gegeven wanneer de regel functie de regel niet kan weer geven. De opbouw functie voor regels wijzigt niet de ondersteunde syntaxis, validatie of verwerking van dynamische groeps regels op enigerlei wijze.

![Lidmaatschaps regel voor een dynamische groep toevoegen](./media/groups-update-rule/update-dynamic-group-rule.png)

Zie voor voor beelden van de syntaxis, ondersteunde eigenschappen, Opera tors en waarden voor een lidmaatschaps regel, [dynamische lidmaatschaps regels voor groepen in azure Active Directory](groups-dynamic-membership.md).

## <a name="to-update-a-group-membership-rule"></a>Een regel voor groepslid maatschap bijwerken

1. Meld u aan bij het [Azure AD-beheer centrum](https://aad.portal.azure.com) met een account in de rol globale beheerder, groeps beheerder, intune-beheerder of gebruikers beheerder in de Tenant.
1. Selecteer **groepen** > **alle groepen**.
1. Selecteer een groep om het profiel ervan te openen.
1. Selecteer op de profiel pagina voor de groep **dynamische lidmaatschaps regels**. De opbouw functie voor regels ondersteunt Maxi maal vijf expressies. Als u meer dan vijf expressies wilt toevoegen, moet u het tekstvak gebruiken.

   ![Lidmaatschaps regel voor een dynamische groep toevoegen](./media/groups-update-rule/update-dynamic-group-rule.png)

1. Voor een overzicht van de aangepaste extensie-eigenschappen die beschikbaar zijn voor uw lidmaatschaps regel:
   1. **Eigenschappen van aangepaste uitbrei ding ophalen** selecteren
   1. Voer de toepassings-ID in en selecteer vervolgens **Eigenschappen vernieuwen**.
1. Nadat u de regel hebt bijgewerkt, selecteert u **Opslaan**.

Als de regel die u hebt ingevoerd niet geldig is, wordt uitgelegd waarom de regel niet kan worden verwerkt, wordt weer gegeven in een Azure-melding in de portal. Lees het zorgvuldig om te begrijpen hoe u de regel kunt herstellen.

## <a name="check-processing-status-for-a-rule"></a>De verwerkings status controleren voor een regel

U kunt de verwerkings status van lidmaatschap en de datum van laatste update **voor de groep** zien.
  
  ![weer gave van de status van de dynamische groep](./media/groups-create-rule/group-status.png)

De volgende status berichten kunnen worden weer gegeven voor de **verwerkings status van lidmaatschappen** :

- **Evalueren**: de groeps wijziging is ontvangen en de updates worden geëvalueerd.
- **Verwerken**: updates worden verwerkt.
- **Update voltooid**: verwerking is voltooid en alle toepasselijke updates zijn aangebracht.
- **Verwerkings fout**: verwerking kan niet worden voltooid vanwege een fout bij het evalueren van de lidmaatschaps regel.
- **Update onderbroken**: updates van dynamische lidmaatschaps regel zijn onderbroken door de beheerder. MembershipRuleProcessingState is ingesteld op ' paused '.

De volgende status berichten kunnen worden weer gegeven voor het lidmaatschap van de **laatste bijgewerkte** status:

- **Datum en tijd**: de laatste keer dat het lidmaatschap is bijgewerkt.
- Wordt **uitgevoerd**: updates worden momenteel uitgevoerd.
- **Onbekend**: de tijd van de laatste update kan niet worden opgehaald. De groep is mogelijk nieuw.

Als er een fout optreedt tijdens het verwerken van de lidmaatschaps regel voor een specifieke groep, wordt er een waarschuwing weer gegeven boven aan de **pagina overzicht** voor de groep. Als er in behandeling zijnde dynamische lidmaatschaps updates voor alle groepen in de Tenant meer dan 24 uur kunnen worden verwerkt, wordt een waarschuwing weer gegeven aan de bovenkant van **alle groepen**.

![fout bericht waarschuwingen verwerken](./media/groups-create-rule/processing-error.png)

## <a name="next-steps"></a>Volgende stappen

Deze artikelen bevatten aanvullende informatie over het werken met dynamische groepen in azure AD.

- Zie de syntaxis van de [regel voor dynamische lidmaatschap](groups-dynamic-membership.md)voor een volledige verwijzing naar een dynamische regel structuur.
- [Een statische lidmaatschaps groep maken en leden toevoegen](../fundamentals/active-directory-groups-create-azure-portal.md).
