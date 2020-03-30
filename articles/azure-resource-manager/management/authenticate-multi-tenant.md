---
title: Verifiëren in meerdere tenants
description: Beschrijft hoe Azure Resource Manager verificatieaanvragen voor alle tenants verwerkt.
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 7a13ba6f6cbfc10c52484c45e4011da7a0d8ee4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75478823"
---
# <a name="authenticate-requests-across-tenants"></a>Aanvragen verifiëren voor tenants

Wanneer u een toepassing met meerdere tenants maakt, moet u mogelijk verificatieaanvragen verwerken voor resources die zich in verschillende tenants bevinden. Een veelvoorkomend scenario is wanneer een virtuele machine in een tenant moet deelnemen aan een virtueel netwerk in een andere tenant. Azure Resource Manager biedt een kopwaarde voor het opslaan van extra tokens om de aanvragen aan verschillende tenants te verifiëren.

## <a name="header-values-for-authentication"></a>Kopwaarden voor verificatie

De aanvraag heeft de volgende verificatiekopwaarden:

| Headernaam | Beschrijving | Voorbeeldwaarde |
| ----------- | ----------- | ------------ |
| Autorisatie | Primair token | Primaire &lt;token voor de drager&gt; |
| x-ms-autorisatie-hulp | Extra tokens | Hulptoken &lt;voor dragers1&gt;, &lt;Versleuteld Drager&gt;hulptoken2 &lt;, Hulptoken aan toonder3&gt; |

De hulpkop kan maximaal drie extra tokens bevatten. 

Download in de code van uw multi-tenant-app het verificatietoken voor andere tenants en sla ze op in de extra kopteksten. Alle tokens moeten van dezelfde gebruiker of toepassing zijn. De gebruiker of toepassing moet zijn uitgenodigd als gast voor de andere huurders.

## <a name="processing-the-request"></a>Behandeling van het verzoek

Wanneer uw app een verzoek naar Resource Manager verzendt, wordt de aanvraag uitgevoerd onder de identiteit van het primaire token. Het primaire token moet geldig en niet verlopen zijn. Dit token moet afkomstig zijn van een tenant die het abonnement kan beheren.

Wanneer de aanvraag verwijst naar een resource van verschillende tenant, controleert Resource Manager de extra tokens om te bepalen of de aanvraag kan worden verwerkt. Alle extra tokens in de header moeten geldig en niet verlopen zijn. Als een token is verlopen, retourneert Resource Manager een antwoordcode van 401. Het antwoord bevat de client-id en de tenant-id van het token dat niet geldig is. Als de hulpkopeen geldig token voor de tenant bevat, wordt de cross tenant-aanvraag verwerkt.

## <a name="next-steps"></a>Volgende stappen

* Zie [Verificatiestromen en toepassingsscenario's](../../active-directory/develop/authentication-flows-app-scenarios.md)voor meer informatie over verificatieaanvragen.
* Zie [Azure Active Directory-toegangstokens](../../active-directory/develop/access-tokens.md)voor meer informatie over tokens.
