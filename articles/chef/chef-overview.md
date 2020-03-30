---
title: Chef gebruiken met Azure
description: Inleiding tot het gebruik van Chef om uw Azure-infrastructuur te configureren en te testen
keywords: azure, chef-kok, devops, virtuele machines, overzicht, automatiseren
ms.date: 02/22/2020
ms.topic: article
ms.openlocfilehash: c22faa69bf8f42111d328a9c156dc1a2432731b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586339"
---
# <a name="using-chef-with-azure"></a>Chef gebruiken met Azure
[Chef](https://www.chef.io) is een krachtig automatiseringsplatform dat de infrastructuur van virtuele machines op Azure omzet in code. Chef automatiseert hoe de infrastructuur wordt geconfigureerd, geïmplementeerd en beheerd in uw netwerk, ongeacht de grootte.

In dit artikel worden de voordelen beschreven van het gebruik van Chef om de Azure-infrastructuur te beheren.

## <a name="chef-extension-on-azure"></a>Chef-extensie op Azure
Inrichten van een virtuele machine met Chef-client die wordt uitgevoerd als achtergrondservice met de [chef-extensie](https://docs.microsoft.com/azure/chef/chef-extension-portal) op de [Azure-portal.](https://go.microsoft.com/fwlink/p/?LinkID=525040) Eenmaal ingericht, zijn deze virtuele machines klaar om te worden beheerd door een Chef-server.

## <a name="chef-cloud-shell"></a>Chef Cloud Shell
Gebruik Chef Workstation rechtstreeks in Azure Cloud Shell! Voer al uw chef-hulpprogramma's en InSpec uit vanuit Cloud Shell. U gebruik maken van de Chef commando's van:

* [Chef](https://docs.chef.io/ctl_chef.html)
* [Keuken](https://docs.chef.io/ctl_kitchen.html)
* [Inspec](https://www.inspec.io/docs/reference/cli/)
* [Mes](https://docs.chef.io/knife.html)
* [kookstijl](https://docs.chef.io/cookstyle.html)
* [chef-kok-looppas](https://www.chef.sh/docs/chef-workstation/getting-started/)

Combineer onze opdrachthulpprogramma's met de andere tools `git` `az-cli`die `terraform`beschikbaar zijn in Cloud Shell, zoals , en , en schrijf uw infrastructuur en compliance automatisering vanuit de browser.

## <a name="automate-infrastructure-apps-and-compliance-with-one-platform"></a>Automatiseer infrastructuur, apps en naleving van één platform
Bedrijven hebben snelheid, snelheid en veiligheid nodig om te kunnen concurreren op de digitale markt. Samen helpen Chef en Microsoft individuen, teams en ondernemingen om al deze dingen te bereiken. Met één platform, Chef Automate, u nu uw infrastructuur, applicaties en compliance op uw Microsoft-landgoed automatiseren en continu leveren.

## <a name="test-drive-chef-automate-on-azure"></a>Teststation Chef Automatiser op Azure
Met de Azure [Marketplace-oplossing](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/chef-software.chef-automate) voor Chef Automate, die wordt ondersteund door Chef, u uw infrastructuur en toepassingen gezamenlijk bouwen, implementeren en beheren. Met één klik krijgt u direct toegang tot alle commerciële functies die bij Chef Automate zijn inbegrepen; krijg end-to-end zichtbaarheid in uw hele wagenpark, maak continue compliance mogelijk en beheer alle wijzigingen met een uniforme workflow.

## <a name="next-steps"></a>Volgende stappen

* [Een virtuele Windows-machine maken op Azure met Chef](chef-automation.md)
