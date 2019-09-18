---
title: Fout Geen abonnementen gevonden - aanmelden bij Azure-portal | Microsoft Docs
description: Biedt de oplossing voor een probleem waarbij de fout Geen abonnementen gevonden optreedt tijdens het aanmelden bij de Azure-portal of het Azure-accountcentrum.
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 094d79026a55e651a1f67a5047dff20c769c359a
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "60371225"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal-or-azure-account-center"></a>Aanmeldingsfout Geen abonnementen gevonden voor Azure-portal of het Azure-accountcentrum

Mogelijk wordt het foutbericht 'Geen abonnementen gevonden' weergegeven wanneer u zich probeert aan te melden bij de [Azure-portal](https://portal.azure.com/) of het [Azure-accountcentrum](https://account.windowsazure.com/Subscriptions). Dit artikel biedt een oplossing voor dit probleem.

## <a name="symptom"></a>Symptoom

Wanneer u zich probeert aan te melden bij de [Azure-portal](https://portal.azure.com/) of het [Azure-accountcentrum](https://account.windowsazure.com/Subscriptions), wordt het volgende foutbericht weergegeven: 'Geen abonnementen gevonden'

## <a name="cause"></a>Oorzaak

Dit probleem treedt op als u de verkeerde map hebt geselecteerd of als uw account niet over voldoende machtigingen beschikt. 

## <a name="solution"></a>Oplossing

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>Scenario 1: Het foutbericht wordt ontvangen in de [Azure-portal](https://portal.azure.com)

Dit probleem oplossen:

* Zorg ervoor dat de juiste Azure-map is geselecteerd door in de rechterbovenhoek op uw account te klikken.

  ![Selecteer de map in de rechterbovenhoek van de Azure-portal](./media/billing-no-subscriptions-found/directory-switch.png)
* Als de juiste Azure-map is geselecteerd maar u nog steeds het foutbericht ontvangt, [wijst u de rol van Eigenaar toe aan uw account](../role-based-access-control/role-assignments-portal.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>Scenario 2: Het foutbericht wordt ontvangen in het [Azure-accountcentrum](https://account.windowsazure.com/Subscriptions)

Controleer of het account dat u gebruikt de accountbeheerder is. Voer de volgende stappen uit om te controleren wie de accountbeheerder is:

1. Meld u aan bij de [Abonnementenweergave in de Azure-portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecteer het abonnement dat u wilt controleren en kijk vervolgens onder **Instellingen**.
1. Selecteer **Eigenschappen**. De accountbeheerder van het abonnement wordt weergegeven in het vak **Accountbeheerder**.  

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Voor vragen of hulp [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).
