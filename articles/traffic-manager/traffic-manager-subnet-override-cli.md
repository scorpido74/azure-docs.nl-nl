---
title: Subnet overschrijven azure traffic manager met Azure CLI | Microsoft Documenten
description: In dit artikel u begrijpen hoe subnetoverschrijving en overschrijven van Traffic Manager kan worden gebruikt om de routeringsmethode van een Traffic Manager-profiel te overschrijven om verkeer naar een eindpunt te leiden op basis van het IP-adres van de eindgebruiker via vooraf gedefinieerde IP-bereik naar eindpunttoewijzingen.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: rohink
ms.openlocfilehash: 818b692884bd9d31efd08663a582ebcfec2032e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938476"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Subnet overschrijven van traffic manager met Azure CLI

Met subnetoverschrijven van Traffic Manager u de routeringsmethode van een profiel wijzigen.  De toevoeging van een overschrijving leidt het verkeer op basis van het IP-adres van de eindgebruiker met een vooraf gedefinieerd IP-bereik naar endpoint mapping. 

## <a name="how-subnet-override-works"></a>Hoe subnetoverschrijving werkt

Wanneer subnetoverschrijvingen worden toegevoegd aan een profiel van verkeersbeheerder, controleert Traffic Manager eerst of er een subnetoverschrijving is voor het IP-adres van de eindgebruiker. Als er een wordt gevonden, wordt de DNS-query van de gebruiker naar het bijbehorende eindpunt geleid.  Als er geen toewijzing wordt gevonden, valt Traffic Manager terug op de oorspronkelijke routeringsmethode van het profiel. 

De IP-adresbereiken kunnen worden opgegeven als CIDR-bereiken (bijvoorbeeld 1.2.3.0/24) of als adresbereiken (bijvoorbeeld 1.2.3.4-5.6.7.8). De IP-bereiken die aan elk eindpunt zijn gekoppeld, moeten uniek zijn voor dat eindpunt. Elke overlapping van IP-bereiken tussen verschillende eindpunten zorgt ervoor dat het profiel wordt geweigerd door Traffic Manager.

Er zijn twee soorten routeringsprofielen die subnetoverschrijvingen ondersteunen:

* **Geografisch** - Als Traffic Manager een subnetoverschrijving voor het IP-adres van de DNS-query vindt, wordt de query doorgerouteerd naar het eindpunt, ongeacht de status van het eindpunt.
* **Prestaties** - Als Traffic Manager een subnetoverschrijving voor het IP-adres van de DNS-query vindt, wordt het verkeer alleen doorsturen naar het eindpunt als het in orde is.  Traffic Manager valt terug naar de performance routing heuristisch als het eindpunt voor subnetoverride niet in orde is.

## <a name="create-a-traffic-manager-subnet-override"></a>Een subnet overschrijven van een verkeersbeheer-subnet maken

Als u een subnetoverschrijving voor Traffic Manager wilt maken, u Azure CLI gebruiken om de subnetten voor de overschrijving toe te voegen aan het eindpunt Traffic Manager.

## <a name="azure-cli"></a>Azure-CLI

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze zelfstudie versie 2.0.28 of hoger van Azure CLI uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli).

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Werk het eindpunt Van Traffic Manager bij met subnetoverschrijving.
Gebruik Azure CLI om uw eindpunt bij te werken met de update van het [EINDPUNT van AZ-netwerkverkeer.](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update)

```azurecli

### Add a range of IPs ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 1.2.3.4-5.6.7.8 \
    --type AzureEndpoints

### Add a subnet ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 9.10.11.0:24 \
    --type AzureEndpoints

```

U de IP-adresbereiken verwijderen door de eindpuntupdate voor het [AZ-netwerkverkeer-beheer](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update) uit te voeren met de optie **-verwijderen.**

```azurecli

az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints

```
## <a name="next-steps"></a>Volgende stappen
Meer informatie over [verkeersrouteringsmethoden voor](traffic-manager-routing-methods.md)Traffic Manager .

Meer informatie over de [methode voor het routeren van subnetverkeer](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)