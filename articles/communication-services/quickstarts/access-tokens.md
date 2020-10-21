---
title: Snelstart - Toegangstoken maken en beheren
titleSuffix: An Azure Communication Services quickstart
description: Meer informatie over het beheren van identiteits- en toegangstokens met behulp van de Azure Communication Services Administration-clientbibliotheek.
author: tomaschladek
manager: jken
services: azure-communication-services
ms.author: tchladek
ms.date: 08/20/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: e323f1f50fe6c67a841c300fcbec1eed3afc4497
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92074121"
---
# <a name="quickstart-create-and-manage-access-tokens"></a>Quickstart: Toegangstokens maken en beheren

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Ga aan de slag met Azure Communication Services door de clientbibliotheek voor Communication Services Administration te gebruiken om uw toegangstokens te provisioneren en te beheren. Met toegangstokens kunt u uw chat- en aanroep-clientbibliotheken rechtstreeks verifiëren tegen Azure Communication Services. Deze tokens worden gegenereerd aan de kant van de server door een tokenleveringsservice die u implementeert. Ze worden vervolgens gebruikt voor het initialiseren van de Communication Services-clientbibliotheken op clientapparaten.

Houd er rekening mee dat alle prijzen die in de afbeeldingen in deze handleiding worden getoond, alleen als bijvoorbeeld bedoeld zijn.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/user-access-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/user-access-token-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/user-access-token-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/user-access-token-java.md)]
::: zone-end

De uitvoer van de app beschrijft elke actie die is voltooid:
<!---cSpell:disable --->
```console
Azure Communication Services - Access Tokens Quickstart

Issued a access token with 'voip' scope for identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050:
<token signature here>

Issued a access token with 'chat' scope for identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050:
<token signature here>

Successfully deleted the identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050

Deleted the identity with ID: 8:acs:fecfaddf-bf2c-4a0e-b52f-7d918c9536e6_65012b-1400da9050
```
<!---cSpell:enable --->

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Communication Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd. Meer informatie over het [opschonen van resources](./create-communication-resource.md#clean-up-resources).


## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u de volgende zaken geleerd:

> [!div class="checklist"]
> * Identiteiten beheren
> * Toegangstokens uitgeven
> * De clientbibliotheek voor Communication Services-beheer gebruiken


> [!div class="nextstepaction"]
> [Spraakoproep aan uw app toevoegen](./voice-video-calling/getting-started-with-calling.md)

U kunt ook het volgende doen:

 - [Meer informatie over verificatie](../concepts/authentication.md)
 - [Chat aan uw app toevoegen](./chat/get-started.md)
 - [Meer informatie over de client -en serverarchitectuur](../concepts/client-and-server-architecture.md)
