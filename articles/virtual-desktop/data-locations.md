---
title: Gegevens locaties voor virtueel bureau blad van Windows-Azure
description: Een kort overzicht van de locaties waar de gegevens en meta gegevens van Windows virtueel bureau blad zijn opgeslagen.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b1a061b2dfcdd212178a3f534a1a951697940685
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87292611"
---
# <a name="data-and-metadata-locations-for-windows-virtual-desktop"></a>Locaties van gegevens en meta gegevens voor Windows virtueel bureau blad

>[!IMPORTANT]
>Deze inhoud is van toepassing op virtueel bureau blad van Windows met Azure Resource Manager virtuele bureau blad-objecten van Windows. Zie [dit artikel](./virtual-desktop-fall-2019/data-locations-2019.md)als u Windows virtueel bureau blad (klassiek) gebruikt zonder Azure Resource Manager objecten.

Virtueel bureau blad van Windows is momenteel beschikbaar voor alle geografische locaties. Beheerders kunnen de locatie kiezen voor het opslaan van gebruikers gegevens wanneer ze de virtuele machines van de hostgroep en gekoppelde services, zoals bestands servers, maken. Meer informatie over Azure-geografi op de [Azure Data Center-kaart](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Micro soft heeft geen invloed op de regio's waar u of uw gebruikers toegang hebben tot uw gebruiker en app-specifieke gegevens.

>[!IMPORTANT]
>In Windows Virtual Desktop worden globale meta gegevens opgeslagen, zoals Tenant namen, namen van hostnamen, namen van app-groepen en principal-namen van gebruikers in een Data Center. Wanneer een klant een service object maakt, moet deze een locatie voor het Service object invoeren. De locatie die ze invoeren, bepaalt waar de meta gegevens voor het object worden opgeslagen. De klant kiest een Azure-regio en de meta gegevens worden opgeslagen in de verwante geografie. Zie [Azure-geografi](https://azure.microsoft.com/global-infrastructure/geographies/)voor een lijst met alle Azure-regio's en gerelateerde geografische gebieden.

Op het moment bieden we alleen ondersteuning voor het opslaan van meta gegevens in de Azure-Geografie van Verenigde Staten (Verenigde Staten). De opgeslagen meta gegevens worden versleuteld op rest en geo-redundante mirrors worden binnen de geografie bewaard. Alle klant gegevens, zoals app-instellingen en gebruikers gegevens, bevinden zich op de locatie die de klant kiest en wordt niet beheerd door de service. Meer geographs worden beschikbaar naarmate de service groeit.

Meta gegevens van de service worden gerepliceerd binnen de Azure-Geografie voor nood herstel doeleinden.