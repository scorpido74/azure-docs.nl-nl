---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 1ab404b838af65dcb75395dfeee1ca0553e497a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176705"
---
## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Structuurdefinitie voor rechthoekige gegevenssets opgeven
De structuursectie in de setssets JSON is een **optionele** sectie voor rechthoekige tabellen (met rijen & kolommen) en bevat een verzameling kolommen voor de tabel. U gebruikt de structuursectie voor het verstrekken van tekstinformatie voor typeconversies of het uitvoeren van kolomtoewijzingen. In de volgende secties worden deze functies in detail beschreven. 

Elke kolom bevat de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| name |Naam van de kolom. |Ja |
| type |Gegevenstype van de kolom. Zie hieronder de sectie tekstconversies voor meer informatie over wanneer u tekstgegevens moet opgeven |Nee |
| Cultuur |.NET-gebaseerde cultuur die moet worden gebruikt wanneer tekst is opgegeven en .NET-type Datetime of Datetimeoffset is. Standaard is "en-us". |Nee |
| formaat |Tekenreeks Opmaak die moet worden gebruikt wanneer tekst is opgegeven en .NET-type Datumtijd of Datumtijdverschuiving is. |Nee |

In het volgende voorbeeld wordt de structuursectie JSON weergegeven voor een tabel met drie kolommen met gebruiks-, naam- en laatsteinlogdatum.

```json
"structure": 
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```

Gebruik de volgende richtlijnen voor het opnemen van "structuur" informatie en wat op te nemen in de **structuur** sectie.

* **Voor gestructureerde gegevensbronnen** die gegevensschema opslaan en informatie typen samen met de gegevens zelf (bronnen zoals SQL Server, Oracle, Azure-tabel enz.), moet u de sectie 'structuur' alleen opgeven als u kolomtoewijzing van specifieke bronkolommen wilt doen naar specifieke kolommen in gootsteen en hun namen niet hetzelfde zijn (zie details in kolomtoewijzing hieronder). 
  
    Zoals hierboven vermeld, is de typeinformatie optioneel in de sectie "structuur". Voor gestructureerde bronnen is tekstinformatie al beschikbaar als onderdeel van de definitie van gegevenssets in het gegevensarchief, dus u moet geen tekstinformatie opnemen wanneer u de sectie 'structuur' opneemt.
* **Voor schema's op leesgegevensbronnen (met name Azure blob)** u ervoor kiezen om gegevens op te slaan zonder schema of typegegevens op te slaan met de gegevens. Voor dit soort gegevensbronnen moet u in de volgende 2 gevallen "structuur" opnemen:
  * U wilt kolomtoewijzing doen.
  * Wanneer de gegevensset een bron is in een kopieeractiviteit, u tekstgegevens in 'structuur' verstrekken en gebruikt de gegevensfabriek deze tekstgegevens voor conversie naar native typen voor de gootsteen. Zie [Gegevens verplaatsen van en naar het Azure Blob-artikel](../articles/data-factory/v1/data-factory-azure-blob-connector.md) voor meer informatie.

### <a name="supported-net-based-types"></a>Ondersteund. OP NETTEN gebaseerde typen
Gegevensfabriek ondersteunt de volgende CLS-compatibele .NET-gebaseerde typewaarden voor het verstrekken van tekstinformatie in 'structuur' voor schema op leesgegevensbronnen zoals Azure blob.

* Int16
* Int32 
* Int64
* Enkel
* Double
* Decimal
* Byte
* Booleaanse waarde
* Tekenreeks 
* GUID
* Datum/tijd
* Datumtijdverschuiving
* Periode 

Voor Datetime & Datetimeoffset u ook optioneel 'cultuur' & tekenreeks 'opmaak' opgeven om het ontken en maken van uw aangepaste Datetime-tekenreeks te vergemakkelijken. Zie voorbeeld voor typeconversie hieronder.

