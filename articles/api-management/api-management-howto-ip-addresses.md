---
title: IP-adressen van azure API Management-service | Microsoft Documenten
description: Meer informatie over het ophalen van de IP-adressen van een Azure API Management-service en wanneer deze worden gewijzigd.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 45501fee9ae6ff47643a1ed197a07c4ba598e981
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047733"
---
# <a name="ip-addresses-of-azure-api-management"></a>IP-adressen van Azure API Management

In dit artikel beschrijven we hoe u de IP-adressen van de Azure API Management-service ophalen. IP-adressen kunnen openbaar of privé zijn als de service zich in een virtueel netwerk bevindt.

U IP-adressen gebruiken om firewallregels te maken, het binnenkomende verkeer naar de backendservices te filteren of het uitgaande verkeer te beperken.

## <a name="ip-addresses-of-api-management-service"></a>IP-adressen van API Management-service

Elke API Management-serviceinstantie in de laag Ontwikkelaars, Basic, Standard of Premium heeft openbare IP-adressen, die alleen exclusief zijn voor die service-instantie (ze worden niet gedeeld met andere bronnen). 

U de IP-adressen ophalen uit het overzichtsdashboard van uw resource in de Azure-portal.

![IP-adres API-beheer](media/api-management-howto-ip-addresses/public-ip.png)

U ze ook programmatisch ophalen met de volgende API-aanroep:

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

Openbare IP-adressen maken deel uit van het antwoord:

```json
{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.77.143.53"
    ],
    ...
  }
  ...
}
```

Bij [multiregionale implementaties](api-management-howto-deploy-multi-region.md)heeft elke regionale implementatie één openbaar IP-adres.

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>IP-adressen van API Management-service in VNet

Als uw API Management-service zich in een virtueel netwerk bevindt, heeft deze twee typen IP-adressen: openbaar en privé.

Openbare IP-adressen worden gebruikt voor `3443` interne communicatie op de poort - voor het beheren van configuratie (bijvoorbeeld via Azure Resource Manager). In de externe VNet-configuratie worden ze ook gebruikt voor runtime API-verkeer. Wanneer een aanvraag wordt verzonden van API-beheer naar een openbare back-end (internetgerichte) backend, is een openbaar IP-adres zichtbaar als de oorsprong van de aanvraag.

Vip-adressen (Private virtual IP), **die alleen** beschikbaar zijn in de [interne VNet-modus,](api-management-using-with-internal-vnet.md)worden gebruikt om vanuit het netwerk verbinding te maken met API Management-eindpunten - gateways, de ontwikkelaarsportal en het beheervlak voor directe API-toegang. U ze gebruiken voor het instellen van DNS-records binnen het netwerk.

U ziet adressen van beide typen in de Azure-portal en in de reactie van de API-aanroep:

![API-beheer in VNet-IP-adres](media/api-management-howto-ip-addresses/vnet-ip.png)


```json
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>

{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.85.20.170"
    ],
    "privateIPAddresses": [
      "192.168.1.5"
    ],
    ...
  },
  ...
}
```

API Management gebruikt een openbaar IP-adres voor verbindingen buiten het VNet en een privé-IP-adres voor verbindingen binnen het VNet.

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>IP-adressen van de API-beheerservice voor verbruikslagen

Als uw API Management-service een service voor verbruikslaag is, heeft deze geen specifiek IP-adres. De service voor verbruikslagen wordt uitgevoerd op een gedeelde infrastructuur en zonder een deterministisch IP-adres. 

Voor verkeersbeperkingsdoeleinden u het bereik van IP-adressen van Azure-datacenters gebruiken. Raadpleeg [het documentatieartikel voor Azure Functions](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses) voor precieze stappen.

## <a name="changes-to-the-ip-addresses"></a>Wijzigingen in de IP-adressen

In de lagen Developer, Basic, Standard en Premium van API Management zijn de openbare IP-adressen (VIP) statisch voor de levensduur van een service, met de volgende uitzonderingen:

* De service wordt verwijderd en vervolgens opnieuw gemaakt.
* Het serviceabonnement [wordt opgeschort](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) of [gewaarschuwd](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (bijvoorbeeld voor niet-betaling) en vervolgens opnieuw ingesteld.
* Azure Virtual Network wordt toegevoegd aan of verwijderd uit de service.
* API Management-service wordt geschakeld tussen externe en interne VNet-implementatiemodus.

Bij [multiregionale implementaties](api-management-howto-deploy-multi-region.md)verandert het regionale IP-adres als een regio wordt ontruimd en vervolgens wordt hersteld.
