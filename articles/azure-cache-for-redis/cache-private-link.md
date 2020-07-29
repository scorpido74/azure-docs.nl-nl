---
title: Azure-cache voor redis met persoonlijke Azure-koppeling (preview-versie)
description: Persoonlijk Azure-eind punt is een netwerk interface waarmee u privé en veilig kunt verbinden met Azure cache voor redis die worden aangestuurd door een persoonlijke Azure-koppeling. In dit artikel wordt beschreven hoe u een Azure-cache, een virtueel Azure-netwerk en een persoonlijk eind punt maakt met behulp van de Azure Portal.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: d85fe36bb948ae9a0c81fa25f87450c7f5fe93b7
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337259"
---
# <a name="azure-cache-for-redis-with-azure-private-link-preview"></a>Azure-cache voor redis met persoonlijke Azure-koppeling (preview-versie)
Persoonlijk Azure-eind punt is een netwerk interface waarmee u privé en veilig kunt verbinden met Azure cache voor redis die worden aangestuurd door een persoonlijke Azure-koppeling. 

In dit artikel leert u hoe u een Azure-cache, een virtueel Azure-netwerk en een persoonlijk eind punt maakt met behulp van de Azure Portal.  

## <a name="prerequisites"></a>Vereisten
* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)

> [!NOTE]
  > Deze functie is momenteel beschikbaar als preview- [contact](mailto:azurecache@microsoft.com) opnemen als u geïnteresseerd bent.
  >


