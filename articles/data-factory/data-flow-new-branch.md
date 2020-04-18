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
ms.openlocfilehash: daebd6a4a00d82706ceda68f94736f21b8fe5a84
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606398"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Een nieuwe branch maken in de toewijzingvan gegevensstroom

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Voeg een nieuwe branch toe om meerdere sets bewerkingen en transformaties uit te voeren tegen dezelfde gegevensstroom. Het toevoegen van een nieuwe branch is handig als u dezelfde bron wilt gebruiken voor meerdere sinks of voor zelf-samenvoegen van gegevens.

Een nieuwe vertakking kan worden toegevoegd uit de transformatielijst vergelijkbaar met andere transformaties. **Nieuwe branch** is alleen beschikbaar als een actie wanneer er een bestaande transformatie is na de transformatie die u probeert te vertakken.

![Een nieuwe vestiging toevoegen](media/data-flow/new-branch2.png "Een nieuwe vestiging toevoegen")

In het onderstaande voorbeeld leest de gegevensstroom taxiritten. Uitvoer geaggregeerd door zowel dag als leverancier is vereist. In plaats van twee afzonderlijke gegevensstromen te maken die uit dezelfde bron worden gelezen, kan een nieuwe branch worden toegevoegd. Op deze manier kunnen beide aggregaties worden uitgevoerd als onderdeel van dezelfde gegevensstroom. 

![Een nieuwe vestiging toevoegen](media/data-flow/new-branch.png "Een nieuwe vestiging toevoegen")
