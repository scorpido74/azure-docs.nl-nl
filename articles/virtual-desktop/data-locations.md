---
title: Gegevenslocaties voor Windows Virtual Desktop - Azure
description: Een kort overzicht van de gegevens en metadata van Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5f48586f38c310c77c61f470d3e2fbc819c33f71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128055"
---
# <a name="data-locations-for-windows-virtual-desktop"></a>Gegevenslocaties voor Windows Virtual Desktop

Windows Virtual Desktop is momenteel beschikbaar voor alle geografische locaties. In eerste instantie kunnen servicemetadata alleen worden opgeslagen in de geografie van de Verenigde Staten (VS). Beheerders kunnen de locatie kiezen om gebruikersgegevens op te slaan wanneer ze virtuele machines en bijbehorende services, zoals bestandsservers, maken. Meer informatie over Azure-regio's vindt u op de [Azure-datacenterkaart.](https://azuredatacentermap.azurewebsites.net/)

>[!NOTE]
>Microsoft beheert of beperkt niet de regio's waar u of uw gebruikers toegang hebben tot uw gebruikers- en app-specifieke gegevens.

>[!IMPORTANT]
>Windows Virtual Desktop slaat algemene metagegevens op, zoals tenantnamen, namen van hostgroepen, namen van app-groepen en gebruikershoofdnamen in een datacenter in de Verenigde Staten. De opgeslagen metadata wordt versleuteld in rust, en geo-redundante spiegels worden onderhouden binnen de Verenigde Staten. Alle klantgegevens, zoals app-instellingen en gebruikersgegevens, bevinden zich op de locatie die de klant kiest en niet wordt beheerd door de service.

Servicemetagegevens worden in de Verenigde Staten gerepliceerd voor noodhersteldoeleinden.