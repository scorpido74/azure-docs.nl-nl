---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "67175982"
---
Nadat u de beschikbaarheids groep-listener hebt gemaakt, kan het nodig zijn om de RegisterAllProvidersIP-en HostRecordTTL-cluster parameters voor de listener-resource aan te passen. Deze para meters kunnen de Herverbindings tijd na een failover verminderen, waardoor verbindingstime-outs kunnen worden voor komen. Zie [een beschikbaarheids groep maken of configureren](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover)voor meer informatie over deze para meters en voorbeeld code.

