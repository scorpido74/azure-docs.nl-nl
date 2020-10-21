---
title: 'Quickstart: een telefoonnummer aanvragen bij Azure Communication Services'
description: Meer informatie over het kopen van een telefoonnummer bij Communication Services met behulp van de Azure-portal.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 10/05/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: a9c33fad6d376c2f568f4eefbb0379486b5c40f3
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044765"
---
# <a name="quickstart-get-a-phone-number-using-the-azure-portal"></a>Quickstart: Een telefoonnummer aanvragen met behulp van de Azure-portal

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Ga aan de slag met Azure Communication Services om een telefoonnummer te kopen met behulp van de Azure-portal.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Een actieve Communication Services-resource.](../create-communication-resource.md)

## <a name="get-a-phone-number"></a>Een telefoonnummer aanvragen

Als u wilt beginnen met het inrichten van nummers, gaat u naar uw Communication Services-resource in de [Azure-portal](https://portal.azure.com).

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Schermopname van de hoofdpagina van een Communicatie Services-resource.":::

### <a name="getting-new-phone-numbers"></a>Nieuwe telefoonnummers aanvragen

Navigeer naar de blade Telefoonnummers in het resourcemenu.

:::image type="content" source="../media/manage-phone-azure-portal-phone-page.png" alt-text="Schermopname van de hoofdpagina van een Communicatie Services-resource.":::

Druk op de knop `Get` om de wizard te starten. De wizard op de blade `Phone numbers` begeleidt u door een reeks vragen die u helpt bij het kiezen van het telefoonnummer dat het beste bij uw scenario past. 

U moet eerst bij `Country/region` kiezen waar u het telefoonnummer wilt inrichten. Nadat u een land/regio hebt geselecteerd, moet u bij `use case` het telefoonabonnement selecteren dat het beste bij uw behoeften past. 

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers.png" alt-text="Schermopname van de hoofdpagina van een Communicatie Services-resource.":::

### <a name="select-a-phone-plan"></a>Een telefoonabonnement selecteren

Het proces voor het selecteren van een telefoonabonnement bestaat uit twee stappen: 

1. De selectie van het [nummertype](../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)
2. De selectie van het [abonnement](../../concepts/telephony-sms/plan-solution.md#phone-number-plans-in-azure-communication-services)

We bieden momenteel twee nummertypen: `Geographic` en `Toll-free`. Wanneer u een nummertype hebt geselecteerd, worden er verschillende abonnementen aangeboden waaruit u kunt kiezen.

In ons voorbeeld hebben we het nummertype `Toll-free` en de abonnementen `Outbound calling` en `Inbound and Outbound SMS` gekozen.

:::image type="content" source="../media/manage-phone-azure-portal-select-plans.png" alt-text="Schermopname van de hoofdpagina van een Communicatie Services-resource.":::

Klik op de knop `Next: Numbers` onder aan de pagina om de telefoonnummers die u wilt inrichten aan te passen.

### <a name="customizing-phone-numbers"></a>Telefoonnummers aanpassen

Op de pagina `Numbers` past u de telefoonnummers aan die u wilt inrichten.

:::image type="content" source="../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="Schermopname van de hoofdpagina van een Communicatie Services-resource.":::

> [!NOTE]
> In deze quickstart wordt de stroom voor het aanpassen van het nummertype `Toll-free` weergegeven. De ervaring kan enigszins verschillen als u het nummertype `Geographic` hebt gekozen, maar het eindresultaat is hetzelfde.

Kies de `Area code` in de lijst met beschikbare netnummers, voer het aantal nummers dat u wilt inrichten in, en klik vervolgens op `Search` om nummers te vinden die voldoen aan uw selectiecriteria. De telefoonnummers die aan uw criteria voldoen, worden samen met de kosten per maand weergegeven.

:::image type="content" source="../media/manage-phone-azure-portal-found-numbers.png" alt-text="Schermopname van de hoofdpagina van een Communicatie Services-resource.":::

> [!NOTE]
> De beschikbaarheid is afhankelijk van het nummertype, de locatie en het abonnement dat u hebt geselecteerd.
> Nummers worden korte tijd gereserveerd voordat de transactie verloopt. Als de transactie verloopt, moet u de nummers opnieuw selecteren.

Als u het aankoopoverzicht wilt bekijken en uw bestelling wilt plaatsen, klikt u op de knop `Next: Summary` onder aan de pagina.

### <a name="place-order"></a>Bestelling plaatsen

Op de overzichtspagina worden het nummertype, de kenmerken, de telefoonnummers en de totale maandelijkse kosten voor het inrichten van de telefoonnummers weergegeven.

> [!NOTE]
> De weergegeven prijzen zijn de **maandelijks terugkerende kosten** die aan u worden berekend voor het leasen van het geselecteerde telefoonnummer. Niet inbegrepen in deze weergave zijn de **kosten voor betalen per gebruik**, die u maakt voor binnenkomende of uitgaande oproepen. De prijslijsten zijn [hier beschikbaar](../../concepts/pricing.md). Deze kosten zijn afhankelijk van het nummertype en van welke nummers u belt. Zo kan de prijs per minuut voor een gesprek van een regionaal nummer in Seattle naar een regionaal nummer in New York erg verschillen van de gesprekskosten voor een gesprek van datzelfde nummer in Seattle naar een mobiel nummer in het Verenigd Koninkrijk.

Klik ten slotte op `Place order` onder aan de pagina ter bevestiging.

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="Schermopname van de hoofdpagina van een Communicatie Services-resource.":::

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Uw telefoonnummers zoeken in de Azure-portal

Ga naar de Azure Communication Services-resource in de [Azure-portal](https://portal.azure.com):

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Schermopname van de hoofdpagina van een Communicatie Services-resource.":::

Selecteer de blade Telefoonnummers in het menu om uw telefoonnummers te beheren.

:::image type="content" source="../media/manage-phone-azure-portal-phones.png" alt-text="Schermopname van de hoofdpagina van een Communicatie Services-resource.":::

> [!NOTE]
> Het kan enkele minuten duren voordat de ingerichte nummers op deze pagina worden weergegeven.

### <a name="customizing-phone-number-features"></a>Telefoonnummerfuncties aanpassen
Op de pagina `Numbers` kunt u het telefoonnummer selecteren door te klikken op het nummer waarvoor u het abonnement wilt aanpassen.

:::image type="content" source="../media/manage-phone-azure-portal-capability-update.png" alt-text="Schermopname van de hoofdpagina van een Communicatie Services-resource.":::

Selecteer de mogelijkheden in de lijst met beschikbare mogelijkheden voor bellen en sms en klik op `Confirm` om de selectie toe te passen.

## <a name="troubleshooting"></a>Problemen oplossen

Veelvoorkomende vragen en problemen:

- Op dit moment kunt u alleen telefoonnummers kopen in de Verenigde Staten. Dit is gebaseerd op het factuuradres van het abonnement waaraan de resource is gekoppeld. U kunt de resource op dit moment niet naar een ander abonnement verplaatsen.

- Wanneer een telefoonnummer wordt vrijgegeven, wordt het telefoonnummer pas vrijgegeven en kan het pas worden teruggekocht aan het einde van de factureringsperiode.

- Wanneer een Communication Services-resource wordt verwijderd, worden de telefoonnummers die aan die resource zijn gekoppeld, automatisch op hetzelfde moment vrijgegeven.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u de volgende zaken geleerd:

> [!div class="checklist"]
> * Een telefoonnummer kopen
> * Uw telefoonnummer beheren
> * Een telefoonnummer vrijgeven

> [!div class="nextstepaction"]
> [Een sms verzenden](../telephony-sms/send.md)
> [Aan de slag met gesprekken](../voice-video-calling/getting-started-with-calling.md)
