---
title: Referentie knooppunt voor gegevens stroom van Azure Data Factory toewijzing
description: Data Factory gegevens stroom voegt een referentie knooppunt toe voor samen voegingen, zoek acties, samen voegingen
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 4ed17114cc0ce586c68c5b3e087acffdb82ea96c
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030489"
---
# <a name="mapping-data-flow-reference-node"></a>Referentie knooppunt van de gegevens stroom koppelen



Referentie knooppunt verwijzings ![knooppunt](media/data-flow/referencenode.png "")

Er wordt automatisch een referentie knooppunt aan het canvas toegevoegd om aan te duiden dat het knoop punt waaraan het is gekoppeld, verwijst naar een ander bestaand knoop punt op het canvas. U kunt een referentie knooppunt beschouwen als een wijzer of verwijzing naar een andere gegevensstroom transformatie.

Bijvoorbeeld: Wanneer u meerdere gegevens stromen samenvoegt of samenvoegt, kan het gegevensstroom canvas een referentie knooppunt toevoegen met de naam en instellingen van de niet-primaire binnenkomende stroom.

Het referentie knooppunt kan niet worden verplaatst of verwijderd. U kunt echter klikken op het knoop punt om de oorspronkelijke transformatie-instellingen te wijzigen.

De UI-regels die bepalen wanneer de gegevens stroom het referentie knooppunt toevoegt, zijn gebaseerd op beschik bare ruimte en verticale afstand tussen rijen.
