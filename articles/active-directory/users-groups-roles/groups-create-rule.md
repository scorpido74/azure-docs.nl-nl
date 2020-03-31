---
title: Een dynamische groep maken of bewerken en status krijgen - Azure AD | Microsoft Documenten
description: Een groepslidmaatschapsregel maken of bijwerken in de Azure-portal en de verwerkingsstatus controleren.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/07/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2ed7f27e2145f666f38eec5ddc6c985a4d32138
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266375"
---
# <a name="create-or-update-a-dynamic-group-in-azure-active-directory"></a>Een dynamische groep maken of bijwerken in Azure Active Directory

In Azure Active Directory (Azure AD) u regels gebruiken om groepslidmaatschap te bepalen op basis van gebruikers- of apparaateigenschappen. In dit artikel wordt uitgelegd hoe u een regel instelt voor een dynamische groep in de Azure-portal.
Dynamisch lidmaatschap wordt ondersteund voor beveiligingsgroepen of Office 365-groepen. Wanneer een groepslidmaatschapsregel wordt toegepast, worden gebruikers- en apparaatkenmerken geëvalueerd op overeenkomsten met de lidmaatschapsregel. Wanneer een kenmerk verandert voor een gebruiker of apparaat, worden alle dynamische groepsregels in de organisatie verwerkt voor lidmaatschapswijzigingen. Gebruikers en apparaten worden toegevoegd of verwijderd als ze voldoen aan de voorwaarden voor een groep. Beveiligingsgroepen kunnen worden gebruikt voor apparaten of gebruikers, maar Office 365-groepen kunnen alleen gebruikersgroepen zijn.

## <a name="rule-builder-in-the-azure-portal"></a>Regelbouwer in de Azure-portal

Azure AD biedt een regelbouwer om uw belangrijke regels sneller te maken en bij te werken. De regelbouwer ondersteunt de constructie tot vijf expressies. De regelbouwer maakt het gemakkelijker om een regel te vormen met een paar eenvoudige expressies, maar het kan niet worden gebruikt om elke regel te reproduceren. Als de regelbouwer de regeldie u wilt maken niet ondersteunt, u het tekstvak gebruiken.

Hier volgen enkele voorbeelden van geavanceerde regels of syntaxis waarvoor u het tekstvak samenstellen:

