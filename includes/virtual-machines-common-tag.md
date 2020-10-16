---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 196dfdc045fd60e4a253857087177f478f50ea24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86050335"
---
## <a name="tagging-a-virtual-machine-through-templates"></a>Een virtuele machine labelen via sjablonen
Eerst gaan we Tags bekijken via sjablonen. Met [deze sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) worden tags op de volgende resources geplaatst: Compute (virtuele machine), opslag (opslag account) en netwerk (openbaar IP-adres, Virtual Network, en netwerk interface). Deze sjabloon is voor een Windows-VM, maar kan worden aangepast voor Linux-Vm's.

Klik op de knop **implementeren naar Azure** van de [sjabloon koppeling](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Hiermee gaat u naar de [Azure Portal](https://portal.azure.com/) waar u deze sjabloon kunt implementeren.

![Eenvoudige implementatie met Tags](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

Deze sjabloon bevat de volgende Tags: *afdeling*, *toepassing*en *gemaakt door*. U kunt deze Tags rechtstreeks in de sjabloon toevoegen/bewerken als u andere label namen wilt.

![Azure Tags in een sjabloon](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

Zoals u kunt zien, worden de tags gedefinieerd als sleutel-waardeparen, gescheiden door een dubbele punt (:). De labels moeten worden gedefinieerd in deze indeling:

```config
"tags": {
    "Key1" : "Value1",
    "Key2" : "Value2"
}
```

Sla het sjabloon bestand op nadat u klaar bent met het bewerken van de gewenste labels.

Vervolgens kunt u in het gedeelte **para meters bewerken** de waarden voor uw Tags invullen.

![Tags bewerken in Azure Portal](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

Klik op **maken** om deze sjabloon te implementeren met de label waarden.

## <a name="tagging-through-the-portal"></a>Tags toevoegen via de portal
Nadat u uw resources met tags hebt gemaakt, kunt u tags in de portal weer geven, toevoegen en verwijderen.

Selecteer het pictogram Tags om uw tags weer te geven:

![Pictogram labels in Azure Portal](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

Voeg een nieuwe tag toe via de portal door uw eigen sleutel/waarde-paar te definiëren en op te slaan.

![Nieuwe tag toevoegen in Azure Portal](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

Het nieuwe label wordt nu weer gegeven in de lijst met tags voor uw resource.

![Nieuwe tag opgeslagen in Azure Portal](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)

