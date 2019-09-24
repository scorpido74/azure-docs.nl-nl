---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/23/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8908ded31b96aac50db1fc25e92c2c0a8e960453
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219028"
---
Lokaal redundante opslag (LRS) repliceert uw gegevens drie keer binnen één Data Center. LRS biedt ten minste 99,999999999% (11 Nines) duurzaamheid van objecten in een bepaald jaar. LRS is de optie voor de laagste kosten replicatie en biedt de minste duurzaamheid ten opzichte van andere opties.

Als er sprake is van een ramp op datacenter niveau (bijvoorbeeld brand of flooding), worden alle replica's in een opslag account met LRS mogelijk verloren of onherstelbaar. Om dit risico te beperken, raadt micro soft aan gebruik te maken van zone-redundante opslag (ZRS), geografisch redundante opslag (GRS) of geo-zone-redundante opslag (GZRS).

Een schrijf aanvraag naar een LRS-opslag account wordt alleen weer gegeven als de gegevens naar alle drie de replica's zijn geschreven.

U kunt LRS gebruiken in de volgende scenario's:

* Als uw toepassing gegevens opslaat die gemakkelijk kunnen worden geconstrueerd als er gegevens verlies optreedt, kunt u kiezen voor LRS.
* Sommige toepassingen zijn beperkt tot het repliceren van gegevens in een land/regio als gevolg van vereisten voor gegevens beheer. In sommige gevallen kunnen de gekoppelde regio's waarvan de gegevens voor GRS-accounts worden gerepliceerd, zich in een ander land of een andere regio bevinden. Zie [Azure-regio's](https://azure.microsoft.com/regions/)voor meer informatie over gekoppelde regio's.
