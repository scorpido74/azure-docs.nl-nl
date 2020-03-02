---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: 33624ab800bd1155b52efbc05f317122a99bb479
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/01/2020
ms.locfileid: "78205815"
---
Ga naar de Azure Portal en <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="een nieuwe resource voor de herkenner te maken" target="_blank">Maak een nieuwe resource <span class="docon docon-navigate-external x-hidden-focus"></span> voor de herkenner van formulieren</a>. Geef in het deel venster **maken** de volgende informatie op:

|    |    |
|--|--|
| **Naam** | Een beschrijvende naam voor uw resource. U kunt het beste een beschrijvende naam gebruiken, bijvoorbeeld *MyNameFormRecognizer*. |
| **Abonnement** | Selecteer het Azure-abonnement waaraan toegang is verleend. |
| **Locatie** | De locatie van uw cognitieve service-exemplaar. Verschillende locaties kunnen latentie introduceren, maar hebben geen invloed op de runtime-Beschik baarheid van uw resource. |
| **Prijscategorie** | De kosten van uw resource zijn afhankelijk van de prijs categorie die u kiest en uw gebruik. Zie de [prijs informatie](https://azure.microsoft.com/pricing/details/cognitive-services/)voor de API voor meer informatie.
| **Resourcegroep** | De [Azure-resource groep](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) die uw resource bevat. U kunt een nieuwe groep maken of toevoegen aan een bestaande groep. |

> [!IMPORTANT]
> Normaal gesp roken wanneer u een cognitieve service resource in de Azure Portal maakt, hebt u de mogelijkheid om een sleutel voor meerdere service abonnementen te maken (die wordt gebruikt voor meerdere cognitieve Services) of een abonnements sleutel van één service (die alleen wordt gebruikt met een specifieke cognitieve service). Omdat de formulier Recognizer echter een preview-versie is, is deze niet opgenomen in het abonnement voor meerdere services en kunt u het abonnement met één service niet maken, tenzij u de koppeling in het welkomst bericht gebruikt.

Wanneer de resource voor de herkenner van het formulier is geïmplementeerd, zoekt en selecteert u deze in de lijst **alle resources** in de portal. Selecteer vervolgens het tabblad **Quick Start** om uw abonnements gegevens weer te geven. Sla de waarden van **key1** en **endpoint** op een tijdelijke locatie op. U gebruikt deze in de volgende stappen.
