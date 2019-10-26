---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: 14740e780f6143f19f843891c16b25df761d0b4f
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931256"
---
Wanneer u toegang hebt tot het gebruik van de formulier herkenning, ontvangt u een welkomst-e-mail met verschillende koppelingen en bronnen. Gebruik de koppeling ' Azure Portal ' in dat bericht om de Azure Portal te openen en een resource voor een formulier herkenning te maken. Geef in het deel venster **maken** de volgende informatie op:

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