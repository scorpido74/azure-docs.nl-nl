---
title: Gegevens locaties voor virtueel bureau blad van Windows-Azure
description: Een kort overzicht van de locaties waar de gegevens en meta gegevens van Windows virtueel bureau blad zijn opgeslagen.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b8cbee74a8f9639fc2c3838f38dabf2a62cf339e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82614445"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Gegevens locaties voor virtueel bureau blad van Windows

>[!IMPORTANT]
>Deze inhoud is van toepassing op de update uit het najaar van 2019 die geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten. Raadpleeg [dit artikel](../data-locations.md) als u Azure Resource Manager Windows Virtual Desktop-objecten wilt beheren die zijn geïntroduceerd in de update Lente 2020.

Virtueel bureau blad van Windows is momenteel beschikbaar voor alle geografische locaties. In eerste instantie kunnen meta gegevens van de service alleen worden opgeslagen in de Geografie van Verenigde Staten (Verenigde Staten). Beheerders kunnen de locatie kiezen voor het opslaan van gebruikers gegevens wanneer ze de virtuele machines van de hostgroep en gekoppelde services, zoals bestands servers, maken. Meer informatie over Azure-geografi op de [Azure Data Center-kaart](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Micro soft heeft geen invloed op de regio's waar u of uw gebruikers toegang hebben tot uw gebruiker en app-specifieke gegevens.

>[!IMPORTANT]
>In Windows Virtual Desktop worden globale meta gegevens opgeslagen, zoals Tenant namen, namen van hostnamen, namen van app-groepen en UPN-namen in een Data Center in de Verenigde Staten. De opgeslagen meta gegevens worden versleuteld op rest en geo-redundante mirrors worden in de Verenigde Staten bewaard. Alle klant gegevens, zoals app-instellingen en gebruikers gegevens, bevinden zich op de locatie die de klant kiest en wordt niet beheerd door de service.

Meta gegevens van de service worden gerepliceerd in de Verenigde Staten voor nood herstel doeleinden.