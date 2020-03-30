---
title: Eigenschappen voor gegevensopname voor Azure Data Explorer
description: Meer informatie over de verschillende gegevensinname-eigenschappen die worden ondersteund door Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 48bce1bf03a0a4c8d81fff7ae54e0b22261b70f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109574"
---
# <a name="azure-data-explorer-data-ingestion-properties"></a>Eigenschappen van Azure Data Explorer voor het innemen van gegevens 

Gegevensopname is het proces waarbij gegevens aan een tabel worden toegevoegd en beschikbaar worden gesteld voor query's in Azure Data Explorer. U voegt eigenschappen toe aan `with` de innameopdracht na het trefwoord.

## <a name="ingestion-properties"></a>Inname-eigenschappen

In de volgende tabel worden de eigenschappen weergegeven die worden ondersteund door Azure Data Explorer, worden deze beschreven en worden voorbeelden gegeven: 

|Eigenschap              |Beschrijving                                              |Voorbeeld                                             |
|----------------------|---------------------------------------------------------|----------------------------------------------------|
|`ingestionMapping`    |Een tekenreekswaarde die aangeeft hoe gegevens uit het bronbestand kunnen worden toegewezen aan de werkelijke kolommen in de tabel. Definieer `format` de waarde met het relevante toewijzingstype. Zie [gegevenstoewijzingen](/azure/kusto/management/mappings).|`with (format="json", ingestionMapping = "[{\"column\":\"rownumber\", \"Properties\":{\"Path\":\"$.RowNumber\"}}, {\"column\":\"rowguid\", \"Properties\":{\"Path\":\"$.RowGuid\"}}]")`<br>(afgeschaft: `avroMapping`, `csvMapping`, `jsonMapping`) |
|`ingestionMappingReference`|Een tekenreekswaarde die aangeeft hoe gegevens uit het bronbestand kunnen worden toegewezen aan de werkelijke kolommen in de tabel met een benoemd toewijzingsbeleidsobject. Definieer `format` de waarde met het relevante toewijzingstype. Zie [gegevenstoewijzingen](/azure/kusto/management/mappings).|`with (format="csv", ingestionMappingReference = "Mapping1")`<br>(afgeschaft: `avroMappingReference`, `csvMappingReference`, `jsonMappingReference`)|
|`creationTime` |De datumtijdwaarde (opgemaakt als een ISO8601-tekenreeks) die moet worden gebruikt op de creatietijd van de opgenomen gegevensomvang. Indien niet gespecificeerd, wordt`now()`de huidige waarde ( ) gebruikt. Het overschrijven van de standaardis handig bij het innemen van oudere gegevens, zodat het bewaarbeleid correct wordt toegepast.|`with (creationTime="2017-02-13T11:09:36.7992775Z")`|
|`extend_schema`|Een Booleaanse waarde die, indien opgegeven, de opdracht instrueert `false`om het schema van de tabel uit te breiden (standaard naar ). Deze optie is `.append` `.set-or-append` alleen van toepassing op en opdrachten. De enige toegestane schema-extensies hebben extra kolommen toegevoegd aan de tabel aan het einde.|Als het oorspronkelijke `(a:string, b:int)`tabelschema een geldige `(a:string, b:int, c:datetime, d:string)`schema-extensie is, maar `(a:string, c:datetime)` niet geldig zou zijn|
|`folder` |Voor [inname-van-query](/azure/kusto/management/data-ingestion/ingest-from-query) opdrachten, de map toe te wijzen aan de tabel. Als de tabel al bestaat, overschrijft deze eigenschap de map van de tabel.|`with (folder="Tables/Temporary")`|
|`format` |De gegevensindeling (zie [ondersteunde gegevensindelingen).](ingestion-supported-formats.md)|`with (format="csv")`|
|`ingestIfNotExists`|Een tekenreekswaarde die, indien opgegeven, voorkomt dat opname slaagt `ingest-by:` als de tabel al gegevens heeft die zijn getagd met een tag met dezelfde waarde. Dit zorgt voor idempotent data opname. Zie voor meer informatie [inname door: tags](/azure/kusto/management/extents-overview#ingest-by-extent-tags).|De `with (ingestIfNotExists='["Part0001"]', tags='["ingest-by:Part0001"]')` eigenschappen geven aan dat `ingest-by:Part0001` als gegevens met de tag al bestaan, de huidige opname niet voltooien. Als deze tag nog niet bestaat, moet deze tag zijn ingesteld (in het geval dat een toekomstige opname probeert dezelfde gegevens opnieuw in te nemen.)|
|`ignoreFirstRecord` |Een Booleaanse waarde die, indien ingesteld op, `true`aangeeft dat inname de eerste record van elk bestand moet negeren. Deze eigenschap is handig `CSV`voor bestanden in en vergelijkbare indelingen, als de eerste record in het bestand de kolomnamen zijn. Standaard wordt `false` aangenomen.|`with (ignoreFirstRecord=false)`|
|`persistDetails` |Een Booleaanse waarde die, indien opgegeven, aangeeft dat de opdracht de gedetailleerde resultaten moet blijven behouden (zelfs als deze is geslaagd), zodat de opdracht [.show-bewerkingsdetails](/azure/kusto/management/operations#show-operation-details) deze kan ophalen. Standaard instellingen `false`voor .|`with (persistDetails=true)`|
|`policy_ingestiontime`|Een Booleaanse waarde die, indien opgegeven, beschrijft of u het [innametijdbeleid](/azure/kusto/management/ingestiontimepolicy) wilt inschakelen voor een tabel die door deze opdracht is gemaakt. De standaardwaarde is `true`.|`with (policy_ingestiontime=false)`|
|`recreate_schema` |Een Booleaanse waarde die, indien opgegeven, beschrijft of de opdracht het schema van de tabel kan opnieuw maken. Deze eigenschap is `.set-or-replace` alleen van toepassing op de opdracht. Deze eigenschap heeft voorrang `extend_schema` op de eigenschap als beide zijn ingesteld.|`with (recreate_schema=true)`|
|`tags`|Een lijst met [tags](/azure/kusto/management/extents-overview#extent-tagging) die moeten worden gekoppeld aan de ingenomen gegevens, opgemaakt als JSON-tekenreeks |`with (tags="['Tag1', 'Tag2']")`|
|`validationPolicy`|Een JSON-tekenreeks die aangeeft welke validaties tijdens inname moeten worden uitgevoerd. Zie [Gegevensopname](/azure/kusto/management/data-ingestion/) voor een uitleg van de verschillende opties.| `with (validationPolicy='{"ValidationOptions":1, "ValidationImplications":1}')`(Dit is eigenlijk het standaardbeleid)|
|`zipPattern`|Gebruik deze eigenschap bij het innemen van gegevens uit opslag die een ZIP-archief heeft. Dit is een tekenreekswaarde die de reguliere expressie aangeeft die moet worden gebruikt bij het selecteren van welke bestanden in het ZIP-archief moeten worden opgenomen.  Alle andere bestanden in het archief worden genegeerd.|`with (zipPattern="*.csv")`|

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het innemen van gegevens](/azure/data-explorer/ingest-data-overview)
* Meer informatie over [ondersteunde gegevensindelingen](ingestion-supported-formats.md)
