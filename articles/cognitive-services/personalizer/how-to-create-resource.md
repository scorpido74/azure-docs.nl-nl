---
title: Gepersonaliseerde bron maken
description: Serviceconfiguratie omvat hoe de service beloningen behandelt, hoe vaak de service wordt verkend, hoe vaak het model wordt omgeschoold en hoeveel gegevens worden opgeslagen.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: adb97db53d1fc0b6f0cdb14b697c82ec52501b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336065"
---
# <a name="create-a-personalizer-resource"></a>Een personalizerbron maken

Een Personalizer resource is hetzelfde als een Personalizer leerlus. Er wordt één resource of leerlus gemaakt voor elk onderwerpdomein of inhoudsgebied dat u hebt. Gebruik niet meerdere inhoudsgebieden in dezelfde lus, omdat dit de leerlus in verwarring brengt en slechte voorspellingen geeft.

Als u wilt dat Personalizer de beste inhoud selecteert voor meer dan één inhoudsgebied van een webpagina, gebruikt u voor elke pagina een andere leerlus.


## <a name="create-a-resource-in-the-azure-portal"></a>Een resource maken in de Azure-portal

Maak een personalizerbron voor elke feedbacklus.

1. Meld u aan bij de [Azure-portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). De vorige link brengt u naar de pagina **Maken** voor de personalizerservice.
1. Voer uw servicenaam in, selecteer een abonnement, locatie, prijscategorie en resourcegroep.

    > [!div class="mx-imgBorder"]
    > ![Gebruik Azure-portal om personalizerbronnen te maken, ook wel een leerlus genoemd.](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. Selecteer **Maken** om de resource te maken.

1. Nadat uw resource is geïmplementeerd, selecteert u de knop **Ga naar resource** om naar uw persoonlijke resource te gaan.

1. Selecteer de **startpagina Snel voor** uw resource en kopieer vervolgens de waarden voor uw eindpunt en sleutel. U hebt zowel het resourceeindpunt als de sleutel nodig om de rank- en belonings-API's te gebruiken.

1. Selecteer de **pagina Configuratie** voor de nieuwe resource om [de leerlus](how-to-settings.md)te configureren .

## <a name="create-a-resource-with-the-azure-cli"></a>Een resource maken met de Azure CLI

1. Meld u aan bij de Azure CLI met de volgende opdracht:

    ```azurecli-interactive
    az login
    ```

1. Maak een resourcegroep, een logische groepering om alle Azure-resources te beheren die u wilt gebruiken met de personalizerbron.


    ```azurecli-interactive
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. Maak een nieuwe personalizerresource, _leerlus,_ met de volgende opdracht voor een bestaande resourcegroep.

    ```azurecli-interactive
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```

    Hiermee wordt een JSON-object geretourneerd, dat uw **resourceeindpunt**bevat.

1. Gebruik de volgende opdracht Azure CLI om uw **resourcesleutel**op te halen.

    ```azurecli-interactive
        az cognitiveservices account keys list \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group
    ```

    U hebt zowel het resourceeindpunt als de sleutel nodig om de rank- en belonings-API's te gebruiken.

## <a name="next-steps"></a>Volgende stappen

* [Configureren](how-to-settings.md) Gepersonaliseerde leerlus