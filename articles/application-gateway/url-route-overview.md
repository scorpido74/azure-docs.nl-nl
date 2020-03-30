---
title: Overzicht van routering van op URL gebaseerde inhoud van Azure Application Gateway
description: In dit artikel vindt u een overzicht van de op URL gebaseerde inhoudsroutering op basis van Azure Application Gateway, urlpathmap-configuratie en pathbasedrouteregel.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 09/10/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: e20acb131b1a091fef858dab34705f4a8d3b4c4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77251835"
---
# <a name="url-path-based-routing-overview"></a>Overzicht van op URL-pad gebaseerde routering

Met op URL-pad gebaseerde routering kunt u verkeer routeren naar back-endserverpools die zijn gebaseerd op de URL-paden van de aanvraag. 

Een van de scenario's is het routeren van aanvragen voor verschillende inhoudstypen naar verschillende back-endserverpools.

In het volgende voorbeeld verzorgt de toepassingsgateway het verkeer voor contoso.com van drie back-endserverpools: VideoServerPool, ImageServerPool en DefaultServerPool.

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

Aanvragen voor\:http -contoso.com/video/* worden doorgestuurd naar\:VideoServerPool en http -contoso.com/images/* worden doorgestuurd naar ImageServerPool. Als geen van de padpatronen overeenkomen, wordt DefaultServerPool geselecteerd.

> [!IMPORTANT]
> Voor de v1 SKU worden regels verwerkt in de volgorde waarin ze in de portal worden vermeld. Als een basislistener als eerste wordt weergegeven en overeenkomt met een inkomende aanvraag, wordt deze door die listener verwerkt. Voor de v2 SKU hebben exacte overeenkomsten een hogere prioriteit. Het is echter sterk aanbevolen om multi-site listeners eerst te configureren voordat u een basislistener configureert. Dit zorgt ervoor dat verkeer naar de juiste back-end wordt geleid.

## <a name="urlpathmap-configuration-element"></a>Configuratie-element UrlPathMap

Het element UrlPathMap wordt gebruikt om padpatronen op te geven voor back-endservergroepstoewijzingen. Het volgende codevoorbeeld is het fragment van het urlPathMap-element van het sjabloonbestand.

```json
"urlPathMaps": [{
    "name": "{urlpathMapName}",
    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/urlPathMaps/{urlpathMapName}",
    "properties": {
        "defaultBackendAddressPool": {
            "id": "/subscriptions/    {subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName1}"
        },
        "defaultBackendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpSettingsList/{settingname1}"
        },
        "pathRules": [{
            "name": "{pathRuleName}",
            "properties": {
                "paths": [
                    "{pathPattern}"
                ],
                "backendAddressPool": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName2}"
                },
                "backendHttpsettings": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpsettingsList/{settingName2}"
                }
            }
        }]
    }
}]
```

### <a name="pathpattern"></a>PathPatroon

PathPattern is een lijst met padpatronen die overeenkomen. Elk hiervan moet beginnen met / en de enige plaats waar een * is toegestaan, is aan het einde na een /. De tekenreeks die aan de padmatcher wordt gevoerd, bevat geen tekst na de eerste? of #, en die chars zijn hier niet toegestaan. Anders zijn alle tekens die in een URL zijn toegestaan, toegestaan in PathPattern.

De ondersteunde patronen zijn afhankelijk van het feit of u Application Gateway v1 of v2 implementeert:

#### <a name="v1"></a>v1

Padregels zijn hoofdletters ongevoelig.

|v1-padpatroon  |Wordt ondersteund?  |
|---------|---------|
|`/images/*`     |ja|
|`/images*`     |nee|
|`/images/*.jpg`     |nee|
|`/*.jpg`     |nee|
|`/Repos/*/Comments/*`     |nee|
|`/CurrentUser/Comments/*`     |ja|

#### <a name="v2"></a>v2

Padregels zijn hoofdletters ongevoelig.

|v2-padpatroon  |Wordt ondersteund?  |
|---------|---------|
|`/images/*`     |ja|
|`/images*`     |ja|
|`/images/*.jpg`     |nee|
|`/*.jpg`     |nee|
|`/Repos/*/Comments/*`     |nee|
|`/CurrentUser/Comments/*`     |ja|

U kunt een [Resource Manager-sjabloon met op URL gebaseerde routering](https://azure.microsoft.com/documentation/templates/201-application-gateway-url-path-based-routing) bekijken voor meer informatie.

## <a name="pathbasedrouting-rule"></a>PathBasedRouting-regel

RequestRoutingRule van type PathBasedRouting wordt gebruikt voor het verbinden van een listener met een urlPathMap. Alle aanvragen die zijn ontvangen voor deze listener, worden gerouteerd op basis van een beleid dat is opgegeven in urlPathMap.
Fragment van PathBasedRouting-regel:

```json
"requestRoutingRules": [
    {

"name": "{ruleName}",
"id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/requestRoutingRules/{ruleName}",
"properties": {
    "ruleType": "PathBasedRouting",
    "httpListener": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/httpListeners/<listenerName>"
    },
    "urlPathMap": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/ urlPathMaps/{urlpathMapName}"
    },

}
    }
]
```

## <a name="next-steps"></a>Volgende stappen

Nadat u meer hebt geleerd over routering van op URL gebaseerde inhoud, gaat u naar [Een toepassingsgateway maken met behulp van URL-gebaseerde routering](create-url-route-portal.md) om een toepassingsgateway te maken met URL-routeringsregels.
