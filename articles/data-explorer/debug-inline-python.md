---
title: Debug Kusto-query taal inline python met VS code-Azure Data Explorer
description: Meer informatie over het opsporen van fouten in Kusto query language (KQL) in line python met behulp van VS code.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: 96bd66f96b04bd7032d976ba9ebbbeb60c8415e7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444472"
---
# <a name="debug-kusto-query-language-inline-python-using-vs-code"></a>Fout opsporing voor Kusto-query taal in line python met VS code

Azure Data Explorer ondersteunt het uitvoeren van python-code die is inge sloten in Kusto-query taal met behulp van de [python ()-invoeg toepassing](/azure/kusto/query/pythonplugin) De runtime van de invoeg toepassing wordt gehost in een sandbox, een geïsoleerde en veilige python-omgeving. De invoeg toepassing python () breidt de systeem eigen functionele Kusto-query taal uit met het enorme archief van OSS Python-pakketten. Met deze extensie kunt u geavanceerde algoritmen, zoals machine learning, kunst matige intelligentie, statistische en tijd reeksen, als onderdeel van de query uitvoeren.

Kusto query language-hulpprogram ma's zijn niet geschikt voor het ontwikkelen en opsporen van problemen met python-algoritmen. Ontwikkel daarom het algoritme voor uw favoriete python-Integrated Development Environment, zoals Jupyter, PyCharm, VS of VS code. Wanneer het algoritme is voltooid, kopieert en plakt u deze in KQL. Om deze werk stroom te verbeteren en te stroom lijnen, ondersteunt Azure Data Explorer de integratie tussen de Kusto Explorer-of Web UI-clients en de VS code voor het ontwerpen en debuggen van KQL inline python-code. 

> [!NOTE]
> Deze werk stroom kan alleen worden gebruikt voor het opsporen van fouten in relatief kleine invoer tabellen (Maxi maal enkele MB). Daarom moet u mogelijk de invoer voor fout opsporing beperken.  Als u een grote tabel moet verwerken, kunt u deze beperken voor fout opsporing met behulp van `| take`, `| sample`of `where rand() < 0.x`.

## <a name="prerequisites"></a>Vereisten

1. Installeer python [Anaconda-distributie](https://www.anaconda.com/distribution/#download-section). Selecteer in **Geavanceerde opties** **de optie Anaconda toevoegen aan de omgevings variabele PATH**.
2. [Visual Studio Code](https://code.visualstudio.com/Download) installeren
3. [Python-extensie voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-python.python)installeren.

## <a name="run-your-query-in-your-client-application"></a>Uw query uitvoeren in uw client toepassing

1. Open in uw client toepassing een voor voegsel van een query met inline python met `set query_python_debug;`
1. Voer de query uit.
    * Kusto Explorer: VS code wordt automatisch gestart met het script *debug_python. py* .
    * Kusto-webgebruikersinterface: 
        1. Down load en sla *debug_python. py*, *DF. txt*en *kargs. txt*op. Selecteer **toestaan**in het venster. Bestanden in de geselecteerde map **Opslaan** . 

            ![De Web-UI downloadt de inline python-bestanden](media/debug-inline-python/webui-inline-python.png)

        1. Klik met de rechter muisknop op *debug_python. py* en open met VS code. 
        Het *debug_python. py* -script bevat de inline python-code, uit de KQL-query, voorafgegaan door de sjabloon code voor het initialiseren van de invoer data frame van *DF. txt* en de woorden lijst met para meters van *kargs. txt*.    
            
1. Open in VS code het fout opsporingsprogramma VS code: **Debug** > **Start Debugging (F5)** , selecteer **python** -configuratie. Het fout opsporingsprogramma wordt gestart en automatisch onderbrekings punt voor het opsporen van fouten in de inline code.

### <a name="how-does-inline-python-debugging-in-vs-code-work"></a>Hoe werkt de inline python-fout opsporing in VS code?

1. De query wordt geparseerd en uitgevoerd op de server totdat de vereiste `| evaluate python()`-component is bereikt.
1. De python-sandbox wordt aangeroepen, maar in plaats van de code uit te voeren, worden de invoer tabel, de woorden lijst van de para meters en de code gedecodeerd en teruggezonden naar de client.
1. Deze drie objecten worden opgeslagen in drie bestanden: *DF. txt*, *kargs. txt*en *debug_python. py* in de geselecteerde directory (webgebruikersinterface) of in de map client% Temp% (Kusto Explorer).
1. VS code wordt gestart en vooraf geladen met het bestand *debug_python. py* dat een voorvoegsel code bevat voor het initialiseren van VG en kargs van hun respectieve bestanden, gevolgd door het python-script dat is inge sloten in de KQL-query.

## <a name="query-example"></a>Query voorbeeld

1. Voer de volgende KQL-query uit in uw client toepassing:

    ```kusto
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

    Bekijk de resulterende tabel:

    | x  | kabel  |
    |---------|---------|
    | 1     |   1      |
    | 2     |   16      |
    | 3     |   81      |
    | 4     |    256     |
    
1. Voer dezelfde KQL-query uit in uw client toepassing met behulp van `set query_python_debug;`:

    ```kusto
    set query_python_debug;
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

1. VS-code wordt gestart:

    ![Start VS code](media/debug-inline-python/launch-vs-code.png)

1. VS code wordt uit-data frame en drukt ' resultaat ' af in de console voor fout opsporing:

    ![VS-code fout opsporing](media/debug-inline-python/debug-vs-code.png)

> [!NOTE]
> Er zijn mogelijk verschillen tussen de installatie kopie van de python-sandbox en de lokale installatie. [Controleer de sandbox-installatie kopie voor specifieke pakketten door de invoeg toepassing op te vragen](https://github.com/Azure/azure-kusto-analytics-lib/blob/master/Utils/functions/get_modules_version.csl).
