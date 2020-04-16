---
title: Meerdere branches in kaartgegevensstroom
description: Gegevensstromen repliceren in kaartgegevensstroom met meerdere branches
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: af356641b4588529aea25826ff180707ff1ef4e2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413606"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Een nieuwe branch maken in de toewijzingvan gegevensstroom

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Voeg een nieuwe branch toe om meerdere sets bewerkingen en transformaties uit te voeren tegen dezelfde gegevensstroom. Het toevoegen van een nieuwe branch is handig als u dezelfde bron wilt gebruiken voor meerdere sinks of voor zelf-samenvoegen van gegevens.

Een nieuwe vertakking kan worden toegevoegd uit de transformatielijst vergelijkbaar met andere transformaties. **Nieuwe branch** is alleen beschikbaar als een actie wanneer er een bestaande transformatie is na de transformatie die u probeert te vertakken.

![Een nieuwe vestiging toevoegen](media/data-flow/new-branch2.png "Een nieuwe vestiging toevoegen")

In het onderstaande voorbeeld leest de gegevensstroom taxiritten. Uitvoer geaggregeerd door zowel dag als leverancier is vereist. In plaats van twee afzonderlijke gegevensstromen te maken die uit dezelfde bron worden gelezen, kan een nieuwe branch worden toegevoegd. Op deze manier kunnen beide aggregaties worden uitgevoerd als onderdeel van dezelfde gegevensstroom. 

![Een nieuwe vestiging toevoegen](media/data-flow/new-branch.png "Een nieuwe vestiging toevoegen")
