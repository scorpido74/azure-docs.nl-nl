---
title: HTTP-headers opnieuw schrijven in Azure Application Gateway
description: In dit artikel vindt u informatie over het herschrijven van HTTP-headers in Azure Application Gateway met Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 47fe6a5247622e3ad3b3720955068580e0329913
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "64947193"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>HTTP-aanvraag- en antwoordkoppen opnieuw schrijven met Azure Application Gateway - Azure PowerShell

In dit artikel wordt beschreven hoe u Azure PowerShell gebruiken om een [V2 SKU-instantie van de Toepassingsgateway](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) te configureren om de HTTP-headers in aanvragen en antwoorden te herschrijven.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

- U moet Azure PowerShell lokaal uitvoeren om de stappen in dit artikel uit te voeren. U moet ook Az-module versie 1.0.0 of hoger hebben geïnstalleerd. Voer `Import-Module Az` uit `Get-Module Az` en bepaal vervolgens de versie die u hebt geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps). Nadat u de versie van PowerShell hebt gecontroleerd, voert u `Login-AzAccount` uit om een verbinding op te zetten met Azure.
- U moet een Instantie Application Gateway v2 SKU hebben. Het herschrijven van kopteksten wordt niet ondersteund in de v1 SKU. Als u de v2 SKU niet hebt, maakt u een [Instantie Application Gateway v2 SKU](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) voordat u begint.

## <a name="create-required-objects"></a>Vereiste objecten maken

Als u http-headerherschrijven wilt configureren, moet u deze stappen uitvoeren.

1. Maak de objecten die nodig zijn voor het herschrijven van HTTP-koppen:

   - **RequestHeaderConfiguration:** Wordt gebruikt om de velden van de aanvraagkoptekst op te geven die u wilt herschrijven en de nieuwe waarde voor de kopteksten.

   - **ResponseHeaderConfiguration:** wordt gebruikt om de antwoordkopvelden op te geven die u wilt herschrijven en de nieuwe waarde voor de kopteksten.

   - **ActionSet:** bevat de configuraties van de eerder opgegeven aanvraag- en antwoordkoppen.

   - **Voorwaarde**: Een optionele configuratie. Voorwaarden herschrijven evalueert de inhoud van HTTP(S)-verzoeken en -antwoorden. De herschrijfactie vindt plaats als het HTTP(S)-verzoek of -antwoord overeenkomt met de herschrijfvoorwaarde.

     Als u meer dan één voorwaarde aan een actie koppelt, vindt de actie alleen plaats wanneer aan alle voorwaarden is voldaan. Met andere woorden, de bewerking is een logische EN operatie.

   - **HerschrijvenRegel:** Bevat meerdere herschrijfactie / herschrijven van conditiecombinaties.

   - **Regelvolgorde:** een optionele configuratie die helpt bij het bepalen van de volgorde waarin herschrijfregels worden uitgevoerd. Deze configuratie is handig wanneer u meerdere herschrijfregels in een herschrijfset hebt. Een herschrijfregel met een lagere regelvolgordewaarde wordt eerst uitgevoerd. Als u dezelfde regelvolgordewaarde toewijst aan twee herschrijfregels, is de uitvoeringsvolgorde niet-deterministisch.

     Als u de regelvolgorde niet expliciet opgeeft, wordt een standaardwaarde van 100 ingesteld.

   - **RewriteRuleSet**: Bevat meerdere herschrijfregels die worden gekoppeld aan een regel voor het routeren van aanvragen.

2. Voeg de rewriteRuleSet toe aan een routeringsregel. De herschrijfconfiguratie is via de routeringsregel gekoppeld aan de bronlistener. Wanneer u een basisrouteringsregel gebruikt, wordt de configuratie van de koptekst herschrijven gekoppeld aan een bronlistener en is een globale koptekst herschrijven. Wanneer u een routeringsregel op basis van paden gebruikt, wordt de configuratie van de koptekst herschrijven gedefinieerd op de URL-padkaart. In dat geval is het alleen van toepassing op het specifieke padgebied van een site.

U meerdere HTTP-koptekstsets opnieuw schrijven en elke herschrijfset toepassen op meerdere listeners. U echter slechts één herschrijvenset toepassen op een specifieke listener.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>De configuratie van de HTTP-koptekst opnieuw schrijven

In dit voorbeeld wijzigen we een omleidings-URL door de locatiekoptekst in het HTTP-antwoord te herschrijven wanneer de locatiekopeen verwijzing naar azurewebsites.net bevat. Hiervoor voegen we een voorwaarde toe om te evalueren of de locatiekoptekst in het antwoord azurewebsites.net bevat. We gebruiken het `(https?):\/\/.*azurewebsites\.net(.*)$`patroon. En we gebruiken `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` als de header waarde. Deze waarde vervangt *azurewebsites.net* door *contoso.com* in de locatiekoptekst.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>De configuratie van de toepassingsgateway ophalen

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>De configuratie van de regel voor het routeren van aanvragen ophalen

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>De toepassingsgateway bijwerken met de configuratie voor het herschrijven van HTTP-headers

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name LocationHeaderRewrite -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Een herschrijfregel verwijderen

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>Volgende stappen

Zie [veelvoorkomende scenario's voor het herschrijven van kopteksten voor](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)meer informatie over het instellen van een aantal veelvoorkomende use cases.