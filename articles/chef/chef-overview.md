---
title: Chef gebruiken met Azure
description: Inleiding tot het gebruik van chef voor het configureren en testen van uw Azure-infra structuur
keywords: Azure, chef, devops, virtual machines, overzicht, automatiseren
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 4ad8b79b42c9d8d7942f391223c052f63579b11b
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158003"
---
# <a name="using-chef-with-azure"></a>Chef gebruiken met Azure
[Chef](https://www.chef.io) is een krachtig automatiserings platform dat de infra structuur van virtuele machines op Azure transformeert in code. Chef automatiseert de manier waarop de infra structuur wordt geconfigureerd, geïmplementeerd en beheerd in uw netwerk, ongeacht de grootte.

In dit artikel worden de voor delen beschreven van het gebruik van chef voor het beheren van Azure-infra structuur.

## <a name="chef-extension-on-azure"></a>Chef-extensie op Azure
Richt een virtuele machine in met chef-client die als een achtergrond service wordt uitgevoerd met de [extensie chef](https://docs.microsoft.com/azure/chef/chef-extension-portal) in azure Portal. Zodra de virtuele machines zijn ingericht, zijn ze klaar om te worden beheerd door een chef-server.

## <a name="chef-cloud-shell"></a>Chef Cloud Shell
Gebruik chef-werk station rechtstreeks in Azure Cloud Shell! Voer al uw chef-hulpprogram ma's en inspecies uit Cloud Shell. U kunt de chef-opdrachten gebruiken van:

* [chef](https://docs.chef.io/ctl_chef.html)
* [keuken](https://docs.chef.io/ctl_kitchen.html)
* [inspec](https://www.inspec.io/docs/reference/cli/)
* [gereedschap](https://docs.chef.io/knife.html)
* [cookstyle](https://docs.chef.io/cookstyle.html)
* [foodcritic](https://docs.chef.io/foodcritic.html)
* [chef-run](https://www.chef.sh/docs/chef-workstation/getting-started/)

Combi neer onze opdracht hulpprogramma's met de andere hulpprogram ma's die beschikbaar zijn in Cloud Shell, zoals `git`, `az-cli`en `terraform`, en schrijf uw infra structuur en compatibiliteits automatisering vanuit de browser.

## <a name="automate-infrastructure-apps-and-compliance-with-one-platform"></a>Infra structuur, apps en naleving automatiseren met één platform
Bedrijven hebben snelheid, snelheid en veiligheid nodig om in de digitale Marketplace te concurreren. Chef en micro soft helpen individuen, teams en ondernemingen al deze dingen uit te voeren. Met één platform, chef automatiseren, kunt u uw infra structuur, toepassingen en naleving van uw micro soft-account automatiseren en continu bezorgen.

## <a name="test-drive-chef-automate-on-azure"></a>Chef automatisch op Azure testen
Met de chef-oplossing voor het [automatiseren van Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/chef-software.chef-automate) kunt u met chef de infra structuur en toepassingen gezamenlijk bouwen, implementeren en beheren. Met één klik krijgt u direct toegang tot alle commerciële functies die zijn opgenomen in chef-automatisering; Profiteer van end-to-end zicht baarheid in uw gehele vloot, schakel voortdurende naleving in en beheer alle wijzigingen met een uniforme werk stroom.

## <a name="next-steps"></a>Volgende stappen

* [Een virtuele Windows-machine maken in azure met behulp van chef](/azure/virtual-machines/windows/chef-automation)
