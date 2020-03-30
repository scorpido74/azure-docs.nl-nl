---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: f6bd574c83d309ce6d6f54fdb1c7d23cb713420d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73182201"
---
## <a name="tagging-a-virtual-machine-through-templates"></a>Een virtuele machine taggen via sjablonen
Laten we eerst eens kijken naar het taggen via sjablonen. [Deze sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags) plaatst tags op de volgende bronnen: Compute (Virtual Machine), Storage (Storage Account) en Network (Public IP Address, Virtual Network, and Network Interface). Deze sjabloon is voor een Windows VM, maar kan worden aangepast voor Linux VM's.

Klik **op** de knop Implementeren naar Azure in de [sjabloonkoppeling](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-tags). Hiermee wordt naar de [Azure-portal](https://portal.azure.com/) genavigeerd waar u deze sjabloon implementeren.

![Eenvoudige implementatie met tags](./media/virtual-machines-common-tag/deploy-to-azure-tags.png)

Deze sjabloon bevat de volgende tags: *Afdeling,* *Toepassing*en *Gemaakt door*. U deze tags rechtstreeks in de sjabloon toevoegen/bewerken als u verschillende tagnamen wilt.

![Azure-tags in een sjabloon](./media/virtual-machines-common-tag/azure-tags-in-a-template.png)

Zoals u zien, worden de tags gedefinieerd als sleutel/waardeparen, gescheiden door een dubbele punt (:). De tags moeten in deze indeling worden gedefinieerd:

        "tags": {
            "Key1" : "Value1",
            "Key2" : "Value2"
        }

Sla het sjabloonbestand op nadat u het hebt bewerkt met de tags van uw keuze.

Vervolgens u in de sectie **Parameters bewerken** de waarden voor uw tags invullen.

![Tags bewerken in Azure-portal](./media/virtual-machines-common-tag/edit-tags-in-azure-portal.png)

Klik **op Maken** om deze sjabloon te implementeren met uw tagwaarden.

## <a name="tagging-through-the-portal"></a>Tagging via de portal
Nadat u uw resources hebt maken met tags, u tags in de portal bekijken, toevoegen en verwijderen.

Selecteer het pictogram tags om uw tags weer te geven:

![Pictogram Tags in Azure-portal](./media/virtual-machines-common-tag/azure-portal-tags-icon.png)

Voeg een nieuwe tag toe via de portal door uw eigen sleutel-/waardepaar te definiëren en op te slaan.

![Nieuwe tag toevoegen in Azure-portal](./media/virtual-machines-common-tag/azure-portal-add-new-tag.png)

Uw nieuwe tag moet nu worden weergegeven in de lijst met tags voor uw resource.

![Nieuwe tag opgeslagen in Azure-portal](./media/virtual-machines-common-tag/azure-portal-saved-new-tag.png)

