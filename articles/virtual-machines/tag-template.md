---
title: Een virtuele machine labelen met behulp van een sjabloon
description: Meer informatie over het coderen van een virtuele machine met behulp van een sjabloon.
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.author: cynthn
author: cynthn
ms.date: 10/26/2018
ms.openlocfilehash: d1acbe82a086574a102e7897bbd3b99683c1185e
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594942"
---
# <a name="tagging-a-vm-using-a-template"></a>Een virtuele machine coderen met behulp van een sjabloon


In dit artikel wordt beschreven hoe u een virtuele machine in azure kunt labelen met behulp van een resource manager-sjabloon. Tags zijn door de gebruiker gedefinieerde sleutel/waarde-paren die rechtstreeks kunnen worden geplaatst op een resource of resource groep. Azure ondersteunt momenteel Maxi maal 50 Tags per resource en resource groep. Labels kunnen worden geplaatst op een resource op het moment dat ze worden gemaakt of worden toegevoegd aan een bestaande resource.

Met [deze sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) worden tags op de volgende resources geplaatst: Compute (virtuele machine), opslag (opslag account) en netwerk (openbaar IP-adres, Virtual Network, en netwerk interface). Deze sjabloon is voor een Windows-VM, maar kan worden aangepast voor Linux-Vm's.

Klik op de knop **implementeren naar Azure** van de [sjabloon koppeling](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Hiermee gaat u naar de [Azure Portal](https://portal.azure.com/) waar u deze sjabloon kunt implementeren.

![Eenvoudige implementatie met Tags](./media/tag/deploy-to-azure-tags.png)

Deze sjabloon bevat de volgende Tags: *afdeling* , *toepassing* en *gemaakt door*. U kunt deze Tags rechtstreeks in de sjabloon toevoegen/bewerken als u andere label namen wilt.

![Azure Tags in een sjabloon](./media/tag/azure-tags-in-a-template.png)

Zoals u kunt zien, worden de tags gedefinieerd als sleutel-waardeparen, gescheiden door een dubbele punt (:). De labels moeten worden gedefinieerd in deze indeling:

```config
"tags": {
    "Key1" : "Value1",
    "Key2" : "Value2"
}
```

Sla het sjabloon bestand op nadat u klaar bent met het bewerken van de gewenste labels.

Vervolgens kunt u in het gedeelte **para meters bewerken** de waarden voor uw Tags invullen.

![Tags bewerken in Azure Portal](./media/tag/edit-tags-in-azure-portal.png)

Klik op **maken** om deze sjabloon te implementeren met de label waarden.


**Volgende stappen**

- Zie [Azure Resource Manager Overview](../azure-resource-manager/management/overview.md) en [Tags gebruiken om uw Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)voor meer informatie over het coderen van uw Azure-resources.
- Als u wilt zien hoe Tags u kunnen helpen bij het beheren van uw gebruik van Azure-resources, raadpleegt u [inzicht in uw Azure-factuur](../cost-management-billing/understand/review-individual-bill.md) en [krijgt u inzicht in uw Microsoft Azure Resource verbruik](../cost-management-billing/manage/usage-rate-card-overview.md).
