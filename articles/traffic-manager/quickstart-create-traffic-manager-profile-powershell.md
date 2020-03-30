---
title: Snelstart:Een profiel maken voor een hoge beschikbaarheid van toepassingen - Azure PowerShell - Azure Traffic Manager
description: In dit quickstart-artikel wordt beschreven hoe u een Traffic Manager-profiel maakt om een zeer beschikbare webtoepassing te maken.
services: traffic-manager
author: rohinkoul
mnager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2019
ms.author: rohink
ms.openlocfilehash: 0ab7392b4fa6e248d51392706fedaed156344a99
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76934819"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>Snelstart: een Traffic Manager-profiel maken voor een zeer beschikbare webtoepassing met Azure PowerShell

In deze quickstart wordt beschreven hoe u een Traffic Manager-profiel maakt die hoge beschikbaarheid van uw webtoepassing biedt.

In deze snelstart maakt u twee exemplaren van een webtoepassing. Ze worden elk in een andere Azure-regio uitgevoerd. U maakt een Traffic Manager-profiel op basis van [eindpuntprioriteit](traffic-manager-routing-methods.md#priority-traffic-routing-method). het profiel stuurt gebruikersverkeer door naar de primaire site waar de webtoepassing wordt uitgevoerd. Traffic Manager bewaakt de webtoepassing continu. Als de primaire site niet beschikbaar is, biedt Traffic Manager automatische failover voor de back-upsite.

Als u nog geen abonnement op Azure hebt, maak dan nu een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u PowerShell lokaal wilt installeren en gebruiken, is voor dit artikel versie 5.4.1 of hoger van de Azure PowerShell-module vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="create-a-resource-group"></a>Een resourcegroep maken
Maak een resourcegroep met [Nieuwe AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

```azurepowershell-interactive


# Variables
$Location1="WestUS"

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location $Location1
```

## <a name="create-a-traffic-manager-profile"></a>Een Traffic Manager-profiel maken

Maak een Traffic [Manager-profiel met Nieuw-AzTrafficManagerProfiel](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) dat gebruikersverkeer aanstuurt op basis van de prioriteit van eindpunten.

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

## <a name="create-web-apps"></a>Web-apps maken

Voor deze quickstart hebt u twee exemplaren nodig van een webtoepassing die is geïmplementeerd in twee verschillende Azure-regio's *(West US* en *East US).* Elk exemplaar dient als primair en failover-eindpunt voor Traffic Manager.

### <a name="create-web-app-service-plans"></a>Web App-serviceplannen maken
Web App-serviceplannen maken met [Nieuw-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) voor de twee exemplaren van de webtoepassing die u in twee verschillende Azure-regio's implementeert.

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
### <a name="create-a-web-app-in-the-app-service-plan"></a>Een webapp maken in het App-serviceplan
Maak twee exemplaren van de webtoepassing met [Nieuw-AzWebApp](/powershell/module/az.websites/new-azwebapp) in de App Service-abonnementen in de Azure-regio's *West-VS* en *Oost-VS.*

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name $App1Name -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "$App1Name-Plan").Id
$App2ResourceId=(New-AzWebApp -Name $App2Name -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "$App2Name-Plan").Id

```

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-eindpunten toevoegen
Voeg de twee Web Apps als Eindpunt voor Verkeersbeheer toe met [Nieuw-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) als volgt aan het profiel Traffic Manager:
- Voeg de web-app toe in de *Azure-regio West US* als het primaire eindpunt om al het gebruikersverkeer te routeren. 
- Voeg de web-app in de *Azure-regio Oost-VS* toe als het failovereindpunt. Als het primaire eindpunt niet beschikbaar is, wordt het verkeer automatisch naar het failover-eindpunt gerouteerd.

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

Bepaal de DNS-naam van het Traffic [Manager-profiel met Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile).

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

Kopieer de waarde **Relatieve DnsName.** De DNS-naam van uw Traffic Manager-profiel is *http://<* relativednsname *>.trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Traffic Manager in werking zien
1. Voer in een webbrowser de DNS-naam van uw Traffic Manager-profiel (*http://<* relativednsname *>.trafficmanager.net)* in om de standaardwebsite van uw web-app te bekijken.

    > [!NOTE]
    > In dit quickstartscenario worden alle aanvragen gerouteerd naar het primaire eindpunt. Het is ingesteld op **Priority 1**.
2. Als u traffic manager-failover in actie wilt weergeven, schakelt u uw primaire site uit met [Disable-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint).

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name $App1Name-$Location1 `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. Kopieer de DNS-naam van uw Traffic Manager-profiel (*http://<* relativednsname *>.trafficmanager.net)* om de website in een nieuwe webbrowsersessie te bekijken.
4. Controleer of de web-app nog beschikbaar is.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent, verwijdert u de brongroepen, webtoepassingen en alle gerelateerde bronnen met [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Traffic Manager-profiel gemaakt dat hoge beschikbaarheid biedt voor uw webtoepassing. Voor meer informatie over het routeren van verkeer gaat u door naar de zelfstudies voor Traffic Manager.

> [!div class="nextstepaction"]
> [Traffic Manager tutorials](tutorial-traffic-manager-improve-website-response.md) (Traffic Manager-zelfstudies)
