---
title: Terraform gebruiken met Azure
description: Inleiding tot het gebruik van Terraform om de Azure-infrastructuur te versioneren en te implementeren.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: overview
ms.date: 10/26/2019
ms.openlocfilehash: 1c6ac9b67f556b039b9ffd5ed725ea1f24aeeb3a
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969250"
---
# <a name="terraform-with-azure"></a>Terraform met Azure

[Hashicorp Terraform](https://www.terraform.io/) is een open-source-hulpprogramma voor het inrichten en beheren van cloudinfrastructuur. Er wordt een infra structuur in configuratie bestanden die de topologie van Cloud bronnen beschrijven beschreven. Deze resources omvatten virtuele machines, opslag accounts en netwerk interfaces. De terraform CLI biedt een eenvoudig mechanisme voor het implementeren en de versie van de configuratie bestanden naar Azure.

In dit artikel worden de voordelen beschreven van het gebruik van Terraform voor het beheer van de Azure-infrastructuur.

## <a name="automate-infrastructure-management"></a>Automatiseer infrastructuurbeheer.

Met de op sjablonen gebaseerde configuratiebestanden van Terraform kunt u Azure-bronnen op een herhaalbare en voorspelbare manier definiëren, inrichten en configureren. Het automatiseren van infrastructuur heeft verschillende voordelen:

- Het verkleint de kans op menselijke fouten bij het inzetten en beheren van infrastructuur.
- Dezelfde sjabloon wordt meerdere keren geïmplementeerd om identieke ontwikkel-, test- en productieomgevingen te maken.
- Reduceert de kosten van ontwikkel- en testomgevingen door ze op aanvraag te maken.

## <a name="understand-infrastructure-changes-before-being-applied"></a>Wijzigingen in de infra structuur begrijpen voordat u deze toepast

Als een resourcetopologie complex wordt, kan het moeilijk zijn om de betekenis en impact van wijzigingen in de infrastructuur te begrijpen.

Met de terraform CLI kunnen gebruikers wijzigingen in de infra structuur valideren en bekijken vóór de toepassing. Het bekijken van een voor beeld van infrastructuur wijzigingen op een veilige manier heeft verschillende voor delen:
- Teamleden kunnen effectiever samenwerken door voorgestelde wijzigingen en hun impact snel te begrijpen.
- Onbedoelde veranderingen kunnen vroeg in het ontwikkelingsproces worden opgevangen

## <a name="deploy-infrastructure-to-multiple-clouds"></a>Infrastructuur implementeren in meerdere clouds

Terraform is vertrouwd bij het implementeren van een infra structuur over meerdere cloud providers. Hierdoor kunnen ontwikkel aars consistente hulp middelen gebruiken om elke infrastructuur definitie te beheren.

## <a name="next-steps"></a>Volgende stappen

Nu u een overzicht van Terraform en de voordelen daarvan hebt, zijn dit mogelijke volgende stappen:

- Ga aan de slag door [Terraform te installeren en te configureren om Azure te gebruiken](/azure/virtual-machines/linux/terraform-install-configure).
- [Een virtuele machine maken in Azure met behulp van Terraform](/azure/virtual-machines/linux/terraform-create-complete-vm)
- De [Azure Resource Manager-module voor Terraform](https://www.terraform.io/docs/providers/azurerm/) verkennen 