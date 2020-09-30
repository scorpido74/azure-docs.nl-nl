---
title: Zone redundantie voor Azure cache inschakelen voor redis (preview-versie)
description: Meer informatie over het instellen van zone redundantie voor uw Azure-cache voor de Premium-laag voor redis-instanties
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 33c346fa2e4572799ad6341bd5115cdd6e3b9ec9
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569984"
---
# <a name="enable-zone-redundancy-for-azure-cache-for-redis-preview"></a>Zone redundantie voor Azure cache inschakelen voor redis (preview-versie)
In dit artikel leert u hoe u een zone-redundante Azure-cache-instantie configureert met behulp van de Azure Portal.

Azure cache voor redis Standard-en Premium-lagen bieden ingebouwde redundantie door elke cache op twee specifieke virtuele machines (Vm's) te hosten. Hoewel deze Vm's zich in een afzonderlijke Azure-fout bevinden [en domeinen bijwerken](/azure/virtual-machines/windows/manage-availability) en Maxi maal beschikbaar zijn, zijn ze gevoelig voor storingen op het niveau van het Data Center. Azure cache voor redis biedt ook ondersteuning voor zone redundantie in de Premium-laag. Een zone-redundante cache wordt op Vm's verdeeld over meerdere [beschikbaarheids zones](/azure/virtual-machines/windows/manage-availability#use-availability-zones-to-protect-from-datacenter-level-failures). Het biedt meer flexibiliteit en meer Beschik baarheid.

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
   
1. Configureer de instellingen voor de nieuwe cache op de pagina **basis beginselen** .
   
    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Abonnement** | Selecteer uw abonnement. | Het abonnement waarmee dit nieuwe Azure Cache voor Redis-exemplaar wordt gemaakt. | 
    | **Resourcegroep** | Selecteer een resource groep of selecteer **nieuwe maken** en voer een nieuwe naam voor de resource groep in. | Naam voor de resourcegroep waarin de cache en andere resources moeten worden gemaakt. Door al uw app-resources in één resourcegroep te plaatsen, kunt u ze eenvoudig beheren of verwijderen. | 
    | **DNS-naam** | Geef een wereldwijd unieke naam op. | De cachenaam is een tekenreeks van 1 tot 63 tekens die alleen cijfers, letters en afbreekstreepjes mag bevatten. De naam moet beginnen en eindigen met een cijfer of letter en mag geen opeenvolgende afbreekstreepjes bevatten. De *hostnaam* van uw cache-exemplaar wordt *\<DNS name>.redis.cache.windows.net*. | 
    | **Locatie** | Selecteer een locatie. | Selecteer een [regio](https://azure.microsoft.com/regions/) in de buurt van andere services die gaan gebruikmaken van de cache. |
    | **Cache type** | Selecteer een cache voor de [Premium-laag](https://azure.microsoft.com/pricing/details/cache/) . |  De prijscategorie bepaalt de grootte, prestaties en functies die beschikbaar zijn voor de cache. Zie het [Azure Cache voor Redis-overzicht](cache-overview.md) voor meer informatie. |
   
1. Kies op de pagina **Geavanceerd** de optie **aantal replica's**.
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="Selecteer Azure-cache voor redis.":::

1. Selecteer **beschikbaarheids zones**. 
   
    :::image type="content" source="media/cache-how-to-zone-redundancy/create-zones.png" alt-text="Selecteer Azure-cache voor redis.":::

1. Wijzig de standaard instellingen van andere opties. 

    > [!NOTE]
    > Ondersteuning voor zone redundantie werkt momenteel alleen met niet-geclusterde en niet-Geo-gerepliceerde caches. Daarnaast biedt het geen ondersteuning voor persoonlijke koppelingen, schalen, gegevens persistentie of importeren/exporteren.
    >

1. Klik op **Create**. 
   
    Het duurt even voordat de cache is gemaakt. U kunt de voortgang bekijken op de **overzichtspagina** van Azure Cache voor Redis. Als u bij **Status** **Wordt uitgevoerd** ziet staan, kunt u de cache gebruiken.
   
    > [!NOTE]
    > Beschikbaarheids zones kunnen niet worden gewijzigd nadat een cache is gemaakt.
    >

## <a name="next-steps"></a>Volgende stappen
Meer informatie over Azure cache voor redis-functies.

> [!div class="nextstepaction"]
> [Azure-cache voor redis Premium-Service lagen](cache-overview.md#service-tiers)
