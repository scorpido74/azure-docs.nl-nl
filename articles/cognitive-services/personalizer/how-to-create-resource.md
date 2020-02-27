---
title: Een persoonlijke resource maken
description: Service configuratie bevat de manier waarop de service beloningen behandelt, hoe vaak de service bekijkt, hoe vaak het model opnieuw wordt getraind en hoeveel gegevens worden opgeslagen.
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: cb14415f3a5950ad1534d9eb8da94198a41f4f91
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624229"
---
# <a name="create-a-personalizer-resource"></a>Een persoonlijke resource maken

Een persoonlijke resource is hetzelfde als een aangepaste lus voor het leren. Er wordt één resource of Learning-lus gemaakt voor elk onderwerp dat u hebt of het inhouds gebied dat u hebt. Gebruik niet meerdere inhouds gebieden in dezelfde lus, omdat dit de leer proces verwart en slechte voor spellingen oplevert.

Als u de beste inhoud voor meer dan één inhouds gebied van een webpagina wilt selecteren, moet u voor elke inhoud een andere lus gebruiken.


## <a name="create-a-resource-in-the-azure-portal"></a>Een resource maken in de Azure Portal

Maak een persoonlijke resource voor elke feedback-lus.

1. Meld u aan bij de [Azure-portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Met de vorige koppeling gaat u naar de pagina **maken** voor de personaler-service.
1. Voer uw service naam in, selecteer een abonnement, locatie, prijs categorie en resource groep.

    > [!div class="mx-imgBorder"]
    > ![gebruik Azure Portal om een persoonlijker resource te maken, ook wel een Learning-lus genoemd.](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. Selecteer **maken** om de resource te maken.

1. Zodra de resource is geïmplementeerd, selecteert u de knop **Ga naar resource** om naar uw persoonlijke resource te gaan. Ga naar de **configuratie** pagina voor de nieuwe resource om [de learning-lus te configureren](how-to-settings.md).

## <a name="create-a-resource-with-the-azure-cli"></a>Een resource maken met de Azure CLI

1. Meld u aan bij de Azure CLI met de volgende opdracht:

    ```bash
    az login
    ```

1. Maak een resource groep, een logische groepering, voor het beheer van alle Azure-resources die u wilt gebruiken met de persoonlijke resource.


    ```bash
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. Maak een nieuwe Personaler-resource, _Learning-lus_, met de volgende opdracht voor een bestaande resource groep.

    ```bash
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```
## <a name="next-steps"></a>Volgende stappen

* [Configureren](how-to-settings.md) Leer loop van personaler