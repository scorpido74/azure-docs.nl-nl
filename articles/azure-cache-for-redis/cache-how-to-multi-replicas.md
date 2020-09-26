---
title: Replica's toevoegen aan Azure cache voor redis (preview-versie)
description: Meer informatie over het toevoegen van meer replica's aan uw Azure-cache voor de Premium-laag voor redis-instanties
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: a747cf8e1713eb905aee02af95c568a448b47f05
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91344326"
---
# <a name="add-replicas-to-azure-cache-for-redis-preview"></a>Replica's toevoegen aan Azure cache voor redis (preview-versie)
In dit artikel leert u hoe u een Azure-cache-exemplaar met aanvullende replica's instelt met behulp van de Azure Portal.

Azure cache voor de redis Standard en Premium-lagen bieden redundantie door elke cache te hosten op twee toegewezen virtuele machines (Vm's). Deze Vm's zijn geconfigureerd als primair en replica. Wanneer de primaire virtuele machine niet meer beschikbaar is, detecteert de replica dat en wordt automatisch de nieuwe primaire VM overgenomen. U kunt nu het aantal replica's in een Premium-cache verhogen tot drie, waardoor u een totaal van vier Vm's hebt die een cache back-ups maken. Als er meerdere replica's zijn, resulteert dit in hogere toleranties dan wat één replica kan bieden.

> [!IMPORTANT]
> Deze preview-versie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 
> 

## <a name="prerequisites"></a>Vereisten
* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)

> [!NOTE]
> Deze functie is momenteel beschikbaar als preview- [contact](mailto:azurecache@microsoft.com) opnemen als u geïnteresseerd bent.
>

## <a name="create-a-cache"></a>Een cache maken
Voer de volgende stappen uit om een cache te maken:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en selecteer **Create a resource**.
  
1. Selecteer op de pagina **Nieuw** de optie **Databases** en selecteer vervolgens **Azure Cache voor Redis**.

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Selecteer Azure-cache voor redis.":::
   
1. Configureer op de pagina **Nieuwe Redis-cache** de instellingen voor de nieuwe cache.
   
    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ |  ------- | -------------------------------------------------- |
    | **DNS-naam** | Geef een wereldwijd unieke naam op. | De cachenaam is een tekenreeks van 1 tot 63 tekens die alleen cijfers, letters en afbreekstreepjes mag bevatten. De naam moet beginnen en eindigen met een cijfer of letter en mag geen opeenvolgende afbreekstreepjes bevatten. De *hostnaam* van uw cache-exemplaar wordt *\<DNS name>.redis.cache.windows.net*. | 
    | **Abonnement** | Open de vervolgkeuzelijst en selecteer uw abonnement. | Het abonnement waarmee dit nieuwe Azure Cache voor Redis-exemplaar wordt gemaakt. | 
    | **Resourcegroep** | Open de vervolgkeuzelijst en selecteer een resourcegroep of kies **Nieuwe maken** en geef een naam voor de nieuwe resourcegroep op. | Naam voor de resourcegroep waarin de cache en andere resources moeten worden gemaakt. Door al uw app-resources in één resourcegroep te plaatsen, kunt u ze eenvoudig beheren of verwijderen. | 
    | **Locatie** | Open de vervolgkeuzelijst en selecteer een locatie. | Selecteer een [regio](https://azure.microsoft.com/regions/) in de buurt van andere services die gaan gebruikmaken van de cache. |
    | **Prijscategorie** | Vervolg keuzelijst en selecteer een cache voor de [Premium-laag](https://azure.microsoft.com/pricing/details/cache/) . |  De prijscategorie bepaalt de grootte, prestaties en functies die beschikbaar zijn voor de cache. Zie het [Azure Cache voor Redis-overzicht](cache-overview.md) voor meer informatie. |
    | **Aantal replica's** | Dia om het aantal replica's te kiezen. | Standaard is 1. |
   
1. Nadat u een Premium-laag cache hebt geselecteerd, wordt u gevraagd of u redis-Clustering wilt inschakelen. **Clustering** als *uitgeschakeld*laten staan. 
   
    :::image type="content" source="media/cache-how-to-premium-clustering/redis-clustering-disabled.png" alt-text="Configureer het redis-cluster.":::

    > [!NOTE]
    > Ondersteuning voor meerdere replica's werkt momenteel alleen met niet-geclusterde caches.
    >

1. Klik op **Create**. 
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="Maak een Azure-cache voor redis.":::
   
    Het duurt even voordat de cache is gemaakt. U kunt de voortgang bekijken op de **overzichtspagina** van Azure Cache voor Redis. Als u bij **Status** **Wordt uitgevoerd** ziet staan, kunt u de cache gebruiken.

    > [!NOTE]
    > Het aantal replica's in een cache kan niet worden gewijzigd nadat het is gemaakt.
    >

## <a name="next-steps"></a>Volgende stappen
Meer informatie over Azure cache voor redis-functies.

> [!div class="nextstepaction"]
> [Azure-cache voor redis Premium-Service lagen](cache-overview.md#service-tiers)
