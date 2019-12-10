---
title: Ondersteunde talen in Azure Functions
description: Meer informatie over welke talen worden ondersteund (GA) en wat experimentele of in Preview zijn.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 029ea753439dca3093bf214a5adfb6d58a1fe567
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74942253"
---
# <a name="supported-languages-in-azure-functions"></a>Ondersteunde talen in Azure Functions

Dit artikel bevat informatie over de ondersteunings niveaus voor talen die u kunt gebruiken met Azure Functions.

## <a name="levels-of-support"></a>Ondersteunings niveaus

Er zijn drie ondersteunings niveaus:

* **Algemeen beschikbaar (ga)** : volledig ondersteund en goedgekeurd voor productie gebruik.
* **Preview** -wordt nog niet ondersteund, maar wordt naar verwachting in de toekomst bereikt.
* **Experimenteel** -niet ondersteund en kan in de toekomst worden afgebroken; geen garantie voor de uiteindelijke preview-of GA-status.

## <a name="languages-by-runtime-version"></a>Talen per runtime versie 

Er zijn [drie versies van de Azure functions runtime](functions-versions.md) beschikbaar. In de volgende tabel ziet u welke talen worden ondersteund in elke runtime versie.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Experimentele talen

De experimentele talen in versie 1. x worden niet goed geschaald en bieden geen ondersteuning voor alle bindingen.

Gebruik geen experimentele functies voor alles wat u vertrouwt, aangezien er geen officiële ondersteuning voor hen is. Ondersteunings cases mogen niet worden geopend voor problemen met experimentele talen. 

Latere runtime versies bieden geen ondersteuning voor experimentele talen. Ondersteuning voor nieuwe talen wordt alleen toegevoegd wanneer de taal in productie kan worden ondersteund. 

### <a name="language-extensibility"></a>Taal uitbreid baarheid

Met ingang van versie 2. x is de runtime ontworpen om [taal uitbreid baarheid](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility)aan te bieden. De Java script-en Java-talen in de 2. x-runtime zijn gebouwd met deze uitbrei ding.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende bronnen voor meer informatie over het ontwikkelen van functies in de ondersteunde talen:

+ [C#Naslag informatie over Class Library-ontwikkel aars](functions-dotnet-class-library.md)
+ [C#Naslag informatie over script ontwikkelaars](functions-reference-csharp.md)
+ [Naslag informatie voor Java-Ontwikkel aars](functions-reference-java.md)
+ [Naslag informatie over Java script-ontwikkel aars](functions-reference-node.md)
+ [Naslag informatie over Power shell-ontwikkel aars](functions-reference-powershell.md)
+ [Naslag informatie voor python-ontwikkel aars](functions-reference-python.md)
+ [Naslag informatie voor type script-ontwikkel aars](functions-reference-node.md#typescript)
