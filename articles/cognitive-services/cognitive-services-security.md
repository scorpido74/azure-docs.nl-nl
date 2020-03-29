---
title: Beveiliging
titleSuffix: Azure Cognitive Services
description: Meer informatie over de verschillende beveiligingsoverwegingen voor cognitief services-gebruik.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: c86d806c408c2e8226e632a0b15e1e8729c987f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131538"
---
# <a name="azure-cognitive-services-security"></a>Azure Cognitive Services-beveiliging

Beveiliging moet worden beschouwd als een topprioriteit bij het ontwikkelen van alle toepassingen. Met het ontstaan van kunstmatige intelligentie toepassingen is beveiliging nog belangrijker. In dit artikel worden verschillende aspecten van Azure Cognitive Services-beveiliging beschreven, zoals het gebruik van transportlaagbeveiliging, verificatie en het veilig configureren van gevoelige gegevens.

## <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

Alle eindpunten van Cognitive Services die via HTTP worden blootgesteld, dwingen TLS 1.2 af. Met een afgedwongen beveiligingsprotocol moeten consumenten die een eindpunt voor Cognitive Services proberen aan te roepen, zich aan deze richtlijnen houden:

* Het besturingssysteem (client besturingssysteem) moet TLS 1.2 ondersteunen
* De taal (en het platform) die worden gebruikt om de HTTP-oproep te maken, moet TLS 1.2 opgeven als onderdeel van het verzoek
  * Afhankelijk van de taal en het platform gebeurt het impliciet of expliciet specificeren van TLS

Voor .NET-gebruikers u de <a href="https://docs.microsoft.com/dotnet/framework/network-programming/tls" target="_blank">best practices <span class="docon docon-navigate-external x-hidden-focus"> </span>voor transportlaagbeveiliging </a>overwegen.

## <a name="authentication"></a>Authentication

Bij het bespreken van authenticatie zijn er verschillende veelvoorkomende misvattingen. Authenticatie en autorisatie zijn vaak verward voor elkaar. Identiteit is ook een belangrijk onderdeel van veiligheid. Een identiteit is een verzameling van informatie over een <a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank">opdrachtgever. <span class="docon docon-navigate-external x-hidden-focus"> </span> </a> Identiteitsproviders (IdP) geven identiteiten aan verificatieservices. Verificatie is de handeling van het verifiëren van de identiteit van een gebruiker. Autorisatie is de specificatie van toegangsrechten en bevoegdheden voor resources voor een bepaalde identiteit. Verschillende van de Cognitive Services-aanbiedingen omvatten role-based access control (RBAC). RBAC kan worden gebruikt om een deel van de ceremonie die betrokken zijn bij het handmatig beheren van opdrachtgevers te vereenvoudigen. Zie voor meer informatie [op basis van rollentoegangscontrole voor Azure-resources](../role-based-access-control/overview.md).

Zie <a href="https://docs.microsoft.com/azure/cognitive-services/authentication" target="_blank">Verificatieverzoeken<span class="docon docon-navigate-external x-hidden-focus"></span></a>voor Azure Cognitive Services voor meer informatie over verificatie met abonnementssleutels, toegangstokens en Azure Active Directory (AAD).

## <a name="environment-variables-and-application-configuration"></a>Omgevingsvariabelen en toepassingsconfiguratie

Omgevingsvariabelen zijn naamwaardeparen die zijn opgeslagen binnen een specifieke omgeving. Een veiliger alternatief voor het gebruik van hardcoded waarden voor gevoelige gegevens, is het gebruik van omgevingsvariabelen. Hardcoded waarden zijn onzeker en moeten worden vermeden.

> [!CAUTION]
> Gebruik **geen** hardcoded waarden voor gevoelige gegevens, dit is een groot beveiligingslek.

> [!NOTE]
> Terwijl omgevingsvariabelen in platte tekst worden opgeslagen, worden ze geïsoleerd naar een omgeving. Als een omgeving in het gedrang komt, zijn dat ook de variabelen met de omgeving.

### <a name="set-environment-variable"></a>Omgevingsvariabele instellen

Als u omgevingsvariabelen wilt instellen, gebruikt u `ENVIRONMENT_VARIABLE_KEY` een van `value` de volgende opdrachten - waarbij de benoemde sleutel is en de waarde is die is opgeslagen in de omgevingsvariabele.

