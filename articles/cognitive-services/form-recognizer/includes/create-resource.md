---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: 33624ab800bd1155b52efbc05f317122a99bb479
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205815"
---
Ga naar de <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="Azure-portal en Maak" target="_blank">een nieuwe bron <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>voor formulierherkenning en maak een nieuwe bron voor formulierherkenning . Geef in het deelvenster **Maken** de volgende gegevens op:

|    |    |
|--|--|
| **Naam** | Een beschrijvende naam voor uw resource. We raden u aan een beschrijvende naam te gebruiken, bijvoorbeeld *MyNameFormRecognizer*. |
| **Abonnement** | Selecteer het Azure-abonnement waarvoor toegang is verleend. |
| **Locatie** | De locatie van uw cognitieve service-instantie. Verschillende locaties kunnen latentie introduceren, maar hebben geen invloed op de beschikbaarheid van runtime van uw resource. |
| **Prijslaag** | De kosten van uw resource zijn afhankelijk van de prijscategorie die u kiest en uw gebruik. Zie de [API-prijsdetails](https://azure.microsoft.com/pricing/details/cognitive-services/)voor meer informatie .
| **Resourcegroep** | De [Azure-brongroep](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) die uw bron bevat. U een nieuwe groep maken of toevoegen aan een reeds bestaande groep. |

> [!IMPORTANT]
> Normaal gesproken hebt u bij het maken van een Cognitive Service-bron in de Azure-portal de optie om een multiservice-abonnementssleutel (die wordt gebruikt voor meerdere cognitieve services) of een abonnementssleutel voor één service (alleen gebruikt met een specifieke cognitieve service) te maken. Omdat Form Recognizer echter een preview-release is, is deze niet opgenomen in het abonnement voor meerdere diensten en u het abonnement met één service niet maken, tenzij u de koppeling in de welkomste-mail gebruikt.

Wanneer de bron Van Formulierherkenning klaar is met het implementeren, zoekt en selecteert u deze in de lijst **Alle bronnen** in de portal. Selecteer vervolgens het tabblad **Snel starten** om uw abonnementsgegevens weer te geven. Sla de waarden van **Key1** en **Endpoint** op een tijdelijke locatie op. Je gebruikt ze in de volgende stappen.
