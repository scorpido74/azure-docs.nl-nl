---
title: Azure PowerShell-voorbeeld script-WAF aangepaste regels maken
description: 'Azure PowerShell script-voor beeld: Web Application firewall maken op Application Gateway aangepaste regels'
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: sample
ms.date: 09/30/2019
ms.author: victorh
ms.openlocfilehash: 950f71c284268a9aa2773eb57213e266622d85bd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73501573"
---
# <a name="create-waf-custom-rules-with-azure-powershell"></a>Aangepaste WAF-regels maken met Azure PowerShell

Met dit script maakt u een Application Gateway Web Application firewall waarin aangepaste regels worden gebruikt. De aangepaste regel blokkeert het verkeer als de aanvraag header de *evilbot*van de gebruikers agent bevat.

## <a name="prerequisites"></a>Vereisten

### <a name="azure-powershell-module"></a>Azure PowerShell-module

Als u ervoor kiest om Azure PowerShell lokaal te installeren en te gebruiken, is voor dit script de Azure PowerShell module versie 2.1.0 of hoger vereist.

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
| [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | Hiermee maakt u de configuratie die een subnet koppelt aan de toepassingsgateway. |
| [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | Hiermee maakt u de configuratie die een openbaar IP-adres toewijst aan de toepassingsgateway. |
| [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | Hiermee wijst u een poort toe die moet worden gebruikt voor toegang tot de toepassingsgateway. |
| [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | Hiermee maakt u een back-endpool voor een toepassingsgateway. |
| [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | Hiermee configureert u instellingen voor een back-endpool. |
| [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | Hiermee maakt u een listener. |
| [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | Hiermee maakt u een routeringsregel. |
| [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | Geef de laag en capaciteit voor een toepassingsgateway op. |
| [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | Hiermee maakt u een toepassingsgateway. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep en alle daarin opgenomen resources. |
|[New-AzApplicationGatewayAutoscaleConfiguration](/powershell/module/az.network/New-AzApplicationGatewayAutoscaleConfiguration)|Hiermee maakt u een configuratie voor automatisch schalen voor de Application Gateway.|
|[New-AzApplicationGatewayFirewallMatchVariable](/powershell/module/az.network/New-AzApplicationGatewayFirewallMatchVariable)|Hiermee maakt u een overeenkomende variabele voor de firewall voorwaarde.|
|[New-AzApplicationGatewayFirewallCondition](/powershell/module/az.network/New-AzApplicationGatewayFirewallCondition)|Hiermee maakt u een match-voor waarde voor aangepaste regel.|
|[New-AzApplicationGatewayFirewallCustomRule](/powershell/module/az.network/New-AzApplicationGatewayFirewallCustomRule)|Hiermee maakt u een nieuwe aangepaste regel voor het firewall beleid van Application Gateway.|
|[New-AzApplicationGatewayFirewallPolicy](/powershell/module/az.network/New-AzApplicationGatewayFirewallPolicy)|Hiermee maakt u een Application gateway-firewall beleid.|
|[New-AzApplicationGatewayWebApplicationFirewallConfiguration](/powershell/module/az.network/New-AzApplicationGatewayWebApplicationFirewallConfiguration)|Hiermee maakt u een WAF-configuratie voor een toepassings gateway.|

## <a name="next-steps"></a>Volgende stappen

- Zie [aangepaste regels voor Web Application firewall](../ag/custom-waf-rules-overview.md) voor meer informatie over aangepaste regels voor WAF.
- Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/overview).
