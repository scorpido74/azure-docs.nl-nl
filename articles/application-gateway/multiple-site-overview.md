---
title: Meerdere sites op Azure Application Gateway hosten
description: Dit artikel bevat een overzicht van de ondersteuning voor meerdere sites van Azure-toepassing gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 03/11/2020
ms.author: amsriva
ms.topic: conceptual
ms.openlocfilehash: c43ac0923e0d3d76c25657f4870a0a0431bc8b6e
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096440"
---
# <a name="application-gateway-multiple-site-hosting"></a>Meerdere sites in Application Gateway hosten

Als u meerdere sites host, kunt u meer dan één webtoepassing configureren op dezelfde poort van een toepassings gateway. Met deze functie kunt u een efficiëntere topologie voor uw implementaties configureren door maximaal 100 websites toe te voegen aan één toepassingsgateway. Elke website kan worden omgeleid naar een eigen back-endpool. In het volgende voor beeld verzendt Application Gateway verkeer voor `contoso.com` en `fabrikam.com` van twee back-end-server groepen met de naam ContosoServerPool en FabrikamServerPool.

![imageURLroute](./media/multiple-site-overview/multisite.png)

> [!IMPORTANT]
> Regels worden verwerkt in de volg orde waarin ze worden weer gegeven in de portal voor de V1-SKU. Voor de v2-SKU hebben exacte overeenkomsten een hogere prioriteit. Het is raadzaam om eerst listeners voor meerdere locaties te configureren voordat u een basislistener configureert.  Dit zorgt ervoor dat verkeer naar de juiste back-end wordt geleid. Als een basislistener als eerste wordt weergegeven en overeenkomt met een inkomende aanvraag, wordt deze door die listener verwerkt.

Aanvragen voor `http://contoso.com` worden gerouteerd naar ContoServerPool en aanvragen voor `http://fabrikam.com` worden gerouteerd naar FabrikamServerPool.

Op dezelfde manier kunt u meerdere subdomeinen van hetzelfde bovenliggende domein hosten op dezelfde toepassings gateway-implementatie. U kunt bijvoorbeeld `http://blog.contoso.com` en `http://app.contoso.com` hosten op één toepassings gateway-implementatie.

## <a name="host-headers-and-server-name-indication-sni"></a>Hostheaders en Servernaamindicatie (SNI)

Er zijn drie algemene mechanismen om het hosten van meerdere sites in te schakelen op dezelfde infrastructuur.

1. Het is mogelijk meerdere webtoepassingen te hosten, elk op een uniek IP-adres.
2. Gebruik de hostnaam voor het hosten van meerdere webtoepassingen op hetzelfde IP-adres.
3. Gebruik verschillende poorten voor het hosten van meerdere webtoepassingen op hetzelfde IP-adres.

Momenteel Application Gateway ondersteunt één openbaar IP-adres waar het verkeer wordt geluisterd. Meerdere toepassingen, elk met een eigen IP-adres, worden op dit moment niet ondersteund. 

Application Gateway ondersteunt meerdere toepassingen elke die op verschillende poorten luistert, maar dit scenario vereist dat de toepassingen verkeer accepteren op niet-standaard poorten. Dit is vaak geen configuratie die u wilt.

Application Gateway maakt gebruik van HTTP 1.1-hostheaders voor het hosten van meer dan één website op hetzelfde openbare IP-adres en dezelfde poort. De sites die op de toepassingsgateway worden gehost, kunnen ook SSL-offload ondersteunen met de Servernaamindicatie (SNI) met TLS-extensie. Dit scenario houdt in dat de clientbrowser en back-end-webfarm de HTTP/1.1- en TLS-extensie moeten ondersteunen zoals gedefinieerd in RFC 6066.

## <a name="listener-configuration-element"></a>Configuratie-element Listener

Bestaande HTTPListener-configuratie-elementen zijn uitgebreid ter ondersteuning van de hostnaam en de server naam indicatie-elementen. Het wordt gebruikt door Application Gateway om verkeer door te sturen naar de juiste back-end-groep. 

Het volgende code voorbeeld is het fragment van een HttpListeners-element van een sjabloon bestand:

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

Er is geen wijziging vereist in de routerings regel. De routeringsregel Basic moet nog steeds worden gekozen om de geschikte site-listener te binden aan de overeenkomende back-end-adresgroep.

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

Nadat u meer hebt geleerd over het hosten van meerdere sites, gaat u naar [Een toepassingsgateway maken met het hosten van meerdere sites](tutorial-multiple-sites-powershell.md) om een toepassingsgateway te maken met de mogelijkheid om meer dan één webtoepassing te ondersteunen.

