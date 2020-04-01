---
title: Een ASE v1 maken
description: Flow-beschrijving maken voor een app-serviceomgeving v1. Dit document is alleen bedoeld voor klanten die de verouderde v1 ASE gebruiken.
author: ccompy
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 89dc96370f65ff20d7f8be38ff78d6c1664305d3
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477793"
---
# <a name="how-to-create-an-app-service-environment-v1"></a>Een app-serviceomgeving v1 maken 

> [!NOTE]
> Dit artikel gaat over de App Service Environment v1. Er is een nieuwere versie van de App Service-omgeving die gemakkelijker te gebruiken is en draait op een krachtigere infrastructuur. Voor meer informatie over de nieuwe versie start u met de [inleiding tot de app-serviceomgeving.](intro.md)
> 

### <a name="overview"></a>Overzicht
De App Service Environment (ASE) is een Premium-serviceoptie van Azure App Service die een verbeterde configuratiemogelijkheid biedt die niet beschikbaar is in de stempels met meerdere tenants. De ASE-functie implementeert de Azure App Service in wezen in het virtuele netwerk van een klant. Lees de documentatie [Wat is een app-serviceomgeving][WhatisASE] om meer inzicht te krijgen in de mogelijkheden die appserviceomgeving biedt.

### <a name="before-you-create-your-ase"></a>Voordat u uw ASE maakt
Het is belangrijk om je bewust te zijn van de dingen die je niet veranderen. De aspecten die u niet wijzigen over uw ASE nadat deze is gemaakt, zijn:

* Locatie
* Abonnement
* Resourcegroep
* VNet gebruikt
* Subnet gebruikt 
* Subnetgrootte

Bij het kiezen van een VNet en het opgeven van een subnet, zorg ervoor dat het groot genoeg is om eventuele toekomstige groei tegemoet te komen. 

### <a name="creating-an-app-service-environment-v1"></a>Een app-serviceomgeving v1 maken
Als u een App Service-omgeving v1 wilt maken, u zoeken in de Azure Marketplace for ***App Service Environment v1***of een **resource** -> **Web + Mobile** -> **App Service Environment maken.** Een ASEv1 maken:

1. Geef de naam van uw ASE op. De naam die u voor de ASE opgeeft, wordt gebruikt voor de apps die in de ASE zijn gemaakt. Als de naam van de ASE appsvcenvdemo is, zou de subdomeinnaam zijn: *appsvcenvdemo.p.azurewebsites.net*. Als u dus een app met de naam *mytestapp*gemaakt, zou het adresseerbaar zijn op *mytestapp.appsvcenvdemo.p.azurewebsites.net*. U geen witruimte gebruiken in de naam van uw ASE. Als u hoofdletters in de naam gebruikt, is de domeinnaam de totale kleine versie van die naam. Als u een ILB gebruikt, wordt uw ASE-naam niet gebruikt in uw subdomein, maar wordt deze expliciet vermeld tijdens het maken van ASE.
   
    ![][1]
2. Selecteer uw abonnement. Het abonnement dat u voor uw ASE gebruikt, is ook van toepassing op alle apps die u in die ASE maakt. U uw ASE niet in een VNet plaatsen dat in een ander abonnement zit.
3. Een nieuwe resourcegroep selecteren of opgeven. De resourcegroep die voor uw ASE wordt gebruikt, moet dezelfde zijn die wordt gebruikt voor uw VNet. Als u een reeds bestaand VNet selecteert, wordt de selectie van de brongroep voor uw ASE bijgewerkt om die van uw VNet weer te geven.
   
    ![][2]
4. Maak uw selecties voor virtueel netwerk en locatie. U ervoor kiezen om een nieuwe VNet te maken of een reeds bestaande VNet te selecteren. Als u een nieuw VNet selecteert, u een naam en locatie opgeven. De nieuwe VNet heeft het adresbereik 192.168.250.0/23 en een subnet met de naam **standaard** dat wordt gedefinieerd als 192.168.250.0/24. U ook gewoon een reeds bestaande Classic of Resource Manager VNet selecteren. De VIP-typeselectie bepaalt of uw ASE rechtstreeks toegankelijk is vanaf het internet (Extern) of dat deze een Internal Load Balancer (ILB) gebruikt. Lees voor meer informatie over deze informatie [Een interne load balancer gebruiken met een app-serviceomgeving][ILBASE]. Als u een VIP-type Extern selecteert, u selecteren met hoeveel externe IP-adressen het systeem is gemaakt voor IP SSL-doeleinden. Als u Intern selecteert, moet u het subdomein opgeven dat uw ASE zal gebruiken. ATO's kunnen worden geïmplementeerd in virtuele netwerken die gebruik maken *van* openbare adresbereiken *of* RFC1918-adresruimten (d.w.z. privéadressen). Om een virtueel netwerk met een openbaar adresbereik te gebruiken, moet u het VNet van tevoren maken. Wanneer u een reeds bestaand VNet selecteert, moet u tijdens het maken van ASE een nieuw subnet maken. **U geen vooraf gemaakt subnet in de portal gebruiken. U een ASE maken met een reeds bestaand subnet als u uw ASE maakt met behulp van een resourcemanagersjabloon.** Als u een ASE wilt maken op basis van een sjabloon, gebruikt u de informatie hier, [Maakt u een app-serviceomgeving van sjabloon][ILBAseTemplate] en hier een [ILB-appserviceomgeving maken op basis van sjabloon][ASEfromTemplate].

### <a name="details"></a>Details
Een ASE is gemaakt met 2 front ends en 2 werknemers. De fronteinden fungeren als de HTTP/HTTPS-eindpunten en verzenden verkeer naar de werknemers die de rollen zijn die uw apps hosten. U het aantal na het maken van ASE aanpassen en zelfs automatische schaalregels instellen voor deze resourcegroepen. Voor meer informatie over handmatig schalen, beheer en bewaking van een App Service-omgeving vindt u hier: [Een App-serviceomgeving configureren][ASEConfig] 

Alleen de ene ASE kan bestaan in het subnet gebruikt door de ASE. Het subnet kan niet worden gebruikt voor iets anders dan de ASE

### <a name="after-app-service-environment-v1-creation"></a>Na app-serviceomgeving v1-creatie
Na ASE creation u het:

* Aantal frontends (minimum: 2)
* Aantal werknemers (minimum: 2)
* Aantal IP-adressen beschikbaar voor IP SSL
* Berekeningsresourcegroottes die worden gebruikt door de fronteinden of werknemers (front-end minimumgrootte is P2)

Hier vindt u meer informatie over handmatigschalen, beheer en bewaking van app-serviceomgevingen: [Een App-serviceomgeving configureren][ASEConfig] 

Voor informatie over automatisch schalen vindt u hier een handleiding: [Hoe configureer je automatisch schalen voor een App Service-omgeving][ASEAutoscale]

Er zijn extra afhankelijkheden die niet beschikbaar zijn voor aanpassing, zoals de database en opslag. Deze worden behandeld door Azure en worden geleverd met het systeem. De systeemopslag ondersteunt maximaal 500 GB voor de gehele App Service-omgeving en de database wordt naar behoefte door Azure aangepast door de schaal van het systeem.

## <a name="getting-started"></a>Aan de slag
Zie [Inleiding tot de app-serviceomgeving v1][WhatisASE] om aan de slag te gaan met App Service Environment v1

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
[ILBAseTemplate]: https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
