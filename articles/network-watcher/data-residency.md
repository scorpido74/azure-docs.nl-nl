---
title: Data locatie voor Azure Network Watcher | Microsoft Docs
description: Informatie over het locatie van gegevens voor de Network Watcher-service
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: damendo
ms.openlocfilehash: dff5519c1b36a0a7738cb2529c2f4460ecf61e48
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88117832"
---
# <a name="data-residency-for-azure-network-watcher"></a>Data locatie voor Azure Network Watcher
Azure Network Watcher slaat geen klant gegevens op, met uitzonde ring van de service verbindings monitor (preview).


## <a name="connection-monitor-preview-data-residency"></a>Verbindings monitor (preview)-gegevens locatie
Met de service *verbindings controle (preview)* worden klant gegevens opgeslagen. Deze gegevens worden automatisch opgeslagen door Network Watcher in één regio. De *verbindings monitor (preview-versie)* wordt dus automatisch voldaan aan de vereisten voor de gegevens locatie van de regio, zoals opgegeven in het [vertrouwens centrum](https://azuredatacentermap.azurewebsites.net/).

## <a name="singapore-data-residency"></a>Singapore data locatie

In Azure is de functie voor het inschakelen van klant gegevens in één regio momenteel alleen beschikbaar in de regio Zuidoost-Azië (Singapore) van de Azië en Stille Oceaan geo. Voor alle andere regio's worden klant gegevens opgeslagen in geografische regio. Zie [vertrouwens centrum](https://azuredatacentermap.azurewebsites.net/)voor meer informatie.

> [!Note]
> **Eerdere replicatie** Klanten hebben de mogelijkheid om IP-adressen van eind gebruikers op te slaan met hun Network Watcher-exemplaar, zodat Network Watcher de veranderingen in de bereik baarheid, latentie en netwerk topologie kunt controleren die betrekking hebben op de IP-adressen van de eind gebruiker. Deze IP-adressen voor eind gebruikers kunnen worden geclassificeerd als klant gegevens. Vanaf 15 juli 2020 heeft Network Watcher deze gegevens opgeslagen in één regio. (Klant gegevens worden niet meer gerepliceerd naar HK.) Deze gegevens worden niet meer gerepliceerd naar de HK. Deze klant gegevens zijn en zijn op rest versleuteld.
> 
> Als deze klant gegevens toegankelijk zijn voor een derde partij, zou de derde partij het IP-adres kunnen weten, maar geen toegang verlenen tot de computer of het apparaat dat is gekoppeld aan het IP-adres. Network Watcher meent dat er geen derden toegang hebben tot deze klant gegevens in het HK.

## <a name="next-steps"></a>Volgende stappen

* Lees een overzicht van [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).
