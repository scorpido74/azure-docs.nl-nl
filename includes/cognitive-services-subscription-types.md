---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 3b808f4eb853cf05eb08cd1acf08dedccabf71a6
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274664"
---
## <a name="azure-cognitive-service-resource-types"></a>Resource typen voor de Azure cognitieve service

<!-- > [!NOTE]
> Subscription owners can disable the creation of Cognitive Services resources for resource groups and subscriptions by applying [Azure policy](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), assigning a “Not allowed resource types” policy definition, and specifying **Microsoft.CognitiveServices/accounts** as the target resource type. -->
U hebt toegang tot Azure Cognitive Services via twee verschillende bronnen: Een resource met meerdere services of één service. Met deze abonnementen kunt u verbinding maken met een enkele cognitieve service of meerdere cognitieve services tegelijk.

### <a name="multi-service-resource"></a>Resource met meerdere services

Abonneren op een resource met meerdere services Cognitive Services:
* Met kunt u één Azure-Resource gebruiken voor de meeste Azure-Cognitive Services.
* U kunt één sleutel verkrijgen die kan worden gebruikt met meerdere Azure-Cognitive Services.
* Consolideert facturering van de services die u gebruikt. Zie [Cognitive Services prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/) voor aanvullende informatie.

>[!WARNING]
> Op dit moment bieden deze services **geen** ondersteuning voor meerdere service sleutels: QnA Maker, spraak Services, Custom Vision en anomalie detectie.

### <a name="single-service-resource"></a>Resource met één service

Abonneren op een resource met één service Cognitive Services:
* Hiermee kunt u een opgegeven cognitieve service gebruiken om de resource te maken voor (zoals Computer Vision of spraak Services).
* U verkrijgt een sleutel die specifiek is voor de cognitieve service waarvoor u een resource maakt.