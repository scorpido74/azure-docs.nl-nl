---
title: Aangepaste subdomeinen
titleSuffix: Azure Cognitive Services
description: Aangepaste subdomeinnamen voor elke Cognitive Service-bron worden gemaakt via de Azure-portal, Azure Cloud Shell of Azure CLI.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: erhopf
ms.openlocfilehash: 108e5ebfa34db711af4ff33cbf4fbba897b8947d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647682"
---
# <a name="custom-subdomain-names-for-cognitive-services"></a>Aangepaste subdomeinnamen voor cognitieve services

Azure Cognitive Services gebruiken aangepaste subdomeinnamen voor elke bron die is gemaakt via de [Azure-portal,](https://portal.azure.com) [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)of Azure [CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). In tegenstelling tot regionale eindpunten, die gebruikelijk waren voor alle klanten in een specifieke Azure-regio, zijn aangepaste subdomeinnamen uniek voor de bron. Aangepaste subdomeinnamen zijn vereist om functies zoals Azure Active Directory (Azure AD) in te schakelen voor verificatie.

## <a name="how-does-this-impact-existing-resources"></a>Welke invloed heeft dit op bestaande bronnen?

Cognitive Services-resources die vóór 1 juli 2019 zijn gemaakt, gebruiken de regionale eindpunten voor de bijbehorende service. Deze eindpunten werken met bestaande en nieuwe resources.

Als u een bestaande bron wilt migreren om aangepaste subdomeinnamen te gebruiken, zodat u functies zoals Azure AD inschakelen, volgt u de volgende instructies:

1. Meld u aan bij de Azure-portal en zoek de bron Cognitive Services waaraan u een aangepaste subdomeinnaam wilt toevoegen.
2. Zoek en selecteer aangepaste **domeinnaam genereren**in het **blad Overzicht** .
3. Hiermee wordt een paneel geopend met instructies om een uniek aangepast subdomein voor uw resource te maken.
   > [!WARNING]
   > Nadat u een aangepaste subdomeinnaam hebt **gemaakt, kan deze niet** meer worden gewijzigd.

## <a name="do-i-need-to-update-my-existing-resources"></a>Moet ik mijn bestaande bronnen bijwerken?

Nee. Het regionale eindpunt blijft werken voor nieuwe en bestaande Cognitieve Services en de aangepaste subdomeinnaam is optioneel. Zelfs als een aangepaste subdomeinnaam wordt toegevoegd, blijft het regionale eindpunt met de resource werken.

## <a name="what-if-an-sdk-asks-me-for-the-region-for-a-resource"></a>Wat als een SDK mij vraagt voor de regio voor een bron?

> [!WARNING]
> De spraakservices bieden op dit moment **geen** ondersteuning voor aangepaste subdomeinen. Gebruik de regionale eindpunten bij het gebruik van de Spraakdiensten en bijbehorende SDK's.

Regionale eindpunten en aangepaste subdomeinnamen worden ondersteund en kunnen door elkaar worden gebruikt. Het volledige eindpunt is echter vereist.

Regio-informatie is beschikbaar in het **overzichtsblad** voor uw resource in de [Azure-portal.](https://portal.azure.com) Zie Zie Is er een [lijst met regionale eindpunten?](#is-there-a-list-of-regional-endpoints)

## <a name="are-custom-subdomain-names-regional"></a>Zijn aangepaste subdomeinnamen regionaal?

Ja. Als u een aangepaste subdomeinnaam gebruikt, worden de regionale aspecten van uw resource voor Cognitieve Services niet gewijzigd.

## <a name="what-are-the-requirements-for-a-custom-subdomain-name"></a>Wat zijn de vereisten voor een aangepaste subdomeinnaam?

Een aangepaste subdomeinnaam is uniek voor uw bron. De naam kan alleen alfanumerieke `-` tekens en het teken bevatten; het moet tussen 2 en 64 tekens in `-`lengte zijn en kan niet eindigen met een .

## <a name="can-i-change-a-custom-domain-name"></a>Kan ik een aangepaste domeinnaam wijzigen?

Nee. Nadat een aangepaste subdomeinnaam is gemaakt en aan een resource is gekoppeld, kan deze niet worden gewijzigd.

## <a name="can-i-reuse-a-custom-domain-name"></a>Kan ik een aangepaste domeinnaam opnieuw gebruiken?

Elke aangepaste subdomeinnaam is uniek, dus om een aangepaste subdomeinnaam die u aan een resource voor Cognitive Services hebt toegewezen, opnieuw te gebruiken, moet u de bestaande bron verwijderen. Nadat de bron is verwijderd, u de aangepaste subdomeinnaam opnieuw gebruiken.

## <a name="is-there-a-list-of-regional-endpoints"></a>Is er een lijst met regionale eindpunten?

Ja. Dit is een lijst met regionale eindpunten die u gebruiken met Azure Cognitive Services-bronnen.

> [!NOTE]
> De Translator Text API en Bing Search API's maken gebruik van globale eindpunten.

| Eindpunttype | Regio | Eindpunt |
|---------------|--------|----------|
| Public | Globale (vertalertekst & Bing) | `https://api.cognitive.microsoft.com` |
| | Australië - oost | `https://australiaeast.api.cognitive.microsoft.com` |
| | Brazilië - zuid | `https://brazilsouth.api.cognitive.microsoft.com` |
| | Canada - midden | `https://canadacentral.api.cognitive.microsoft.com` |
| | VS - centraal | `https://centralus.api.cognitive.microsoft.com` |
| | Azië - oost | `https://eastasia.api.cognitive.microsoft.com` |
| | VS - oost | `https://eastus.api.cognitive.microsoft.com` |
| | VS - oost 2 | `https://eastus2.api.cognitive.microsoft.com` |
| | Frankrijk - centraal | `https://francecentral.api.cognitive.microsoft.com` |
| | India - centraal | `https://centralindia.api.cognitive.microsoft.com` |
| | Japan - oost | `https://japaneast.api.cognitive.microsoft.com` |
| | Korea - centraal | `https://koreacentral.api.cognitive.microsoft.com` |
| | VS - noord-centraal | `https://northcentralus.api.cognitive.microsoft.com` |
| | Europa - noord | `https://northeurope.api.cognitive.microsoft.com` |
| | Zuid-Afrika Noord | `https://southafricanorth.api.cognitive.microsoft.com` |
| | VS - zuid-centraal | `https://southcentralus.api.cognitive.microsoft.com` |
| | Azië - zuidoost | `https://southeastasia.api.cognitive.microsoft.com` |
| | Verenigd Koninkrijk Zuid | `https://uksouth.api.cognitive.microsoft.com` |
| | VS - west-centraal | `https://westcentralus.api.cognitive.microsoft.com` |
| | Europa -west | `https://westeurope.api.cognitive.microsoft.com` |
| | VS - west | `https://westus.api.cognitive.microsoft.com` |
| | VS - west 2 | `https://westus2.api.cognitive.microsoft.com` |
| US Gov | VS (overheid) - Virginia | `https://virginia.api.cognitive.microsoft.us` |
| China | China Oost 2 | `https://chinaeast2.api.cognitive.azure.cn` |
| | China - noord | `https://chinanorth.api.cognitive.azure.cn` |

## <a name="see-also"></a>Zie ook

* [Wat zijn de Cognitive Services?](Welcome.md)
* [Verificatie](authentication.md)
