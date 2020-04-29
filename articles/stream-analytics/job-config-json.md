---
title: Azure Stream Analytics JobConfig. json-velden
description: In dit artikel vindt u een lijst met de ondersteunde velden voor het bestand JobConfig. json dat wordt gebruikt voor Azure Stream Analytics het maken van taken in Visual Studio code.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 27ed553035ce9d7abf57ffe93078df9c17b8408c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77617955"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>Azure Stream Analytics JobConfig. json-velden

De volgende velden worden ondersteund in het bestand *JobConfig. json* dat wordt gebruikt om [een Azure stream Analytics-taak te maken met Visual Studio code](quick-create-vs-code.md).

```json
{
    "DataLocale": "string",
    "OutputErrorPolicy": "string",
    "EventsLateArrivalMaxDelayInSeconds": "integer",
    "EventsOutOfOrderMaxDelayInSeconds": "integer",
    "EventsOutOfOrderPolicy": "string",
    "StreamingUnits": "integer",
    "CompatibilityLevel": "string",
    "UseSystemAssignedIdentity": "boolean",
    "GlobalStorage": {
        "AccountName": "string",
        "AccountKey": "string",
    },
    "DataSourceCredentialDomain": "string",
    "ScriptType": "string",
    "Tags": {}
}
```

|Naam|Type|Vereist|Waarde|
|----|----|--------|-----|
|DataLocale|tekenreeks|Nee|De gegevens land instelling van de stream Analytics-taak. De waarde moet de naam van een ondersteund zijn. Wordt standaard ingesteld op ' nl-nl ' als er geen is opgegeven.|
|OutputErrorPolicy|tekenreeks|Nee|Hiermee wordt het beleid aangegeven dat moet worden toegepast op gebeurtenissen die bij de uitvoer arriveren en niet naar de externe opslag kunnen worden geschreven vanwege een onjuiste indeling (ontbrekende kolom waarden, kolom waarden van het verkeerde type of de grootte). -Stoppen of neerzetten|
|EventsLateArrivalMaxDelayInSeconds|geheel getal|Nee|De Maxi maal toegestane vertraging in seconden voor gebeurtenissen die te laat arriveren. Het ondersteunde bereik is-1 tot 1814399 (20.23:59:59 dagen) en-1 wordt gebruikt om een wacht tijd voor onbepaalde tijd op te geven. Als de eigenschap ontbreekt, wordt geïnterpreteerd als een waarde van-1.|
|EventsOutOfOrderMaxDelayInSeconds|geheel getal|Nee|De Maxi maal toegestane vertraging in seconden waarbij out-of-order-gebeurtenissen kunnen worden aangepast zodat ze weer in de juiste volg orde staan.|
|EventsOutOfOrderPolicy|tekenreeks|Nee|Hiermee wordt het beleid aangegeven dat moet worden toegepast op gebeurtenissen die niet in de juiste volg orde in de invoer gebeurtenis stroom arriveren. -Aanpassen of verwijderen|
|StreamingUnits|geheel getal|Ja|Hiermee geeft u het aantal streaming-eenheden op dat door de streaming-taak wordt gebruikt.|
|Compatibiliteits niveau|tekenreeks|Nee|Hiermee wordt bepaald runtime-gedrag van de streaming-taak bepaald. -Acceptabele waarden zijn ' 1,0 ', ' 1,1 ', ' 1,2 '|
|UseSystemAssignedIdentity|booleaans|Nee|Stel waar in om deze taak in te scha kelen voor communicatie met andere Azure-Services, met behulp van een beheerde Azure Active Directory identiteit.|
|GlobalStorage. AccountName|tekenreeks|Nee|Globaal opslag account wordt gebruikt voor het opslaan van inhoud die betrekking heeft op uw stream Analytics-taak, zoals moment opnamen van SQL-referentie gegevens.|
|GlobalStorage. AccountKey|tekenreeks|Nee|Bijbehorende sleutel voor globaal opslag account.|
|DataSourceCredentialDomain|tekenreeks|Nee|Gereserveerde eigenschap voor lokale opslag van referenties.|
|ScriptType|tekenreeks|Ja|Gereserveerde eigenschap waarmee het type van dit bron bestand wordt aangegeven. De acceptabele waarde is JobConfig voor JobConfig. json.|
|Tags|JSON-sleutel-waardeparen|Nee|Tags zijn naam/waarde-paren waarmee u resources kunt categoriseren en een geconsolideerde factuur kunt weer geven door hetzelfde label op meerdere resources en resource groepen toe te passen. Namen van tags zijn hoofdletter gevoelig en label waarden zijn hoofdletter gevoelig.|

## <a name="next-steps"></a>Volgende stappen

* [Een Azure Stream Analytics-taak maken in Visual Studio code](quick-create-vs-code.md)
* [Stream Analytics query's lokaal met voorbeeld gegevens testen met Visual Studio code](visual-studio-code-local-run.md)
* [Stream Analytics query's lokaal testen op invoer van live streams met behulp van Visual Studio code](visual-studio-code-local-run-live-input.md)
*[implementeren van een Azure stream Analytics taak met CI/cd NPM-pakket](setup-cicd-vs-code.md)