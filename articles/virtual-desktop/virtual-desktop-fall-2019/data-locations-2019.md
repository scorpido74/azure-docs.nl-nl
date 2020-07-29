---
title: Gegevens locaties voor virtueel bureau blad van Windows (klassiek)-Azure
description: Een beknopt overzicht van de locaties waar Windows-gegevens en meta gegevens worden opgeslagen.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0e12e17914374a64ecd2d17a8e73c6ebf20328d9
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87270036"
---
# <a name="data-locations-for-windows-virtual-desktop-classic"></a>Gegevens locaties voor virtueel bureau blad van Windows (klassiek)

>[!IMPORTANT]
>Deze inhoud is van toepassing op het virtuele bureau blad van Windows (klassiek), dat geen ondersteuning biedt voor Azure Resource Manager virtueel-bureaublad objecten van Windows. Zie [dit artikel](../data-locations.md)als u probeert Azure Resource Manager virtueel-bureaublad objecten van Windows te beheren.

Virtueel bureau blad van Windows is momenteel beschikbaar voor alle geografische locaties. In eerste instantie kunnen meta gegevens van de service alleen worden opgeslagen in de Geografie van Verenigde Staten (Verenigde Staten). Beheerders kunnen de locatie kiezen voor het opslaan van gebruikers gegevens wanneer ze de virtuele machines van de hostgroep en gekoppelde services, zoals bestands servers, maken. Meer informatie over Azure-geografi op de [Azure Data Center-kaart](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Micro soft heeft geen invloed op de regio's waar u of uw gebruikers toegang hebben tot uw gebruiker en app-specifieke gegevens.

>[!IMPORTANT]
>In Windows Virtual Desktop worden globale meta gegevens opgeslagen, zoals Tenant namen, namen van hostnamen, namen van app-groepen en UPN-namen in een Data Center in de Verenigde Staten. De opgeslagen meta gegevens worden versleuteld op rest en geo-redundante mirrors worden in de Verenigde Staten bewaard. Alle klant gegevens, zoals app-instellingen en gebruikers gegevens, bevinden zich op de locatie die de klant kiest en wordt niet beheerd door de service.

Meta gegevens van de service worden gerepliceerd in de Verenigde Staten voor nood herstel doeleinden.