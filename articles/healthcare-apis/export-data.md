---
title: De export uitvoeren door $export opdracht aan te roepen in azure API voor FHIR
description: In dit artikel wordt beschreven hoe u FHIR gegevens exporteert met $export
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: 74fe09895f49cc9f7c3cdf6b6c97c1624c3e9c0b
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91839823"
---
# <a name="how-to-export-fhir-data"></a>FHIR-gegevens exporteren


Met de functie voor bulk export kunnen gegevens uit de FHIR-server worden geëxporteerd volgens de [FHIR-specificatie](https://hl7.org/fhir/uv/bulkdata/export/index.html). 

Voordat u $export gebruikt, moet u ervoor zorgen dat de Azure-API voor FHIR is geconfigureerd om deze te gebruiken. Raadpleeg [de pagina export gegevens configureren](configure-export-data.md)voor meer informatie over het configureren van export instellingen en het maken van een Azure Storage-account.

## <a name="using-export-command"></a>$export opdracht gebruiken

Na het configureren van de Azure-API voor FHIR voor het exporteren, kunt u de $export opdracht gebruiken om de gegevens uit de service te exporteren. De gegevens worden opgeslagen in het opslag account dat u hebt opgegeven tijdens het configureren van de export. Lees voor meer informatie over het aanroepen van $export-opdracht in FHIR-Server documentatie over de [$export-specificatie](https://hl7.org/Fhir/uv/bulkdata/export/index.html). 

De $export opdracht in azure API for FHIR neemt een optionele _ \_ container_ parameter op die de container specificeert in het geconfigureerde opslag account waarin de gegevens moeten worden geëxporteerd. Als er een container is opgegeven, worden de gegevens naar die container geëxporteerd in een nieuwe map met de naam. Als de container niet is opgegeven, wordt deze geëxporteerd naar een nieuwe container met een wille keurig gegenereerde naam. 

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

## <a name="supported-scenarios"></a>Ondersteunde scenario's

Azure API voor FHIR ondersteunt $export op het niveau van het systeem, de patiënt en de groep. Voor het exporteren van groepen exporteren we alle gerelateerde resources, maar exporteren we de kenmerken van de groep niet.

> [!Note] 
> $export worden dubbele resources geëxporteerd als de resource zich in een compartiment van meer dan één resource bevindt of zich in meerdere groepen bevindt.

Daarnaast wordt het controleren van de export status via de URL die wordt geretourneerd door de locatie-header tijdens de wachtrij, ondersteund in combi natie met het annuleren van de huidige export taak wordt ondersteund.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u FHIR-resources kunt exporteren met behulp van $export opdracht. Daarna kunt u de ondersteunde functies bekijken:
 
>[!div class="nextstepaction"]
>[Ondersteunde functies](fhir-features-supported.md)
