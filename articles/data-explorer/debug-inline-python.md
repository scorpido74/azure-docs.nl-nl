---
title: Foutopsporingskusto-querytaal inline Python met VS-code - Azure Data Explorer
description: Meer informatie over het opsporen van Kusto-querytaal (KQL) inline Python met VS-code.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: 96bd66f96b04bd7032d976ba9ebbbeb60c8415e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444472"
---
# <a name="debug-kusto-query-language-inline-python-using-vs-code"></a>Foutopsporingskusto-querytaal inline Python met VS-code

Azure Data Explorer ondersteunt het uitvoeren van Python-code die is ingesloten in kusto-querytaal met behulp van de [python()-plug-in](/azure/kusto/query/pythonplugin). De plug-in runtime wordt gehost in een sandbox, een geïsoleerde en veilige Python-omgeving. De python() plugin mogelijkheid breidt Kusto query taal native functionaliteiten met het enorme archief van OSS Python pakketten. Met deze extensie u geavanceerde algoritmen uitvoeren, zoals machine learning, kunstmatige intelligentie, statistische en tijdreeksen als onderdeel van de query.

Kusto query taaltools zijn niet handig voor het ontwikkelen en debuggen van Python-algoritmen. Ontwikkel daarom het algoritme op uw favoriete python-geïntegreerde ontwikkelomgeving, zoals Jupyter, PyCharm, VS of VS Code. Wanneer het algoritme is voltooid, kopieer en plak je in KQL. Om deze workflow te verbeteren en te stroomlijnen, ondersteunt Azure Data Explorer integratie tussen Kusto Explorer- of Web UI-clients en VS-code voor het ontwerpen en debuggen van KQL-inline Python-code. 

> [!NOTE]
> Deze werkstroom kan alleen worden gebruikt om relatief kleine invoertabellen (tot weinig MB) te debuggen. Daarom moet u mogelijk de invoer voor foutopsporing beperken.  Als u een grote tabel moet verwerken, beperkt `| take` `| sample`u `where rand() < 0.x`deze voor foutopsporing met , of .

## <a name="prerequisites"></a>Vereisten

1. Installeer Python [Anaconda Distribution](https://www.anaconda.com/distribution/#download-section). Selecteer **in Geavanceerde opties**De optie **Anaconda toevoegen aan mijn PATH-omgevingsvariabele**.
2. [Visual Studio-code installeren](https://code.visualstudio.com/Download)
3. [Python-extensie voor Visual Studio Code installeren](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

## <a name="run-your-query-in-your-client-application"></a>Uw query uitvoeren in uw clienttoepassing

1. Maak in uw clienttoepassing een query met inline Python met`set query_python_debug;`
1. Voer de query uit.
    * Kusto Explorer: VS Code wordt automatisch gestart met het *debug_python.py* script.
    * Kusto Web UI: 
        1. Download en sla *debug_python.py,* *df.txt*en *kargs.txt*. Selecteer **Toestaan**in het venster . **Bestanden opslaan** in geselecteerde map. 

            ![Web UI downloadt de inline python-bestanden](media/debug-inline-python/webui-inline-python.png)

        1. Klik met de rechtermuisknop op *debug_python.py* en open met VS-code. 
        Het *debug_python.py-script* bevat de inline Python-code, van de KQL-query, vooraf vastgelegd door de sjablooncode om het invoergegevensframe van *df.txt* en het woordenboek van parameters van *kargs.txt*te initialiseren.    
            
1. Start in VS-code de VS-codefoutfoutfoutopsporing: **Foutopsporing** > **debuggen (F5)** ontluisteren , selecteer **Python-configuratie.** De foutopsporing wordt gestart en wordt automatisch gekort om de inline-code te debuggen.

### <a name="how-does-inline-python-debugging-in-vs-code-work"></a>Hoe werkt inline Python-foutopsporing in VS-code?

1. De query wordt ontleed en uitgevoerd in `| evaluate python()` de server totdat de vereiste clausule is bereikt.
1. De Python-sandbox wordt aangeroepen, maar in plaats van de code uit te voeren, wordt de invoertabel, het woordenboek met parameters en de code geserialiseerd en teruggestuurd naar de client.
1. Deze drie objecten worden opgeslagen in drie bestanden: *df.txt*, *kargs.txt*en *debug_python.py* in de geselecteerde directory (Web UI) of in de map %TEMP% van de client (Kusto Explorer).
1. VS-code wordt gestart, vooraf geladen met het *debug_python.py-bestand* dat een voorvoegselcode bevat om df en kargs uit hun respectievelijke bestanden te initialiseren, gevolgd door het Python-script dat is ingebed in de KQL-query.

## <a name="query-example"></a>Voorbeeld van query

1. Voer de volgende KQL-query uit in uw clienttoepassing:

    ```kusto
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

    Zie de resulterende tabel:

    | x  | x4  |
    |---------|---------|
    | 1     |   1      |
    | 2     |   16      |
    | 3     |   81      |
    | 4     |    256     |
    
1. Voer dezelfde KQL-query uit `set query_python_debug;`in uw clienttoepassing met:

    ```kusto
    set query_python_debug;
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

1. VS-code wordt gelanceerd:

    ![VS-code starten](media/debug-inline-python/launch-vs-code.png)

1. VS Code debugs en drukt 'resultaat' dataframe in de debug console:

    ![VS-codefoutfoutopsporing](media/debug-inline-python/debug-vs-code.png)

> [!NOTE]
> Er kunnen verschillen zijn tussen de Python sandbox-afbeelding en uw lokale installatie. [Controleer de sandbox-afbeelding voor specifieke pakketten door de plug-in op te vragen.](https://github.com/Azure/azure-kusto-analytics-lib/blob/master/Utils/functions/get_modules_version.csl)
