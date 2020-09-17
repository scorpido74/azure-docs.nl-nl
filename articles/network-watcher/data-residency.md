---
title: Data locatie voor Azure Network Watcher | Microsoft Docs
description: In dit artikel vindt u informatie over het locatie van gegevens voor de Azure Network Watcher-service.
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
ms.openlocfilehash: 9451b6636f2f87806e3d1e39fec4e9e4d4390485
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2020
ms.locfileid: "90706829"
---
# <a name="data-residency-for-azure-network-watcher"></a>Data locatie voor Azure Network Watcher
Met uitzonde ring van de service verbindings monitor (preview-versie), worden klant gegevens niet opgeslagen in azure Network Watcher.


## <a name="connection-monitor-preview-data-residency"></a>Verbindings monitor (preview)-gegevens locatie
Met de service verbindings controle (preview) worden klant gegevens opgeslagen. Deze gegevens worden automatisch opgeslagen door Network Watcher in één regio. De verbindings monitor (preview) wordt dus automatisch voldaan aan de vereisten voor de gegevens locatie van de regio, inclusief de vereisten die zijn opgegeven in het [vertrouwens centrum](https://azuredatacentermap.azurewebsites.net/).

## <a name="singapore-data-residency"></a>Gegevenslocatie Singapore

In Azure is de functie waarmee klant gegevens in één regio kunnen worden opgeslagen, momenteel alleen beschikbaar in de regio Zuidoost-Azië (Singapore) van de Azië en Stille Oceaan geo. Voor alle andere regio's worden klant gegevens opgeslagen in geografische regio. Zie het [vertrouwens centrum](https://azuredatacentermap.azurewebsites.net/)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Lees een overzicht van [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).
