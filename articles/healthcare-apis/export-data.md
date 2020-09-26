---
title: De export uitvoeren door $export opdracht aan te roepen in azure API voor FHIR
description: In dit artikel wordt beschreven hoe u de niet-geïdentificeerde export kunt instellen en gebruiken
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: ecc2134d1a528ee22710cb447f996e0c5e31a8de
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91308177"
---
# <a name="how-to-export-fhir-data"></a>FHIR-gegevens exporteren

Voordat u $export gebruikt, moet u ervoor zorgen dat de Azure-API voor FHIR is geconfigureerd om deze te gebruiken. Raadpleeg [de pagina export gegevens configureren](configure-export-data.md)voor meer informatie over het configureren van export instellingen en het maken van een Azure Storage-account.

## <a name="using-export-command"></a>$export opdracht gebruiken

Na het configureren van de Azure-API voor FHIR voor het exporteren, kunt u de $export opdracht gebruiken om de gegevens uit de service te exporteren. De gegevens worden opgeslagen in het opslag account dat u hebt opgegeven tijdens het configureren van de export. Lees voor meer informatie over het aanroepen van $export-opdracht in FHIR-Server documentatie over de [$export-specificatie](https://hl7.org/Fhir/uv/bulkdata/export/index.html). 

De $export opdracht in azure API for FHIR neemt een optionele _ \_ container_ parameter op die de container specificeert in het geconfigureerde opslag account waarin de gegevens moeten worden geëxporteerd.

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

## <a name="supported-scenarios"></a>Ondersteunde scenario's

Azure API voor FHIR ondersteunt $export op het niveau van het systeem, de patiënt en de groep. Voor het exporteren van groepen exporteren we alle gerelateerde resources, maar exporteren we de kenmerken van de groep niet.

> [!Note] 
> $export worden dubbele resources geëxporteerd als de resource zich in een compartiment van meer dan één resource bevindt of zich in meerdere groepen bevindt.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u FHIR-resources kunt exporteren met behulp van $export opdracht. Daarna kunt u de ondersteunde functies bekijken
 
>[!div class="nextstepaction"]
>[Ondersteunde functies](fhir-features-supported.md)
