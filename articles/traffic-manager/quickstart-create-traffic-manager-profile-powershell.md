---
title: 'Quick Start: een profiel maken voor een hoge Beschik baarheid van toepassingen-Azure PowerShell-Azure Traffic Manager'
description: In dit Quick Start-artikel wordt beschreven hoe u een Traffic Manager profiel maakt om een Maxi maal beschik bare webtoepassing te bouwen.
services: traffic-manager
author: asudbring
mnager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2019
ms.author: allensu
ms.openlocfilehash: 0b1a0040c3cf6d517b19445be689dcc786334325
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038856"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>Snelstartgids: een Traffic Manager profiel maken voor een Maxi maal beschik bare webtoepassing met behulp van Azure PowerShell

In deze quickstart wordt beschreven hoe u een Traffic Manager-profiel maakt die hoge beschikbaarheid van uw webtoepassing biedt.

In deze Quick Start maakt u twee exemplaren van een webtoepassing. Ze worden elk in een andere Azure-regio uitgevoerd. U maakt een Traffic Manager-profiel op basis van [eindpuntprioriteit](traffic-manager-routing-methods.md#priority). het profiel stuurt gebruikersverkeer door naar de primaire site waar de webtoepassing wordt uitgevoerd. Traffic Manager bewaakt de webtoepassing continu. Als de primaire site niet beschikbaar is, biedt Traffic Manager automatische failover voor de back-upsite.

Als u nog geen abonnement op Azure hebt, maak dan nu een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u PowerShell lokaal wilt installeren en gebruiken, is voor dit artikel versie 5.4.1 of hoger van de Azure PowerShell-module vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="create-a-resource-group"></a>Een resourcegroep maken
Maak een resource groep met behulp van [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

```azurepowershell-interactive


# Variables
$Location1="WestUS"

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location $Location1
```

## <a name="create-a-traffic-manager-profile"></a>Een Traffic Manager-profiel maken

Maak een Traffic Manager profiel met behulp van [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) waarmee gebruikers verkeer wordt omgeleid op basis van de eindpunt prioriteit.

```azurepowershell-interactive

# Generates a random value
$Random=(New-Guid).ToString().Substring(0,8)
$mytrafficmanagerprofile="mytrafficmanagerprofile$Random"

New-AzTrafficManagerProfile `
-Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup `
-TrafficRoutingMethod Priority `
-MonitorPath '/' `
-MonitorProtocol "HTTP" `
-RelativeDnsName $mytrafficmanagerprofile `
-Ttl 30 `
-MonitorPort 80
```

## <a name="create-web-apps"></a>Web Apps maken

Voor deze Quick Start hebt u twee exemplaren van een webtoepassing nodig die in twee verschillende Azure-regio's zijn geïmplementeerd (VS-*West* en *VS-Oost*). Elk exemplaar dient als primair en failover-eindpunt voor Traffic Manager.

### <a name="create-web-app-service-plans"></a>Web App Service-abonnementen maken
Maak Web app service-plannen met behulp van [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) voor de twee exemplaren van de webtoepassing die u in twee verschillende Azure-regio's gaat implementeren.

```azurepowershell-interactive

# Variables
$App1Name="AppServiceTM1$Random"
$App2Name="AppServiceTM2$Random"
$Location1="WestUS"
$Location2="EastUS"

# Create an App service plan
New-AzAppservicePlan -Name "$App1Name-Plan" -ResourceGroupName MyResourceGroup -Location $Location1 -Tier Standard
New-AzAppservicePlan -Name "$App2Name-Plan" -ResourceGroupName MyResourceGroup -Location $Location2 -Tier Standard

```
### <a name="create-a-web-app-in-the-app-service-plan"></a>Een web-app maken in het App Service plan
Maak twee exemplaren van de webtoepassing met behulp van [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) in de app service-abonnementen in de Azure-regio *VS-West* en *VS-Oost* .

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name $App1Name -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "$App1Name-Plan").Id
$App2ResourceId=(New-AzWebApp -Name $App2Name -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "$App2Name-Plan").Id

```

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-eindpunten toevoegen
Voeg de twee Web Apps als Traffic Manager eind punten met behulp van [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) toe aan het Traffic Manager profiel als volgt:
- Voeg de web-app die zich bevindt in de Azure-regio *West* , toe als het primaire eind punt om alle gebruikers verkeer te routeren. 
- Voeg de web-app die zich bevindt in de regio *VS-Oost* Azure als het eind punt voor de failover toe. Als het primaire eindpunt niet beschikbaar is, wordt het verkeer automatisch naar het failover-eindpunt gerouteerd.

```azurepowershell-interactive
New-AzTrafficManagerEndpoint -Name "$App1Name-$Location1" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App1ResourceId `
-EndpointStatus "Enabled"

New-AzTrafficManagerEndpoint -Name "$App2Name-$Location2" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App2ResourceId `
-EndpointStatus "Enabled"
```

## <a name="test-traffic-manager-profile"></a>Traffic Manager-profiel testen

In deze sectie controleert u de domeinnaam van uw Traffic Manager-profiel. Tevens configureert u het primaire eindpunt zodanig dat het niet beschikbaar is. Ten slotte kunt u zien dat de web-app nog steeds beschikbaar is. Dat komt omdat Traffic Manager het verkeer naar het failover-eindpunt stuurt.

### <a name="determine-the-dns-name"></a>DNS-naam bepalen

Bepaal de DNS-naam van het Traffic Manager profiel met [Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile).

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

Kopieer de waarde **RelativeDnsName** . De DNS-naam van uw Traffic Manager profiel is *http://<* relativednsname *>. de. net*. 

### <a name="view-traffic-manager-in-action"></a>Traffic Manager in werking zien
1. Voer in een webbrowser de DNS-naam van uw Traffic Manager profiel (*http://<* relativednsname *>. de. net*) in om de standaard website van uw web-app weer te geven.

    > [!NOTE]
    > In dit quickstartscenario worden alle aanvragen gerouteerd naar het primaire eindpunt. Het is ingesteld op **Priority 1**.
2. Als u Traffic Manager failover in actie wilt weer geven, schakelt u de primaire site uit met [Disable-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint).

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name $App1Name-$Location1 `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. Kopieer de DNS-naam van uw Traffic Manager profiel (*http://<* relativednsname *>. de. net*) om de website in een nieuwe webbrowser sessie weer te geven.
4. Controleer of de web-app nog beschikbaar is.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent, verwijdert u de resource groepen, webtoepassingen en alle gerelateerde resources met [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een Traffic Manager profiel gemaakt dat hoge Beschik baarheid biedt voor uw webtoepassing. Voor meer informatie over het routeren van verkeer gaat u door naar de zelfstudies voor Traffic Manager.

> [!div class="nextstepaction"]
> [Traffic Manager tutorials](tutorial-traffic-manager-improve-website-response.md) (Traffic Manager-zelfstudies)