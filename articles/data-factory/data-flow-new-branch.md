---
title: Data flow koppelen nieuwe vertakkings transformatie
description: Nieuwe vertakkings transformatie Azure Data Factory toewijzings gegevens stroom
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/12/2019
ms.openlocfilehash: b4617689fe1ab14856bde9a4e8134b12aa6d815b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930302"
---
# <a name="azure-data-factory-mapping-data-flow-new-branch-transformation"></a>Nieuwe vertakkings transformatie Azure Data Factory toewijzings gegevens stroom

![Vertakkings opties](media/data-flow/menu.png "Menuknop")

Vertakkingen nemen de huidige gegevens stroom in uw gegevens stroom en repliceert deze naar een andere stroom. Gebruik nieuwe vertakking om meerdere sets bewerkingen en trans formaties uit te voeren op dezelfde gegevens stroom.

Voor beeld: uw gegevens stroom heeft een bron transformatie met een geselecteerde set kolommen en gegevens type conversies. Vervolgens plaatst u onmiddellijk na die bron een afgeleide kolom. In de afgeleide kolom maakt u een nieuw veld waarin de voor naam en achternaam worden gecombineerd om een nieuw veld "volledige naam" te maken.

U kunt deze nieuwe stroom behandelen met een reeks trans formaties en een Sink op één rij en nieuwe vertakking gebruiken om een kopie van die stroom te maken, waar u dezelfde gegevens kunt transformeren met een andere set trans formaties. Door het transformeren van de gekopieerde gegevens in een afzonderlijke vertakking kunt u die gegevens vervolgens op een andere locatie ontsleutelen.

> [!NOTE]
> ' Nieuwe vertakking ' wordt alleen weer gegeven als een actie in het menu + trans formatie wanneer er een volgende trans formatie wordt gevolgd door de huidige locatie waar u een vertakking wilt maken. Dit betekent dat de optie ' nieuwe vertakking ' aan het einde niet wordt weer gegeven, totdat u een andere trans formatie hebt toegevoegd na de selectie

![Branche](media/data-flow/branch2.png "Vertakking 2")
