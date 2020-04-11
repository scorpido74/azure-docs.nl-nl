---
title: Regels voor dynamisch groepslidmaatschap valideren (voorbeeld) - Azure AD | Microsoft Documenten
description: Leden testen op basis van een lidmaatschapsregel voor een dynamische groep in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/10/2020
ms.author: curtand
ms.reviewer: yukarppa
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e081f62949f4cf3f7ae375b2c60ee5b032d974
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115519"
---
# <a name="validate-a-dynamic-group-membership-rule-preview-in-azure-active-directory"></a>Een dynamische groepslidmaatschapsregel (voorbeeld) valideren in Azure Active Directory

Azure Active Directory (Azure AD) biedt nu de middelen om dynamische groepsregels te valideren (in openbare preview). Op het tabblad **Regels valideren** u uw dynamische regel valideren tegen leden van de voorbeeldgroep om te bevestigen dat de regel werkt zoals verwacht. Bij het maken of bijwerken van dynamische groepsregels willen beheerders weten of een gebruiker of een apparaat lid is van de groep. Dit helpt bij het beoordelen of de gebruiker of het apparaat voldoet aan de regelcriteria en helpt bij het oplossen van problemen wanneer lidmaatschap niet wordt verwacht.

## <a name="step-by-step-walk-through"></a>Stap-voor-stap walk-through

Ga naar Azure **Active** > **Directory-groepen**om aan de slag te gaan. Selecteer een bestaande dynamische groep of maak een nieuwe dynamische groep en klik op Dynamische lidmaatschapsregels. U vervolgens het tabblad **Regels valideren** bekijken.

![Het tabblad Regels valideren zoeken en beginnen met een bestaande regel](./media/groups-dynamic-rule-validation/validate-tab.png)

Op het tabblad **Regels valideren** u gebruikers selecteren om hun lidmaatschap te valideren. 20 gebruikers of apparaten kunnen tegelijk worden geselecteerd.

![Gebruikers toevoegen om de bestaande regel te valideren tegen](./media/groups-dynamic-rule-validation/validate-tab-add-users.png)

Na het kiezen van de gebruikers of apparaten uit de kiezer en **Selecteren,** wordt de validatie automatisch gestart en worden validatieresultaten weergegeven.

![De resultaten van de regelvalidatie weergeven](./media/groups-dynamic-rule-validation/validate-tab-results.png)

De resultaten vertellen of een gebruiker lid is van de groep of niet. Als de regel niet geldig is of als er een netwerkprobleem is, wordt het resultaat weergegeven als **Onbekend**. In het geval van **Onbekend,** zal de gedetailleerde foutmelding het probleem en de benodigde acties beschrijven.

![De details van de resultaten van de regelvalidatie weergeven](./media/groups-dynamic-rule-validation/validate-tab-view-details.png)

U de regel wijzigen en de validatie van lidmaatschappen wordt geactiveerd. Als u wilt zien waarom de gebruiker geen lid is van de groep, klikt u op 'Details weergeven' en worden verificatiedetails het resultaat weergegeven van elke expressie die de regel samenstelt. Klik op **OK** om af te sluiten.

## <a name="next-steps"></a>Volgende stappen

- [Dynamische lidmaatschapsregels voor groepen](groups-dynamic-membership.md)
