---
title: Fout Geen abonnementen gevonden - aanmelden bij de Azure-portal
description: Biedt de oplossing voor een probleem waarbij de fout Geen abonnementen gevonden optreedt tijdens het aanmelden bij de Azure-portal of het Azure-accountcentrum.
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 08/20/2020
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 70f479148cb2f5f3f2b13c431e0c4b80b1fb9543
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684827"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal-or-azure-account-center"></a>Aanmeldingsfout Geen abonnementen gevonden voor Azure-portal of het Azure-accountcentrum

Mogelijk wordt het foutbericht 'Geen abonnementen gevonden' weergegeven wanneer u zich probeert aan te melden bij de [Azure-portal](https://portal.azure.com/) of het [Azure-accountcentrum](https://account.windowsazure.com/Subscriptions). Dit artikel biedt een oplossing voor dit probleem.

## <a name="symptom"></a>Symptoom

Wanneer u zich probeert aan te melden bij de [Azure-portal](https://portal.azure.com/) of het [Azure-accountcentrum](https://account.windowsazure.com/Subscriptions), wordt het volgende foutbericht weergegeven: 'Geen abonnementen gevonden'

## <a name="cause"></a>Oorzaak

Dit probleem treedt op als u de verkeerde map hebt geselecteerd of als uw account niet over voldoende machtigingen beschikt.

## <a name="solution"></a>Oplossing

### <a name="scenario-1-error-message-is-received-in-the-azure-portal"></a>Scenario 1: Het foutbericht wordt ontvangen in de [Azure-portal](https://portal.azure.com)

Dit probleem oplossen:

* Zorg ervoor dat de juiste Azure-map is geselecteerd door in de rechterbovenhoek op uw account te klikken.

  ![Selecteer de map in de rechterbovenhoek van de Azure-portal](./media/no-subscriptions-found/directory-switch.png)
* Als de juiste Azure-map is geselecteerd maar u nog steeds het foutbericht ontvangt, [wijst u de rol van Eigenaar toe aan uw account](../../role-based-access-control/role-assignments-portal.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-center"></a>Scenario 2: Het foutbericht wordt ontvangen in het [Azure-accountcentrum](https://account.windowsazure.com/Subscriptions)

Controleer of het account dat u gebruikt de accountbeheerder is. Voer de volgende stappen uit om te controleren wie de accountbeheerder is:

1. Meld u aan bij de [Abonnementenweergave in de Azure-portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecteer het abonnement dat u wilt controleren en kijk vervolgens bij **Instellingen**.
1. Selecteer **Eigenschappen**. De accountbeheerder van het abonnement wordt weergegeven in het vak **Accountbeheerder**.  

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).
