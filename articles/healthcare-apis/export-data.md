---
title: De export uitvoeren door $export opdracht aan te roepen in azure API voor FHIR
description: In dit artikel wordt beschreven hoe u de niet-geïdentificeerde export kunt instellen en gebruiken
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: 83509b5f452ab7cf88774561c12d7aa2cf3b46cf
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2020
ms.locfileid: "89482314"
---
# <a name="how-to-export-fhir-data"></a>FHIR-gegevens exporteren

Zie [hier](configure-export-data.md)voor meer informatie over het configureren van export instellingen en het maken van een Azure Storage-account.

## <a name="exporting-fhir-resources-using-export-command"></a>FHIR-resources exporteren met behulp van $export opdracht

Nadat we Azure API voor FHIR voor het exporteren hebben geconfigureerd, kunnen we de $export-opdracht gebruiken om de gegevens uit de service te exporteren naar het opslag account dat is opgegeven tijdens het configureren van de export. Raadpleeg voor meer informatie over het aanroepen van $export-opdracht in FHIR Server documentatie over $export specificatie op [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) . 

De $export opdracht in azure API for FHIR neemt een optionele _ \_ conatiner_ -para meter die kan worden gebruikt om de container op te geven in het geconfigureerde opslag account waarnaar de gegevens moeten worden geëxporteerd.

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

> [!IMPORTANT]
> De Azure-API voor FHIR biedt alleen ondersteuning voor exporteren op systeem niveau zoals gedefinieerd in $export specificatie op [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) . Ook _ \_ omdat_ de query parameter op dit moment wordt ondersteund.

## <a name="exporting-de-identified-data-preview"></a>Het exporteren van de niet-geïdentificeerde gegevens (preview-versie)

De opdracht $export kan ook worden gebruikt voor het exporteren van de niet-geïdentificeerde gegevens van de FHIR-server. Er wordt gebruikgemaakt van de anoniem maken-engine van [FHIR-hulpprogram ma's voor anoniem maken](https://github.com/microsoft/FHIR-Tools-for-Anonymization)en er worden anoniem maken configuratie details gebruikt in query parameters. U kunt uw eigen anoniem maken-configuratie bestand maken of het [voorbeeld configuratie bestand](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method) voor de HIPAA-methode Harbor gebruiken als uitgangs punt. 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

|Query parameter            | Voorbeeld |Optionele| Beschrijving|
|---------------------------|---------|-----------|------------|
| _\_anonymizationConfig_   |DemoConfig.jsop|Vereist voor de niet-geïdentificeerde export |De naam van het configuratie bestand. Bekijk [hier](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format)de indeling van het configuratie bestand. Dit bestand moet worden bewaard in een container met de naam **anoniem maken** binnen hetzelfde Azure-opslag account dat is geconfigureerd als de export locatie. |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|Optioneel voor de niet-geïdentificeerde export|Dit is de ETAG van het configuratie bestand. U kunt de ETAG ophalen met behulp van Azure Storage Explorer van de BLOB-eigenschap|

> [!IMPORTANT]
> Houd er rekening mee dat zowel onbewerkte exporteren als de niet-geïdentificeerde export schrijf bewerkingen naar hetzelfde Azure Storage-account zijn opgegeven als onderdeel van de configuratie van de export. Het is raadzaam om verschillende containers te gebruiken die overeenkomen met de configuratie van de niet-geïdentificeerde configuraties en de gebruikers toegang te beheren op het niveau van de container.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u FHIR-resources kunt exporteren met behulp van $export opdracht, met inbegrip van de gegevens die zijn geïdentificeerd. Vervolgens kunt u uw export gegevens configureren:
 
>[!div class="nextstepaction"]
>[export gegevens configureren](configure-export-data.md)
