---
title: Fout Geen abonnementen gevonden - aanmelden bij Azure-portal | Microsoft Docs
description: Biedt de oplossing voor een probleem waarbij de fout Geen abonnementen gevonden optreedt tijdens het aanmelden bij de Azure-portal of het Azure-accountcentrum.
services: ''
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: cost-management-billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 1573a5d5d9b537b208b2f6d6aea29b9738ddad3e
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75988108"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal-or-azure-account-center"></a>Aanmeldingsfout Geen abonnementen gevonden voor Azure-portal of het Azure-accountcentrum

Mogelijk wordt het foutbericht 'Geen abonnementen gevonden' weergegeven wanneer u zich probeert aan te melden bij de [Azure-portal](https://portal.azure.com/) of het [Azure-accountcentrum](https://account.windowsazure.com/Subscriptions). Dit artikel biedt een oplossing voor dit probleem.

## <a name="symptom"></a>Symptoom

Als u probeert te melden bij de [Azure-portal](https://portal.azure.com/) of de [Azure account center](https://account.windowsazure.com/Subscriptions), ontvangt u de volgende strekking weergegeven: 'Geen abonnementen gevonden'.

## <a name="cause"></a>Oorzaak

Dit probleem treedt op als u de verkeerde map hebt geselecteerd of als uw account niet over voldoende machtigingen beschikt. 

## <a name="solution"></a>Oplossing

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>Scenario 1: Foutbericht wordt ontvangen in de [Azure-portal](https://portal.azure.com)

Dit probleem oplossen:

* Zorg ervoor dat de juiste Azure-map is geselecteerd door in de rechterbovenhoek op uw account te klikken.

  ![Selecteer de map in de rechterbovenhoek van de Azure-portal](./media/no-subscriptions-found/directory-switch.png)
* Als de juiste Azure-map is geselecteerd maar u nog steeds het foutbericht ontvangt, [wijst u de rol van Eigenaar toe aan uw account](../../role-based-access-control/role-assignments-portal.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>Scenario 2: Foutbericht wordt ontvangen in de [Accountcentrum van Azure](https://account.windowsazure.com/Subscriptions)

Controleer of het account dat u gebruikt de accountbeheerder is. Voer de volgende stappen uit om te controleren wie de accountbeheerder is:

1. Meld u aan bij de [Abonnementenweergave in de Azure-portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecteer het abonnement dat u wilt controleren en kijk vervolgens bij **Instellingen**.
1. Selecteer **Eigenschappen**. De accountbeheerder van het abonnement wordt weergegeven in het vak **Accountbeheerder**.  

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u een vraag wilt stellen of hulp nodig hebt, maakt u een [ondersteuningsaanvraag](https://go.microsoft.com/fwlink/?linkid=2083458).
