---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175982"
---
Nadat u de listener voor de beschikbaarheidsgroep hebt gemaakt, kan het nodig zijn om de clusterparameters RegisterAllProvidersIP en HostRecordTTL voor de listenerbron aan te passen. Deze parameters kunnen de herverbindingstijd na een failover verkorten, waardoor time-outs van de verbinding kunnen worden voorkomen. Zie [Een authenticiteitsgroeplistener maken of configureren](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover)voor meer informatie over deze parameters en voorbeeldcode.

