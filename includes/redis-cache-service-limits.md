---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 1ab6243be39bf30bc060ed5745fbf600924743a9
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839227"
---
| Resource | Limiet |
| --- | --- |
| Cachegrootte |1,2 TB |
| Databases |64 |
| Maximum aantal verbonden clients |40,000 |
| Azure cache voor redis-replica's, voor hoge Beschik baarheid |1 |
| Shards in een premium-cache met clustering |10 |

Azure Redis-Cache wordt beperkt en -grootten zijn verschillend voor elke prijscategorie. Zie [Azure-cache voor redis prijzen voor](https://azure.microsoft.com/pricing/details/cache/)een overzicht van de prijs categorieën en de bijbehorende grootte.

Zie voor meer informatie over Azure Cache voor Redis-configuratie limieten, [configuratie van de standaardconfiguratie voor Redis-server](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration).

Omdat de configuratie en beheer van Azure Cache voor instanties van Redis wordt uitgevoerd door Microsoft, worden niet alle Redis-opdrachten in Azure Cache ondersteund voor Redis. Zie voor meer informatie, [Redis-opdrachten niet ondersteund in Azure Cache voor Redis](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

