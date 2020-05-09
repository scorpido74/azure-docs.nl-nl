---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 3929ffa81a8b558d8cb0dbd74135acc9cf1ea47b
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82876046"
---
## <a name="configure-your-local-environment"></a>Uw lokale omgeving configureren

Voordat u begint, moet u het volgende hebben:

+ Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell"  
+ De [Azure functions core tools](../articles/azure-functions/functions-run-local.md#v2) versie 2.7.1846 of een latere versie van 2. x.
::: zone-end  
::: zone pivot="programming-language-python"
+ Voor python 3,6 en 3,7 is [Azure functions core tools](../articles/azure-functions/functions-run-local.md#v2) versie 2.7.1846 of een latere versie van 2. x vereist. Voor python 3,8 is [versie 3. x](../articles/azure-functions/functions-run-local.md#v2) van de kern hulpprogramma's vereist.
::: zone-end

+ De [Azure cli](/cli/azure/install-azure-cli) -versie 2,4 of hoger. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node. js](https://nodejs.org/), Active LTS en onderhoud LTS-versies (8.11.1 en 10.14.1 aanbevolen).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3,8 (64-bits)](https://www.python.org/downloads/release/python-382/), [python 3,7 (64-bits)](https://www.python.org/downloads/release/python-375/), [python-3,6 (64-bit)](https://www.python.org/downloads/release/python-368/), die worden ondersteund door Azure functions. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ De [.net core SDK 2.2 +](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ De [Java Developer Kit](https://aka.ms/azure-jdks), versie 8.

+ [Apache Maven](https://maven.apache.org), versie 3,0 of hoger.

> [!IMPORTANT]
> De omgevingsvariabele JAVA_HOME moet zijn ingesteld op de installatielocatie van de JDK om deze quickstart te kunnen voltooien.
::: zone-end
