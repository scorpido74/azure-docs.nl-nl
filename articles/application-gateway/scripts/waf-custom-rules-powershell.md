---
title: Voorbeeld van Azure PowerShell Script - Aangepaste WAF-regels maken
description: Voorbeeld van Azure PowerShell Script - Aangepaste regels voor WebApplication Firewall maken
author: vhorne
ms.service: application-gateway
ms.topic: sample
ms.date: 6/7/2019
ms.author: victorh
ms.openlocfilehash: ffdde80598322222e2a8f000eee8be269becdd11
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "66743421"
---
# <a name="create-waf-custom-rules-with-azure-powershell"></a>WAF-aangepaste regels maken met Azure PowerShell

Met dit script wordt een Application Gateway Web Application Firewall gemaakt die aangepaste regels gebruikt. De aangepaste regel blokkeert het verkeer als de aanvraagkoptekst user-agent *evilbot*bevat.

## <a name="prerequisites"></a>Vereisten

### <a name="azure-powershell-module"></a>Azure PowerShell-module

Als u ervoor kiest Azure PowerShell lokaal te installeren en te gebruiken, vereist dit script de Azure PowerShell-moduleversie 2.1.0 of hoger.

1. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps).
2. Voer `Connect-AzAccount` uit om een verbinding met Azure tot stand te brengen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/application-gateway/waf-rules/waf-custom-rules.ps1 "Custom WAF rules")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep, de toepassingsgateway en alle gerelateerde resources te verwijderen.

```powershell
Remove-AzResourceGroup -Name CustomRulesTest
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om de implementatie te maken. Elk item in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Hiermee maakt u de subnetconfiguratie. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Hiermee maakt u het virtuele netwerk met de subnetconfiguraties. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Hiermee maakt u het openbare IP-adres voor de toepassingsgateway. |
| [Nieuwe azApplicationGatewayIP-configuratie](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | Hiermee maakt u de configuratie die een subnet koppelt aan de toepassingsgateway. |
| [Nieuw-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | Hiermee maakt u de configuratie die een openbaar IP-adres toewijst aan de toepassingsgateway. |
| [Nieuw-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | Hiermee wijst u een poort toe die moet worden gebruikt voor toegang tot de toepassingsgateway. |
| [Nieuw-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | Hiermee maakt u een back-endpool voor een toepassingsgateway. |
| [>-AzApplicationGatewayBackendhttp-instellingen](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | Hiermee configureert u instellingen voor een back-endpool. |
| [Nieuwe azApplicationGatewayhttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | Hiermee maakt u een listener. |
| [Nieuwe azapplicationgateway-routeringsregel](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | Hiermee maakt u een routeringsregel. |
| [Nieuw-azapplicationgatewaysku](/powershell/module/az.network/new-azapplicationgatewaysku) | Geef de laag en capaciteit voor een toepassingsgateway op. |
| [Nieuwe AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | Hiermee maakt u een toepassingsgateway. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep en alle daarin opgenomen resources. |
|[Configuratie van nieuwe azapplicationgatewayautoscale](/powershell/module/az.network/New-AzApplicationGatewayAutoscaleConfiguration)|Hiermee maakt u een automatische schaalconfiguratie voor de toepassingsgateway.|
|[Nieuw-azapplicationGatewayFirewallMatchVariabele](/powershell/module/az.network/New-AzApplicationGatewayFirewallMatchVariable)|Hiermee maakt u een overeenkomstvariabele voor firewallconditie.|
|[Nieuw-azapplicationgatewayfirewallvoorwaarde](/powershell/module/az.network/New-AzApplicationGatewayFirewallCondition)|Hiermee maakt u een overeenkomende voorwaarde voor aangepaste regel.|
|[Aangepaste regel voor nieuwe azapplicationgatewayfirewall](/powershell/module/az.network/New-AzApplicationGatewayFirewallCustomRule)|Hiermee maakt u een nieuwe aangepaste regel voor het firewallbeleid voor de toepassingsgateway.|
|[Nieuw-azapplicationgatewayfirewallbeleid](/powershell/module/az.network/New-AzApplicationGatewayFirewallPolicy)|Hiermee maakt u een firewallbeleid voor toepassingsgateways.|
|[Configuratie van nieuwe azapplicationgatewaywebtoepassingsfirewalls](/powershell/module/az.network/New-AzApplicationGatewayWebApplicationFirewallConfiguration)|Hiermee maakt u een WAF-configuratie voor een toepassingsgateway.|

## <a name="next-steps"></a>Volgende stappen

- Zie [Aangepaste regels voor webtoepassingsfirewall voor](../custom-waf-rules-overview.md) meer informatie over aangepaste WAF-regels
- Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/overview).
- Voorbeelden van extra PowerShell-voorbeeldscripts voor de toepassingsgateway zijn te vinden in de [documentatie over Azure Application Gateway](../powershell-samples.md).
