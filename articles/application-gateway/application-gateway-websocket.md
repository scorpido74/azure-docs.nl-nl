---
title: WebSocket-ondersteuning in Azure Application Gateway
description: Application Gateway biedt systeemeigen ondersteuning voor WebSocket in gateways van alle grootten. Er zijn geen instellingen voor gebruikersconfigureerbare instellingen.
author: vhorne
ms.author: amsriva
ms.service: application-gateway
services: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.openlocfilehash: baa02c4d946a121f26f421af99835ae2bea18847
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74130329"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Overzicht van WebSocket-ondersteuning in Application Gateway

Application Gateway biedt systeemeigen ondersteuning voor WebSocket in gateways van alle grootten. Er is geen door de gebruiker configureerbare instelling om selectief WebSocket-ondersteuning in of uit te schakelen. 

WebSocket-protocol gestandaardiseerd in [RFC6455](https://tools.ietf.org/html/rfc6455) maakt een volledige duplexcommunicatie tussen een server en een client mogelijk via een langlopende TCP-verbinding. Deze functie zorgt voor een meer interactieve communicatie tussen de webserver en de client, die bidirectioneel kan zijn zonder de noodzaak van polling zoals vereist in HTTP-gebaseerde implementaties. WebSocket heeft een lage overhead in tegenstelling tot HTTP en kan dezelfde TCP-verbinding opnieuw gebruiken voor meerdere aanvragen/antwoorden, wat resulteert in een efficiënter gebruik van resources. WebSocket-protocollen zijn ontworpen om te werken via traditionele HTTP-poorten van 80 en 443.

U een standaard HTTP-listener op poort 80 of 443 blijven gebruiken om WebSocket-verkeer te ontvangen. WebSocket-verkeer wordt vervolgens naar de backendserver met WebSocket-server geleid met behulp van de juiste backendpool zoals opgegeven in toepassingsgatewayregels. De backendserver moet reageren op de toepassingsgateways, die worden beschreven in de [overzichtssectie van de statussonde.](application-gateway-probe-overview.md) Statussen van toepassingsgateways zijn alleen HTTP/HTTPS. Elke backendserver moet reageren op HTTP-sondes voor toepassingsgateway om WebSocket-verkeer naar de server te routeren.

Het wordt gebruikt in apps die profiteren van snelle, realtime communicatie, zoals chat-, dashboard- en game-apps.

## <a name="how-does-websocket-work"></a>Hoe werkt WebSocket

Om een WebSocket-verbinding tot stand te brengen, wordt een specifieke HTTP-gebaseerde handdruk uitgewisseld tussen de client en de server. Als dit is gelukt, wordt het protocol voor toepassingslagen 'geüpgraded' van HTTP naar WebSockets, met behulp van de eerder ingestelde TCP-verbinding. Zodra dit gebeurt, http is volledig uit het beeld; gegevens kunnen worden verzonden of ontvangen met behulp van het WebSocket-protocol door beide eindpunten, totdat de WebSocket-verbinding is gesloten. 

![websocket](./media/application-gateway-websocket/websocket.png)

### <a name="listener-configuration-element"></a>Configuratie-element Listener

Een bestaande HTTP-listener kan worden gebruikt om WebSocket-verkeer te ondersteunen. Het volgende is een fragment van een httpListeners-element uit een voorbeeldsjabloonbestand. U hebt zowel HTTP- als HTTPS-listeners nodig om WebSocket en veilig WebSocket-verkeer te ondersteunen. Op dezelfde manier u de portal of Azure PowerShell gebruiken om een toepassingsgateway met luisteraars op poort 80/443 te maken om WebSocket-verkeer te ondersteunen.

```json
"httpListeners": [
        {
            "name": "appGatewayHttpsListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/DefaultFrontendPublicIP"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort443'"
                },
                "Protocol": "Https",
                "SslCertificate": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/sslCertificates/appGatewaySslCert1'"
                },
            }
        },
        {
            "name": "appGatewayHttpListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/appGatewayFrontendIP'"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort80'"
                },
                "Protocol": "Http",
            }
        }
    ],
```

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>BackendAddressPool, BackendHttpSetting en Routeringsregelconfiguratie

Een BackendAddressPool wordt gebruikt om een backendpool te definiëren met websocketservers. De backendHttpSetting wordt gedefinieerd met een backend-poort 80 en 443. De time-outwaarde van de aanvraag in HTTP-instellingen is ook van toepassing op de WebSocket-sessie. Er is geen wijziging vereist in de routeringsregel, die wordt gebruikt om de juiste listener te koppelen aan de bijbehorende backend-adresgroep. 

```json
"requestRoutingRules": [{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpsListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}, {
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }

    }
}]
```

## <a name="websocket-enabled-backend"></a>Backend met WebSocket ingeschakeld

Op uw backend moet een HTTP/HTTPS-webserver worden uitgevoerd op de geconfigureerde poort (meestal 80/443) om WebSocket te laten werken. Deze vereiste is omdat websocket-protocol vereist dat de eerste handdruk HTTP is met een upgrade naar het WebSocket-protocol als kopveld. Het volgende is een voorbeeld van een koptekst:

```
    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: https://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13
```

Een andere reden hiervoor is dat de backend health probe van de toepassingsgateway alleen HTTP- en HTTPS-protocollen ondersteunt. Als de backendserver niet reageert op HTTP- of HTTPS-sondes, wordt deze uit de backendpool gehaald.

## <a name="next-steps"></a>Volgende stappen

Nadat u meer te weten bent te komen over WebSocket-ondersteuning, gaat u [naar het maken van een toepassingsgateway](quick-create-powershell.md) voor toepassingen om aan de slag te gaan met een websocket-webtoepassing.