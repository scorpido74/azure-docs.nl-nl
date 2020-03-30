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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775405"
---
# <a name="prerequisites-to-set-up-peering-with-microsoft"></a>Vereisten om peering in te stellen met Microsoft

Zorg ervoor dat aan de onderstaande vereisten wordt voldaan voordat u een nieuwe peering aanvraagt of een verouderde peering converteert naar Azure-bron.

## <a name="azure-related-prerequisites"></a>Azure-gerelateerde vereisten
* **Microsoft Azure-account:** Als u geen Microsoft Azure-account hebt, maakt u een [Microsoft Azure-account](https://azure.microsoft.com/free). Een geldig en actief Microsoft Azure-abonnement is vereist om peering in te stellen, omdat de peerings zijn gemodelleerd als resources binnen Azure-abonnementen. Het is belangrijk op te merken dat:
    * De Azure-brontypen die worden gebruikt om peering in te stellen, zijn altijd gratis Azure-producten, d.w.z. dat u geen kosten in rekening wordt gebracht voor het maken van een Azure-account of het maken van een abonnement of het openen van de Azure-bronnen **PeerAsn** en **Peering** om peering in te stellen. Dit is niet te verwarren met peering overeenkomst voor Direct peering tussen u en Microsoft, de voorwaarden waarvoor expliciet worden besproken met onze peering team. Neem contact op met [Microsoft peering](mailto:peering@microsoft.com) als er vragen in dit verband.
    * U hetzelfde Azure-abonnement gebruiken om toegang te krijgen tot andere Azure-producten of cloudservices die mogelijk gratis of betaald zijn. Wanneer u toegang krijgt tot een betaald product, brengt u kosten in rekening.
    * Als u een nieuw Azure-account en/of -abonnement maakt, komt u mogelijk in aanmerking voor gratis Azure-tegoed tijdens een proefperiode die u gebruiken om Azure Cloud-services uit te proberen. Ga als u geïnteresseerd bent naar [het Microsoft Azure-account](https://azure.microsoft.com/free) voor meer informatie.

* **Associate Peer ASN:** Voordat u om peering vraagt, koppelt u eerst uw ASN en neemt u contact op met uw abonnement. Volg de instructies in [Associate Peer ASN aan Azure Subscription](howto-subscription-association-powershell.md).

## <a name="other-prerequisites"></a>Andere voorwaarden
* **PeeringDB profiel:** Peers zullen naar verwachting een volledig en up-to-date profiel hebben op [PeeringDB](https://www.peeringdb.com). We gebruiken deze informatie in ons registratiesysteem om de gegevens van de peer te valideren, zoals NOC-informatie, technische contactgegevens en hun aanwezigheid in de peeringfaciliteiten, enz.

## <a name="next-steps"></a>Volgende stappen

* [Een direct peering maken of wijzigen met behulp van de portal](howto-direct-portal.md).
* [Een verouderde Direct-peering converteren in een Azure-resource met de portal](howto-legacy-direct-portal.md)
* [Exchange-peering maken of wijzigen met behulp van de portal](howto-exchange-portal.md)
* [Een verouderde Exchange-peering converteren in een Azure-resource met de portal](howto-legacy-exchange-portal.md)