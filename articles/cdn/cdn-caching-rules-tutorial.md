---
title: 'Zelfstudie: regels voor Azure CDN-caching instellen | Microsoft Doc'
description: In deze zelfstudie stelt u globale en aangepaste regels voor Azure CDN-caching in.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/20/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 5330c99f4d42f591944ddc5c58ac98bea6387da5
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278153"
---
# <a name="tutorial-set-azure-cdn-caching-rules"></a>Zelfstudie: regels voor Azure CDN-caching instellen

> [!NOTE] 
> Cache regels zijn alleen beschikbaar voor **Azure CDN Standard van Verizon** en **Azure CDN Standard van Akamai** -profielen. Voor **Azure CDN van micro soft** -profielen moet u de [Standard Rules engine](cdn-standard-rules-engine-reference.md) gebruiken voor **Azure CDN Premium van Verizon** -profielen. u moet de [regel engine van Verizon Premium](cdn-rules-engine.md) gebruiken in de portal **beheren** voor vergelijk bare functionaliteit.
 

In deze zelfstudie wordt beschreven hoe u regels voor Azure CDN-caching (Content Delivery Network) kunt gebruiken om het standaardgedrag van de cacheverlooptijd in te stellen of te wijzigen, zowel globaal als aangepast, zoals een URL-pad en bestandsextensie. Azure CDN biedt twee typen regels voor opslaan in cache:
- Globale regels voor opslaan in cache: u kunt voor elk eindpunt in uw profiel één globale regel voor opslaan in cache instellen, die geldt voor alle aanvragen op het eindpunt. De globale regel voor opslaan in cache overschrijft alle HTTP-headers met cache-instructies, indien aanwezig.

- Aangepaste regels voor opslaan in cache: u kunt een of meer aangepaste regels voor opslaan in cache instellen voor elk eindpunt in uw profiel. Aangepaste regels voor opslaan in cache komen overeen met specifieke paden en bestandsextensies, worden in volgorde verwerkt en overschrijven de globale regel voor opslaan in cache, indien ingesteld. 

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> - De pagina Regels voor opslaan in cache openen.
> - Een globale regel voor opslaan in cache maken.
> - Een aangepaste regel voor opslaan in cache maken.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u de stappen in deze zelfstudie kunt voltooien, moet u eerst een CDN-profiel en ten minste één CDN-eindpunt maken. Zie voor meer informatie [Snelstartgids: Een Azure CDN-profiel en een eindpunt maken](cdn-create-new-endpoint.md).

## <a name="open-the-azure-cdn-caching-rules-page"></a>Open de pagina Regels voor Azure CDN-caching

1. Selecteer in [Azure Portal](https://portal.azure.com) een CDN-profiel. Selecteer vervolgens een eindpunt.

2. Selecteer in het linkerdeelvenster onder Instellingen de optie **Regels voor opslaan in cache**.

   ![Knop Regels voor CDN-caching](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   De pagina **Regels voor opslaan in cache** wordt weergegeven.

   ![Pagina Regels voor CDN-caching](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="set-global-caching-rules"></a>Globale regels voor opslaan in cache instellen

U maakt als volgt een globale regel voor opslaan in cache:

1. Stel onder **Globale regels voor opslaan in cache** de optie **Cachinggedrag van querytekenreeks** in op **Querytekenreeks negeren**.

2. Stel **Cachinggedrag** in op **Instellen bij ontbreken**.
       
3. Voer bij **Verloopduur voor cache** in het veld **Dagen** 10 in.

    De globale regel voor opslaan in cache beïnvloedt alle aanvragen op het eindpunt. Deze regel honoreert de oorspronkelijke headers met cache-instructies, als deze bestaan (`Cache-Control` of `Expires`). In andere gevallen, als deze niet zijn opgegeven, wordt de cache ingesteld op 10 dagen. 

    ![Globale regels voor opslaan in cache](./media/cdn-caching-rules/cdn-global-caching-rules.png)

## <a name="set-custom-caching-rules"></a>Aangepaste regels voor opslaan in cache instellen

U maakt als volgt een aangepaste regel voor opslaan in cache:

1. Stel onder **Aangepaste regels voor opslaan in cache** **Voorwaarde voor overeenkomst** in op **Pad** en **Overeenkomende waarde** op `/images/*.jpg`.
    
2. Stel **Cachinggedrag** in op **Overschrijven** en voer in het veld **Dagen** 30 in.
       
    Met deze aangepaste regels voor opslaan in cache wordt een cacheduur van 30 dagen ingesteld voor alle `.jpg`-afbeeldingsbestanden in de `/images`-map op het eindpunt. Dit overschrijft alle `Cache-Control`- of `Expires`-HTTP-headers die zijn verzonden door de oorspronkelijke servers.

    ![Aangepaste regels voor opslaan in cache](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

    
## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u regels voor opslaan in cache gemaakt. Als u deze regels voor opslaan in cache niet meer wilt gebruiken, kunt u ze verwijderen door deze stappen uit te voeren:
 
1. Selecteer een CDN-profiel. Selecteer vervolgens het eindpunt met de regels voor opslaan in cache die u wilt verwijderen.

2. Selecteer in het linkerdeelvenster onder Instellingen de optie **Regels voor opslaan in cache**.

3. Stel onder **Globale regels voor opslaan in cache** **Cachinggedrag** in op **Niet ingesteld**.
 
4. Selecteer onder **Aangepaste regels voor opslaan in cache** het selectievakje naast de regel die u wilt verwijderen.

5. Selecteer **Verwijderen**.

6. Selecteer **Opslaan** boven aan de pagina.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> - De pagina Regels voor opslaan in cache openen.
> - Een globale regel voor opslaan in cache maken.
> - Een aangepaste regel voor opslaan in cache maken.

Ga door met het volgende artikel om te leren hoe u extra instellingen voor regels voor het opslaan in cache configureert.

> [!div class="nextstepaction"]
> [Cachinggedrag in Azure CDN beheren met regels voor opslaan in cache](cdn-caching-rules.md)