# <a name="command-line"></a>[Opdrachtregel](#tab/command-line)

Een persistente omgevingsvariabele maken en toewijzen, gezien de waarde.

```CMD
:: Assigns the env var to the value
setx ENVIRONMENT_VARIABLE_KEY="value"
```

Lees in een nieuw exemplaar van de **opdrachtprompt**de omgevingsvariabele.

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

Een persistente omgevingsvariabele maken en toewijzen, gezien de waarde.

```powershell
# Assigns the env var to the value
[System.Environment]::SetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY', 'value', 'User')
```

Lees in een nieuw exemplaar van de **Windows PowerShell**de omgevingsvariabele.

```powershell
# Prints the env var value
[System.Environment]::GetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY')
```

# <a name="bash"></a>[Bash](#tab/bash)

Een persistente omgevingsvariabele maken en toewijzen, gezien de waarde.

```Bash
# Assigns the env var to the value
echo export ENVIRONMENT_VARIABLE_KEY="value" >> /etc/environment && source /etc/environment
```

In een nieuw exemplaar van de **Bash,** lees de omgevingsvariabele.

```Bash
# Prints the env var value
echo "${ENVIRONMENT_VARIABLE_KEY}"

# Or use printenv:
# printenv ENVIRONMENT_VARIABLE_KEY
```

---

> [!TIP]
> Nadat u een omgevingsvariabele hebt ingesteld, start u uw geïntegreerde ontwikkelomgeving (IDE) opnieuw op om ervoor te zorgen dat nieuw toegevoegde omgevingsvariabelen beschikbaar zijn.

### <a name="get-environment-variable"></a>Omgevingsvariabele krijgen

Om een omgevingsvariabele te krijgen, moet deze worden gelezen in het geheugen. Neem, afhankelijk van de taal die u gebruikt, de volgende codefragmenten in overweging. Deze codefragmenten laten zien hoe u `ENVIRONMENT_VARIABLE_KEY` omgevingsvariabele `value`krijgen, gezien de variabele en deze toewijzen aan een variabele met de naam.

# <a name="c"></a>[C #](#tab/csharp)

Zie voor meer <a href="https://docs.microsoft.com/dotnet/api/system.environment.getenvironmentvariable" target="_blank"> `Environment.GetEnvironmentVariable` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informatie .

```csharp
using static System.Environment;

class Program
{
    static void Main()
    {
        // Get the named env var, and assign it to the value variable
        var value =
            GetEnvironmentVariable(
                "ENVIRONMENT_VARIABLE_KEY");
    }
}
```

# <a name="c"></a>[C++](#tab/cpp)

Zie voor meer <a href="https://docs.microsoft.com/cpp/c-runtime-library/reference/getenv-wgetenv" target="_blank"> `getenv` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informatie .

```cpp
#include <stdlib.h>

int main()
{
    // Get the named env var, and assign it to the value variable
    auto value =
        getenv("ENVIRONMENT_VARIABLE_KEY");
}
```

# <a name="java"></a>[Java](#tab/java)

Zie voor meer <a href="https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getenv(java.lang.String)" target="_blank"> `System.getenv` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informatie .

```java
import java.lang.*;

public class Program {
   public static void main(String[] args) throws Exception {
    // Get the named env var, and assign it to the value variable
    String value =
        System.getenv(
            "ENVIRONMENT_VARIABLE_KEY")
   }
}
```

# <a name="nodejs"></a>[Node.js](#tab/node-js)

Zie voor meer <a href="https://nodejs.org/api/process.html#process_process_env" target="_blank"> `process.env` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informatie .

```javascript
// Get the named env var, and assign it to the value variable
const value =
    process.env.ENVIRONMENT_VARIABLE_KEY;
```

# <a name="python"></a>[Python](#tab/python)

Zie voor meer <a href="https://docs.python.org/2/library/os.html#os.environ" target="_blank"> `os.environ` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informatie .

```python
import os

# Get the named env var, and assign it to the value variable
value = os.environ['ENVIRONMENT_VARIABLE_KEY']
```

# <a name="objective-c"></a>[Doelstelling-C](#tab/objective-c)

Zie voor meer <a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank"> `environment` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>informatie .

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="next-steps"></a>Volgende stappen

* Ontdek de verschillende [Cognitive Services](welcome.md)
* Meer informatie over [virtual networks van Cognitive Services](cognitive-services-virtual-networks.md)
