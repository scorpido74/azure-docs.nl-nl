---
title: Meerdere sites op Azure Application Gateway hosten
description: Op deze pagina wordt de ondersteuning voor meerdere sites in Azure Application Gateway beschreven.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 1/16/2019
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: f2e50217b361b62a468e872fe489381911de9c2c
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73835350"
---
# <a name="application-gateway-multiple-site-hosting"></a>Meerdere sites in Application Gateway hosten

Door meerdere sites te hosten, kunt u meer dan één webtoepassing configureren op dezelfde instantie van de toepassingsgateway. Met deze functie kunt u een efficiëntere topologie voor uw implementaties configureren door maximaal 100 websites toe te voegen aan één toepassingsgateway. Elke website kan worden omgeleid naar een eigen back-endpool. In het volgende voorbeeld verzorgt de toepassingsgateway het verkeer voor contoso.com en fabrikam.com van twee back-endservepools, ContosoServerPool en FabrikamServerPool genaamd.

![imageURLroute](./media/application-gateway-multi-site-overview/multisite.png)

> [!IMPORTANT]
> Regels worden verwerkt in de volgorde die wordt weergegeven in de portal. Het is raadzaam om eerst listeners voor meerdere locaties te configureren voordat u een basislistener configureert.  Dit zorgt ervoor dat verkeer naar de juiste back-end wordt geleid. Als een basislistener als eerste wordt weergegeven en overeenkomt met een inkomende aanvraag, wordt deze door die listener verwerkt.

Aanvragen voor `http://contoso.com` worden gerouteerd naar ContoServerPool en aanvragen voor `http://fabrikam.com` worden gerouteerd naar FabrikamServerPool.

Op dezelfde manier kunnen twee subdomeinen van hetzelfde bovenliggende domein worden gehost op dezelfde gateway-implementatie. Voorbeelden van subdomeinen die worden gehost op één toepassingsgateway-implementatie, zijn `http://blog.contoso.com` en `http://app.contoso.com`.

## <a name="host-headers-and-server-name-indication-sni"></a>Hostheaders en Servernaamindicatie (SNI)

Er zijn drie algemene mechanismen om het hosten van meerdere sites in te schakelen op dezelfde infrastructuur.

1. Het is mogelijk meerdere webtoepassingen te hosten, elk op een uniek IP-adres.
2. Gebruik de hostnaam voor het hosten van meerdere webtoepassingen op hetzelfde IP-adres.
3. Gebruik verschillende poorten voor het hosten van meerdere webtoepassingen op hetzelfde IP-adres.

Op dit ogenblik krijgt een toepassingsgateway één openbaar IP-adres waarop deze luistert naar verkeer. Daarom wordt de ondersteuning van meerdere toepassingen, elk met een eigen IP-adres, momenteel niet ondersteund. Application Gateway ondersteunt het hosten van meerdere toepassingen die elk luisteren op verschillende poorten. Dit scenario zou echter vereisen dat de toepassingen verkeer accepteren op niet-standaardpoorten en dat is vaak geen gewenste configuratie. Application Gateway maakt gebruik van HTTP 1.1-hostheaders voor het hosten van meer dan één website op hetzelfde openbare IP-adres en dezelfde poort. De sites die op de toepassingsgateway worden gehost, kunnen ook SSL-offload ondersteunen met de Servernaamindicatie (SNI) met TLS-extensie. Dit scenario houdt in dat de clientbrowser en back-end-webfarm de HTTP/1.1- en TLS-extensie moeten ondersteunen zoals gedefinieerd in RFC 6066.

## <a name="listener-configuration-element"></a>Configuratie-element Listener

Configuratie-element HTTPListener is verbeterd voor het ondersteunen van indicatie-elementen van de hostnaam en servernaam die worden gebruikt door de toepassingsgateway om verkeer naar de back-endpool te routeren. Het volgende codevoorbeeld is het fragment van het HttpListeners-element van het sjabloonbestand.

```json
"httpListeners": [
    {
        "name": "appGatewayHttpsListener1",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
            },
            "Protocol": "Https",
            "SslCertificate": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
            },
            "HostName": "contoso.com",
            "RequireServerNameIndication": "true"
        }
    },
    {
        "name": "appGatewayHttpListener2",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
            },
            "Protocol": "Http",
            "HostName": "fabrikam.com",
            "RequireServerNameIndication": "false"
        }
    }
],
```

U kunt ook [Resource Manager-sjabloon met het hosten van meerdere sites](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting) bezoeken voor een end-to-end implementatie op basis van sjablonen.

## <a name="routing-rule"></a>Routeringsregel

Er is geen wijziging vereist in de routeringsregel. De routeringsregel Basic moet nog steeds worden gekozen om de geschikte site-listener te binden aan de overeenkomende back-end-adresgroep.

```json
"requestRoutingRules": [
{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener1')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

},
{
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener2')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/FabrikamServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}
]
```

## <a name="next-steps"></a>Volgende stappen

Nadat u meer hebt geleerd over het hosten van meerdere sites, gaat u naar [Een toepassingsgateway maken met het hosten van meerdere sites](application-gateway-create-multisite-azureresourcemanager-powershell.md) om een toepassingsgateway te maken met de mogelijkheid om meer dan één webtoepassing te ondersteunen.