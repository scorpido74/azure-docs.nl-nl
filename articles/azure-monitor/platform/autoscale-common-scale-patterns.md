---
title: Overzicht algemene patronen voor automatische schaalaanpassing
description: Meer informatie over enkele van de algemene patronen om uw resource automatisch te schalen in Azure.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: a77cf1704c20abb77d432eab16569071208f6da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75396098"
---
# <a name="overview-of-common-autoscale-patterns"></a>Overzicht algemene patronen voor automatische schaalaanpassing
In dit artikel worden enkele van de algemene patronen beschreven om uw bron in Azure te schalen.

Azure Monitor autoscale is alleen van toepassing op [Virtual Machine Scale Sets,](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [Cloud Services](https://azure.microsoft.com/services/cloud-services/), App Service - [Web Apps](https://azure.microsoft.com/services/app-service/web/)en API [Management services](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="lets-get-started"></a>Laat aan de slag

In dit artikel wordt ervan uitgegaan dat u bekend bent met automatische schaal. U [hier aan de slag om uw resource te schalen.][1] De volgende zijn enkele van de gemeenschappelijke schaalpatronen.

## <a name="scale-based-on-cpu"></a>Schaal op basis van CPU

U beschikt over een web-app (/VMSS/cloudservicerol) en

- U wilt uitschalen /schalen op basis van CPU.
- Daarnaast wilt u ervoor zorgen dat er een minimum aantal exemplaren is.
- U wilt er ook voor zorgen dat u een maximale limiet instelt voor het aantal exemplaren waarop u schalen.

![Schaal op basis van CPU][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>Schaal anders op weekdagen vs weekends

U beschikt over een web-app (/VMSS/cloudservicerol) en

- U wilt standaard 3 exemplaren (op weekdagen)
- Je verwacht geen verkeer in het weekend en daarom wil je in het weekend naar 1 exemplaar schalen.

![Schaal anders op weekdagen vs weekends][3]

## <a name="scale-differently-during-holidays"></a>Anders schalen tijdens vakanties

U beschikt over een web-app (/VMSS/cloudservicerol) en

- U wilt standaard omhoog/omlaag schalen op basis van CPU-gebruik
- Tijdens de feestdagen (of specifieke dagen die belangrijk zijn voor uw bedrijf) wilt u echter de standaardinstellingen overschrijven en meer capaciteit tot uw beschikking hebben.

![Anders schalen op feestdagen][4]

## <a name="scale-based-on-custom-metric"></a>Schalen op basis van aangepaste statistiek

U hebt een webfront-end en een API-laag die communiceert met de backend.

- U wilt de API-laag schalen op basis van aangepaste gebeurtenissen aan de voorkant (voorbeeld: u wilt uw afhandelingsproces schalen op basis van het aantal items in het winkelwagentje)

![Schalen op basis van aangepaste statistiek][5]

<!--Reference-->
[1]: ./autoscale-get-started.md
[2]: ./media/autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/autoscale-common-scale-patterns/custom-metric-scale.png