- Regel met meer dan vijf expressies
- De regel Direct-rapporten
- Voorrang [van de operator instellen](groups-dynamic-membership.md#operator-precedence)
- [Regels met complexe uitdrukkingen](groups-dynamic-membership.md#rules-with-complex-expressions); bijvoorbeeld`(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> De regelbouwer kan mogelijk bepaalde regels die in het tekstvak zijn gemaakt, niet weergeven. Mogelijk ziet u een bericht wanneer de regelbouwer de regel niet kan weergeven. De regelbouwer wijzigt op geen enkele manier de ondersteunde syntaxis, validatie of verwerking van dynamische groepsregels.

![Lidmaatschapsregel toevoegen voor een dynamische groep](./media/groups-create-rule/update-dynamic-group-rule.png)

Zie [Dynamische lidmaatschapsregels voor groepen in Azure Active Directory](groups-dynamic-membership.md)voor voorbeelden van syntaxis, ondersteunde eigenschappen, operators en waarden voor een lidmaatschapsregel.

## <a name="to-create-a-group-membership-rule"></a>Een groepslidmaatschapsregel maken

1. Meld u aan bij het [Azure AD-beheercentrum](https://aad.portal.azure.com) met een account dat zich in de rol Globale beheerder, Intune-beheerder of Gebruikersbeheerder in de tenant bevindt.
1. Zoeken naar en selecteer **Groepen**.
1. Selecteer **Alle groepen**en selecteer Nieuwe **groep**.

   ![De opdracht selecteren om een nieuwe groep toe te voegen](./media/groups-create-rule/create-new-group-azure-active-directory.png)

1. Voer op de pagina **Groep** een naam en beschrijving in voor de nieuwe groep. Selecteer een **lidmaatschapstype** voor gebruikers of apparaten en selecteer **Dynamische query toevoegen**. De regelbouwer ondersteunt maximaal vijf expressies. Als u meer dan vijf expressies wilt toevoegen, moet u het tekstvak gebruiken.

   ![Lidmaatschapsregel toevoegen voor een dynamische groep](./media/groups-create-rule/add-dynamic-group-rule.png)

1. Ga als instellen voor de aangepaste extensie-eigenschappen die beschikbaar zijn voor uw lidmaatschapsquery:
   1. Aangepaste **extensie-eigenschappen opbrengen**
   1. Voer de toepassings-id in en selecteer **Vernieuwen.**
1. After creating the rule, select **Save**.
1. Selecteer **Maken** op de **pagina Nieuw om** de groep te maken.

Als de ingevoerde regel niet geldig is, wordt een uitleg gegeven waarom de regel niet kan worden verwerkt in een Azure-melding in de portal. Lees het zorgvuldig om te begrijpen hoe de regel vast te stellen.

## <a name="to-update-an-existing-rule"></a>Een bestaande regel bijwerken

1. Meld u aan bij het [Azure AD-beheercentrum](https://aad.portal.azure.com) met een account dat zich in de rol Globale beheerder, groepsbeheerder, Intune-beheerder of Gebruikersbeheerder in de tenant bevindt.
1. Selecteer **Groepen** > **Alle groepen**.
1. Selecteer een groep om het profiel te openen.
1. Selecteer **dynamische lidmaatschapsregels**op de profielpagina voor de groep . De regelbouwer ondersteunt maximaal vijf expressies. Als u meer dan vijf expressies wilt toevoegen, moet u het tekstvak gebruiken.

   ![Lidmaatschapsregel toevoegen voor een dynamische groep](./media/groups-create-rule/update-dynamic-group-rule.png)

1. Ga als instellen voor de aangepaste extensie-eigenschappen die beschikbaar zijn voor uw lidmaatschapsregel:
   1. Aangepaste **extensie-eigenschappen opbrengen**
   1. Voer de toepassings-id in en selecteer **Vernieuwen.**
1. Nadat u de regel hebt bijgewerkt, selecteert u **Opslaan**.

## <a name="turn-on-or-off-welcome-email"></a>Welkomste-mail in- of uitschakelen

Wanneer een nieuwe Office 365-groep wordt gemaakt, wordt een welkome e-mailmelding verzonden naar de gebruikers die aan de groep zijn toegevoegd. Als later, als kenmerken van een gebruiker of apparaat veranderen, worden alle dynamische groepsregels in de organisatie verwerkt voor lidmaatschapswijzigingen. Gebruikers die worden toegevoegd ontvangen dan ook de welkomstmelding. U dit gedrag uitschakelen in [Exchange PowerShell.](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps)

## <a name="check-processing-status-for-a-rule"></a>Verwerkingsstatus controleren op een regel

U de status van lidmaatschapsverwerking en de laatst bijgewerkte datum zien op de pagina **Overzicht** voor de groep.
  
  ![weergave van de status van dynamische groep](./media/groups-create-rule/group-status.png)

De volgende statusberichten kunnen worden weergegeven voor de status **lidmaatschapsverwerking:**

- **Evalueren**: De groepswijziging is ontvangen en de updates worden geëvalueerd.
- **Verwerking**: Updates worden verwerkt.
- **Update compleet**: Verwerking is voltooid en alle toepasselijke updates zijn uitgevoerd.
- **Verwerkingsfout**: Verwerking kan niet worden voltooid vanwege een fout bij het evalueren van de lidmaatschapsregel.
- **Update onderbroken**: Updates voor dynamische lidmaatschapsregels zijn onderbroken door de beheerder. LidmaatschapRuleProcessingState is ingesteld op "Onderbroken".

De volgende statusberichten kunnen worden weergegeven voor de laatst bijgewerkte status **van het lidmaatschap:**

- &lt;**Datum en tijd**&gt;: De laatste keer dat het lidmaatschap is bijgewerkt.
- **In uitvoering**: Er worden momenteel updates uitgevoerd.
- **Onbekend**: de laatste updatetijd kan niet worden opgehaald. De groep kan nieuw zijn.

Als er een fout optreedt tijdens het verwerken van de lidmaatschapsregel voor een specifieke groep, wordt boven aan de **pagina Overzicht** voor de groep een waarschuwing weergegeven. Als er meer dan 24 uur lang geen dynamische lidmaatschapsupdates in behandeling zijn voor alle groepen binnen de tenant, wordt boven aan **Alle groepen**een waarschuwing weergegeven.

![foutberichtwaarschuwingen verwerken](./media/groups-create-rule/processing-error.png)

Deze artikelen bevatten aanvullende informatie over groepen in Azure Active Directory.

- [Bestaande groepen weergeven](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Een nieuwe groep maken en leden toevoegen](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Instellingen van een groep beheren](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Lidmaatschappen van een groep beheren](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Dynamische regels voor gebruikers in een groep beheren](groups-dynamic-membership.md)
