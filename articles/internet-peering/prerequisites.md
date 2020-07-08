---
title: Vereisten om peering in te stellen met Microsoft
titleSuffix: Azure
description: Vereisten om peering in te stellen met Microsoft
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3c820a7be561aeef9b7e50fd0ac0cf4dee721af8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75775405"
---
# <a name="prerequisites-to-set-up-peering-with-microsoft"></a>Vereisten om peering in te stellen met Microsoft

Zorg ervoor dat aan de onderstaande vereisten wordt voldaan voordat u een nieuwe peering aanvraagt of een verouderde peering converteert naar een Azure-resource.

## <a name="azure-related-prerequisites"></a>Aan Azure gerelateerde vereisten
* **Microsoft Azure account:** Als u geen Microsoft Azure account hebt, maakt u een [Microsoft Azure-account](https://azure.microsoft.com/free). Er is een geldig en actief Microsoft Azure abonnement vereist om peering in te stellen, aangezien de peerings worden gemodelleerd als resources binnen Azure-abonnementen. Het is belang rijk te weten dat:
    * De Azure-resource typen die worden gebruikt voor het instellen van peering zijn altijd gratis Azure-producten, dat wil zeggen, er worden geen kosten in rekening gebracht voor het maken van een Azure-account of het maken van een abonnement of het verkrijgen van toegang tot de Azure-bronnen **PeerAsn** en **peering** om peering in te stellen. U hoeft dit niet te verwarren met peering-overeenkomst voor rechtstreekse peering tussen u en micro soft, de voor waarden waarvoor het team van peering expliciet wordt besproken. Neem contact op met [micro soft-peering](mailto:peering@microsoft.com) als u vragen hebt.
    * U kunt hetzelfde Azure-abonnement gebruiken om toegang te krijgen tot andere Azure-producten of-Cloud Services die gratis of betaald kunnen zijn. Wanneer u een betaald product opent, worden er kosten in rekening gebracht.
    * Als u een nieuw Azure-account en/of-abonnement maakt, komt u mogelijk in aanmerking voor gratis Azure-tegoed tijdens een proef periode die u kunt gebruiken om Azure Cloud Services te proberen. Ga als geïnteresseerde naar [Microsoft Azure account](https://azure.microsoft.com/free) voor meer informatie.

* **Peer-ASN koppelen:** Voordat u aanvragen voor peering, koppelt u eerst uw ASN-en contact gegevens aan uw abonnement. Volg de instructies in [peer ASN koppelen aan Azure-abonnement](howto-subscription-association-powershell.md).

## <a name="other-prerequisites"></a>Andere vereisten
* **PeeringDB-profiel:** Peers wordt verwacht een volledig en bijgewerkt profiel te hebben op [PeeringDB](https://www.peeringdb.com). We gebruiken deze informatie in ons registratie systeem voor het valideren van de gegevens van de peer, zoals NOC-informatie, technische contact gegevens en hun aanwezigheid op de peering faciliteiten, enzovoort.

## <a name="next-steps"></a>Volgende stappen

* [Een directe peering maken of wijzigen met behulp van de portal](howto-direct-portal.md).
* [Een verouderde Direct-peering converteren in een Azure-resource met de portal](howto-legacy-direct-portal.md)
* [Exchange-peering maken of wijzigen met behulp van de portal](howto-exchange-portal.md)
* [Een verouderde Exchange-peering converteren in een Azure-resource met de portal](howto-legacy-exchange-portal.md)