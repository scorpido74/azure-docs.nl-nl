---
title: Meerdere vertakkingen in toewijzing van gegevens stroom
description: Gegevens stromen repliceren in toewijzing van gegevens stroom met meerdere vertakkingen
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 71fb9f1ba9952be0e6b3910dd1079aa6d3c0482d
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834505"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Een nieuwe vertakking maken in de stroom voor het toewijzen van gegevens

Voeg een nieuwe vertakking toe om meerdere bewerkingen en trans formaties voor dezelfde gegevens stroom uit te voeren. Het toevoegen van een nieuwe vertakking is handig als u dezelfde bron wilt gebruiken voor meerdere sinks of voor het samen voegen van gegevens.

Een nieuwe vertakking kan vanuit de transformatie lijst worden toegevoegd, vergelijkbaar met andere trans formaties. **Nieuwe vertakkingen** zijn alleen beschikbaar als actie wanneer er een trans formatie is die volgt op de trans formatie die u wilt vertakkingen.

![Een nieuwe vertakking toevoegen](media/data-flow/new-branch2.png "Een nieuwe vertakking toevoegen")

In het onderstaande voor beeld lezen de gegevens stroom de taxi reis gegevens. Uitvoer geaggregeerd per dag en leverancier is vereist. In plaats van twee afzonderlijke gegevens stromen te maken die van dezelfde bron worden gelezen, kan een nieuwe vertakking worden toegevoegd. Op deze manier kunnen beide aggregaties worden uitgevoerd als onderdeel van dezelfde gegevens stroom. 

![Een nieuwe vertakking toevoegen](media/data-flow/new-branch.png "Een nieuwe vertakking toevoegen")