## <a name="create-a-cache"></a>Een cache maken
1. Als u een cache wilt maken, meldt u zich aan bij de [Azure Portal](https://portal.azure.com) en selecteert u **een resource maken**. 

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Selecteer een resource maken.":::
   
1. Selecteer op de pagina **Nieuw** de optie **Databases** en selecteer vervolgens **Azure Cache voor Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Selecteer Azure-cache voor redis.":::
   
1. Configureer op de pagina **Nieuwe Redis-cache** de instellingen voor de nieuwe cache.
   
   | Instelling      | Voorgestelde waarde  | Beschrijving |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-naam** | Geef een wereldwijd unieke naam op. | De cachenaam is een tekenreeks van 1 tot 63 tekens die alleen cijfers, letters en afbreekstreepjes mag bevatten. De naam moet beginnen en eindigen met een cijfer of letter en mag geen opeenvolgende afbreekstreepjes bevatten. De *hostnaam* van uw cache-exemplaar is * \<DNS name> . redis.cache.Windows.net*. | 
   | **Abonnement** | Open de vervolgkeuzelijst en selecteer uw abonnement. | Het abonnement waarmee dit nieuwe Azure Cache voor Redis-exemplaar wordt gemaakt. | 
   | **Resourcegroep** | Open de vervolgkeuzelijst en selecteer een resourcegroep of kies **Nieuwe maken** en geef een naam voor de nieuwe resourcegroep op. | Naam voor de resourcegroep waarin de cache en andere resources moeten worden gemaakt. Door al uw app-resources in één resourcegroep te plaatsen, kunt u ze eenvoudig beheren of verwijderen. | 
   | **Locatie** | Open de vervolgkeuzelijst en selecteer een locatie. | Selecteer een [regio](https://azure.microsoft.com/regions/) in de buurt van andere services die uw cache gebruiken. |
   | **Prijscategorie** | Vervolg keuzelijst en selecteer een [prijs categorie](https://azure.microsoft.com/pricing/details/cache/). |  De prijscategorie bepaalt de grootte, prestaties en functies die beschikbaar zijn voor de cache. Zie het [Azure Cache voor Redis-overzicht](cache-overview.md) voor meer informatie. |
   
1. Selecteer **Maken**. 
   
    :::image type="content" source="media/cache-private-link/3-new-cache.png" alt-text="Maak een Azure-cache voor redis.":::
   
   Het duurt even voordat de cache is gemaakt. U kunt de voortgang bekijken op de **overzichtspagina** van Azure Cache voor Redis. Als u bij **Status** **Wordt uitgevoerd** ziet staan, kunt u de cache gebruiken.
    
    :::image type="content" source="media/cache-private-link/4-status.png" alt-text="Azure-cache voor redis is gemaakt.":::

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

In deze sectie maakt u een virtueel netwerk en een subnet.

1. Selecteer **Een resource maken**.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Selecteer een resource maken.":::

2. Selecteer op de pagina **Nieuw** de optie **netwerken** en selecteer vervolgens **virtueel netwerk**.

    :::image type="content" source="media/cache-private-link/5-select-vnet.png" alt-text="Maak een virtueel netwerk.":::

3. In **virtueel netwerk maken**typt of selecteert u deze informatie op het tabblad **basis beginselen** :

    | **Instelling**          | **Waarde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projectgegevens**  |                                                                 |
    | Abonnement     | Open de vervolgkeuzelijst en selecteer uw abonnement.                                  |
    | Resourcegroep   | Vervolg keuzelijst en selecteer een resource groep. |
    | **Exemplaardetails** |                                                                 |
    | Naam             | **\<virtual-network-name>** invoeren                                    |
    | Regio           | Uitgeschakeld**\<region-name>** |

4. Selecteer het tabblad **IP-adressen** of selecteer de knop **volgende: IP-adressen** aan de onderkant van de pagina.

5. Voer de volgende gegevens in op het tabblad **IP-adressen** :

    | Instelling            | Waarde                      |
    |--------------------|----------------------------|
    | IPv4-adres ruimte | **\<IPv4-address-space>** invoeren |

6. Selecteer bij **subnetnaam**het woord **standaard**.

7. Voer in **subnet bewerken**de volgende gegevens in:

    | Instelling            | Waarde                      |
    |--------------------|----------------------------|
    | Subnetnaam | **\<subnet-name>** invoeren |
    | Subnetadresbereik | **\<subnet-address-range>** invoeren

8. Selecteer **Opslaan**.

9. Selecteer het tabblad **controleren + maken** of selecteer de knop **beoordeling + maken** .

10. Selecteer **Maken**.


## <a name="create-a-private-endpoint"></a>Een privé-eindpunt maken 

In deze sectie maakt u een persoonlijk eind punt en verbindt u het met de cache die u eerder hebt gemaakt.

1. Zoek naar **persoonlijke koppeling** en druk op ENTER of Selecteer deze in de zoek suggesties.

    :::image type="content" source="media/cache-private-link/7-create-private-link.png" alt-text="Zoek naar een privé-koppeling.":::

2. Selecteer aan de linkerkant van het scherm **persoonlijke eind punten**.

    :::image type="content" source="media/cache-private-link/8-select-private-endpoint.png" alt-text="Selecteer privé-koppeling.":::

3. Selecteer de knop **+ toevoegen** om uw persoonlijke eind punt te maken. 

    :::image type="content" source="media/cache-private-link/9-add-private-endpoint.png" alt-text="Voeg een persoonlijke koppeling toe.":::

4. Configureer op de **pagina een persoonlijk eind punt maken**de instellingen voor uw persoonlijke eind punt.

    | Instelling | Waarde |
    | ------- | ----- |
    | **PROJECTGEGEVENS** | |
    | Abonnement | Open de vervolgkeuzelijst en selecteer uw abonnement. |
    | Resourcegroep | Vervolg keuzelijst en selecteer een resource groep. |
    | **EXEMPLAARDETAILS** |  |
    | Naam |Voer een naam in voor uw privé-eind punt.  |
    | Regio |Open de vervolgkeuzelijst en selecteer een locatie. |
    |||

5. Selecteer de knop **volgende: resource** onder aan de pagina.

6. Selecteer uw abonnement op het tabblad **resource** , kies het resource type als micro soft. cache-redis en selecteer vervolgens de cache die u hebt gemaakt in de vorige sectie.

    :::image type="content" source="media/cache-private-link/10-resource-private-endpoint.png" alt-text="Resources voor privé koppeling.":::

7. Selecteer de knop **volgende: Configuratie** onder aan de pagina.

8. Op het tabblad **configuratie** selecteert u het virtuele netwerk en het subnet dat u in de vorige sectie hebt gemaakt.

    :::image type="content" source="media/cache-private-link/11-configuration-private-endpoint.png" alt-text="Configuratie voor privé-koppeling.":::

9. Selecteer de knop **Volgende: Tags** onderaan de pagina.

10. Voer op het tabblad **labels** de naam en waarde in als u de resource wilt categoriseren. Deze stap is optioneel.

    :::image type="content" source="media/cache-private-link/12-tags-private-endpoint.png" alt-text="Labels voor privé koppeling.":::

11. Selecteer **controleren + maken**. U gaat naar het tabblad **controleren + maken**,   waar Azure uw configuratie valideert.

12. Zodra het bericht groene **validatie is voltooid** wordt weer gegeven, selecteert u **maken**.


## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure private link](https://docs.microsoft.com/azure/private-link/private-link-overview)voor meer informatie over persoonlijke koppelingen. 

