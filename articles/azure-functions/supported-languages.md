---
title: Ondersteunde talen in Azure-functies
description: Leer welke talen worden ondersteund (GA) en welke experimenteel of in preview zijn.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 029ea753439dca3093bf214a5adfb6d58a1fe567
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74942253"
---
# <a name="supported-languages-in-azure-functions"></a>Ondersteunde talen in Azure-functies

In dit artikel wordt uitgelegd welke ondersteuningsniveaus worden aangeboden voor talen die u gebruiken met Azure-functies.

## <a name="levels-of-support"></a>Steunniveaus

Er zijn drie niveaus van ondersteuning:

* **Algemeen beschikbaar (GA)** - Volledig ondersteund en goedgekeurd voor productiegebruik.
* **Preview** - Nog niet ondersteund, maar zal naar verwachting ga-status te bereiken in de toekomst.
* **Experimenteel** - Niet ondersteund en zou kunnen worden verlaten in de toekomst; geen garantie voor een eventuele preview of GA-status.

## <a name="languages-by-runtime-version"></a>Talen per runtime-versie 

Er zijn [drie versies van de runtime van Azure-functies](functions-versions.md) beschikbaar. In de volgende tabel ziet u welke talen worden ondersteund in elke runtime-versie.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Experimentele talen

De experimentele talen in versie 1.x schalen niet goed en ondersteunen niet alle bindingen.

Gebruik geen experimentele functies voor iets waarop u vertrouwt, omdat er geen officiële ondersteuning voor hen is. Ondersteuningsaanvragen mogen niet worden geopend voor problemen met experimentele talen. 

Latere runtime-versies bieden geen ondersteuning voor experimentele talen. Ondersteuning voor nieuwe talen wordt alleen toegevoegd wanneer de taal in de productie kan worden ondersteund. 

### <a name="language-extensibility"></a>Taaluitbreidbaarheid

Vanaf versie 2.x is de runtime ontworpen om [taaluitbreidbaarheid](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility)te bieden. De JavaScript- en Java-talen in de 2.x runtime zijn gebouwd met deze uitbreidbaarheid.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie over het ontwikkelen van functies in de ondersteunde talen:

+ [Referentie voor c# klasse bibliotheekontwikkelaars](functions-dotnet-class-library.md)
+ [C# scriptontwikkelaarverwijzing](functions-reference-csharp.md)
+ [Java-ontwikkelaarsreferentie](functions-reference-java.md)
+ [JavaScript-ontwikkelaarsreferentie](functions-reference-node.md)
+ [PowerShell-ontwikkelaarreferentie](functions-reference-powershell.md)
+ [Verwijzing naar Python-ontwikkelaars](functions-reference-python.md)
+ [Verwijzing naar TypeScript-ontwikkelaars](functions-reference-node.md#typescript)
