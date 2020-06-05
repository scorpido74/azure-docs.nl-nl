---
title: Common data model-indeling
description: Gegevens transformeren met behulp van het meta gegevens systeem van common data model
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 2faa14ef7724bb9cccfe425569539f5ef0621a28
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84435392"
---
# <a name="common-data-model-format-in-azure-data-factory"></a>Gemeen schappelijke gegevens model indeling in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Het gegevens systeem van het common data model (CDM) maakt het mogelijk om gegevens en de betekenis ervan eenvoudig te delen tussen toepassingen en bedrijfs processen. Zie het overzicht van [common data model](https://docs.microsoft.com/common-data-model/) voor meer informatie.

In Azure Data Factory kunnen gebruikers transformeren naar en van CDM-entiteiten die zijn opgeslagen in [Azure data Lake Store Gen2](connector-azure-data-lake-storage.md) (ADLS Gen2) met toewijzing van gegevens stromen.

## <a name="mapping-data-flow-properties"></a>Eigenschappen van gegevens stroom toewijzen

Het common data model is beschikbaar als een [inline-gegevensset](data-flow-source.md#inline-datasets) in het toewijzen van gegevens stromen als een bron en een sink.

### <a name="source-properties"></a>Bron eigenschappen

De onderstaande tabel geeft een lijst van de eigenschappen die worden ondersteund door een CDM-bron.

| Naam | Beschrijving | Vereist | Toegestane waarden | Eigenschap gegevens stroom script |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Indeling | De is altijd`cdm` | ja | `cdm` | formaat |
| Meta gegevens indeling | Waar de entiteits verwijzing naar de gegevens zich bevindt. Als u CDM versie 1,0 gebruikt, kiest u Manifest. Als u vóór 1,0 een CDM-versie gebruikt, kiest u model. json. | Ja | `'manifest'` of `'model'` | manifestType |
| Hoofd locatie: container | Container naam van de map CDM | ja | Tekenreeks | System |
| Hoofd locatie: mappad | Locatie van de hoofdmap van de map CDM | ja | Tekenreeks | folderPath |
| Manifest bestand: pad naar entiteit | Mappad van de entiteit binnen de hoofdmap | nee | Tekenreeks | entityPath |
| Manifest bestand: manifest naam | Naam van het manifest bestand  | Ja, als u een manifest gebruikt | Tekenreeks | manifestnaam |
| Filteren op laatst gewijzigd | Kiezen of bestanden moeten worden gefilterd op basis van het tijdstip waarop deze voor het laatst zijn gewijzigd | nee | Tijdstempel | modifiedAfter <br> modifiedBefore | 
| Gekoppelde schema service | De gekoppelde service waar de verzameling zich bevindt | Ja, als u een manifest gebruikt | `'adlsgen2'` of `'github'` | corpusStore | 
| Container voor entiteits verwijzing | Container verzameling is in | Ja, als u Manifest en verzameling in ADLS Gen2 gebruikt | Tekenreeks | adlsgen2_fileSystem |
| Opslag plaats voor entiteit verwijzing | Naam van de GitHub-opslagplaats | Ja, als u Manifest en verzameling in GitHub gebruikt | Tekenreeks | github_repository |
| Vertakking van entiteits verwijzing | GitHub-opslag plaats vertakking | Ja, als u Manifest en verzameling in GitHub gebruikt | Tekenreeks |  github_branch |
| Map verzameling | de hoofd locatie van de verzameling | Ja, als u een manifest gebruikt | Tekenreeks | corpusPath |
| Verzameling entiteit | Pad naar entiteits verwijzing | ja | Tekenreeks | vennootschap |
| Geen bestanden gevonden | Als deze eigenschap waar is, wordt er geen fout gegenereerd als er geen bestanden worden gevonden | nee | `true` of `false` | ignoreNoFilesFound |

![CDM-bron](media/format-common-data-model/data-flow-source.png)

### <a name="sink-properties"></a>Eigenschappen van Sink

De onderstaande tabel geeft een lijst van de eigenschappen die worden ondersteund door een CDM-sink.

| Naam | Beschrijving | Vereist | Toegestane waarden | Eigenschap gegevens stroom script |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Indeling | De is altijd`cdm` | ja | `cdm` | formaat |
| Hoofd locatie: container | Container naam van de map CDM | ja | Tekenreeks | System |
| Hoofd locatie: mappad | Locatie van de hoofdmap van de map CDM | ja | Tekenreeks | folderPath |
| Manifest bestand: pad naar entiteit | Mappad van de entiteit binnen de hoofdmap | nee | Tekenreeks | entityPath |
| Manifest bestand: manifest naam | Naam van het manifest bestand  | ja | Tekenreeks | manifestnaam |
| Gekoppelde schema service | De gekoppelde service waar de verzameling zich bevindt | ja | `'adlsgen2'` of `'github'` | corpusStore | 
| Container voor entiteits verwijzing | Container verzameling is in | Ja, als verzameling in ADLS Gen2 | Tekenreeks | adlsgen2_fileSystem |
| Opslag plaats voor entiteit verwijzing | Naam van de GitHub-opslagplaats | Ja, als verzameling in GitHub | Tekenreeks | github_repository |
| Vertakking van entiteits verwijzing | GitHub-opslag plaats vertakking | Ja, als verzameling in GitHub | Tekenreeks |  github_branch |
| Map verzameling | de hoofd locatie van de verzameling | ja | Tekenreeks | corpusPath |
| Verzameling entiteit | Pad naar entiteits verwijzing | ja | Tekenreeks | vennootschap |
| Pad partitioneren | Locatie waar de partitie wordt geschreven | nee | Tekenreeks | partitionPath |
| De map wissen | Als de doelmap vóór het schrijven is gewist | nee | `true` of `false` | afkappen |
| Type indeling | Kiezen om de Parquet-indeling op te geven | nee | `parquet`Indien opgegeven | subformat |
| Kolom scheidings teken | Als u naar DelimitedText schrijft, kolommen beperken | Ja, als u naar DelimitedText schrijft | Tekenreeks | columnDelimiter |
| Eerste rij als koptekst | Als u DelimitedText gebruikt, of de kolom namen worden toegevoegd als koptekst | nee | `true` of `false` | columnNamesAsHeader |

![CDM-bron](media/format-common-data-model/data-flow-sink.png)

## <a name="next-steps"></a>Volgende stappen

Maak een [bron transformatie](data-flow-source.md) in de toewijzing van gegevens stroom.