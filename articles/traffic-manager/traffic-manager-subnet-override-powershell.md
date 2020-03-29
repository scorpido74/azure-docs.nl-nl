---
title: Subnet overschrijven azure traffic manager met Azure PowerShell | Microsoft Documenten
description: In dit artikel u begrijpen hoe subnetoverschrijving van Traffic Manager wordt gebruikt om de routeringsmethode van een Traffic Manager-profiel te overschrijven om verkeer naar een eindpunt te leiden op basis van het IP-adres van de eindgebruiker via een vooraf gedefinieerd IP-bereik naar eindpunttoewijzingen met Azure Powershell.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: rohink
ms.openlocfilehash: 323093ec78a9486d19496b0ee90e37cb42eea341
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938421"
---
# <a name="traffic-manager-subnet-override-using-azure-powershell"></a>Subnet overschrijven van traffic manager met Azure Powershell

Met subnetoverschrijven van Traffic Manager u de routeringsmethode van een profiel wijzigen.  De toevoeging van een overschrijving leidt het verkeer op basis van het IP-adres van de eindgebruiker met een vooraf gedefinieerd IP-bereik naar endpoint mapping. 

## <a name="how-subnet-override-works"></a>Hoe subnetoverschrijving werkt

Wanneer subnetoverschrijvingen worden toegevoegd aan een profiel van verkeersbeheerder, controleert Traffic Manager eerst of er een subnetoverschrijving is voor het IP-adres van de eindgebruiker. Als er een wordt gevonden, wordt de DNS-query van de gebruiker naar het bijbehorende eindpunt geleid.  Als er geen toewijzing wordt gevonden, valt Traffic Manager terug op de oorspronkelijke routeringsmethode van het profiel. 

De IP-adresbereiken kunnen worden opgegeven als CIDR-bereiken (bijvoorbeeld 1.2.3.0/24) of als adresbereiken (bijvoorbeeld 1.2.3.4-5.6.7.8). De IP-bereiken die aan elk eindpunt zijn gekoppeld, moeten uniek zijn voor dat eindpunt. Elke overlapping van IP-bereiken tussen verschillende eindpunten zorgt ervoor dat het profiel wordt geweigerd door Traffic Manager.

Er zijn twee soorten routeringsprofielen die subnetoverschrijvingen ondersteunen:

* **Geografisch** - Als Traffic Manager een subnetoverschrijving voor het IP-adres van de DNS-query vindt, wordt de query doorgerouteerd naar het eindpunt, ongeacht de status van het eindpunt.
* **Prestaties** - Als Traffic Manager een subnetoverschrijving voor het IP-adres van de DNS-query vindt, wordt het verkeer alleen doorsturen naar het eindpunt als het in orde is.  Traffic Manager valt terug naar de performance routing heuristisch als het eindpunt voor subnetoverride niet in orde is.

## <a name="create-a-traffic-manager-subnet-override"></a>Een subnet overschrijven van een verkeersbeheer-subnet maken

Als u een subnetoverschrijving voor Traffic Manager wilt maken, u Azure PowerShell gebruiken om de subnetten voor de overschrijving toe te voegen aan het eindpunt Traffic Manager.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U de opdrachten uitvoeren die volgen in de [Azure Cloud Shell](https://shell.azure.com/powershell)of door PowerShell vanaf uw computer uit te voeren. De Azure Cloud Shell is een gratis interactieve shell. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Als u PowerShell vanaf uw computer uitvoert, hebt u de Azure PowerShell-module, 1.0.0 of hoger, nodig. U kunt `Get-Module -ListAvailable Az` uitvoeren om de geïnstalleerde versie te vinden. Als u PowerShell wilt installeren of upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet `Login-AzAccount` u ook uitvoeren om u aan te melden bij Azure.


1. **Haal het eindpunt van Traffic Manager op:**

    Als u het subnet overschrijven wilt inschakelen, haalt u het eindpunt op waaraan u de overschrijving wilt toevoegen en slaat u het op in een variabele met [Get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0).

    Vervang de naam, profielnaam en resourcegroepnaam door de waarden van het eindpunt dat u wijzigt.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Voeg het IP-adresbereik toe aan het eindpunt:**
    
    Als u het IP-adresbereik aan het eindpunt wilt toevoegen, gebruikt u [Add-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange?view=azps-2.5.0&viewFallbackFrom=azps-2.4.0) om het bereik toe te voegen.

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27
 
    ```
    Zodra de bereiken zijn toegevoegd, gebruikt u [Set-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) om het eindpunt bij te werken.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```
Verwijdering van het IP-adresbereik kan worden voltooid met [Remove-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/remove-aztrafficmanageripaddressrange?view=azps-2.5.0).

1.  **Haal het eindpunt van Traffic Manager op:**

    Als u het subnet overschrijven wilt inschakelen, haalt u het eindpunt op waaraan u de overschrijving wilt toevoegen en slaat u het op in een variabele met [Get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0).

    Vervang de naam, profielnaam en resourcegroepnaam door de waarden van het eindpunt dat u wijzigt.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Verwijder het IP-adresbereik van het eindpunt:**

    ```powershell
    
    ### Remove a range of IPs ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Remove a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Remove a range of IPs with a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27

    ```
     Zodra de bereiken zijn verwijderd, gebruikt u [Set-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) om het eindpunt bij te werken.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [verkeersrouteringsmethoden voor](traffic-manager-routing-methods.md)Traffic Manager .

Meer informatie over de [methode voor het routeren van subnetverkeer](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)
