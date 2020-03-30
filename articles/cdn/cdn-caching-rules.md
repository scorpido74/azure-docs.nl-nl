---
title: Azure CDN-cachinggedrag beheren met cachingregels | Microsoft Documenten
description: U cdn-cachingregels gebruiken om het standaardverloopgedrag van de cache wereldwijd en met voorwaarden in te stellen of te wijzigen, zoals een URL-pad en bestandsextensies.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: magattus
ms.openlocfilehash: ddd7dc7e1245c2a77e866a454bf6bfa3c1f16f88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74278139"
---
# <a name="control-azure-cdn-caching-behavior-with-caching-rules"></a>Cachinggedrag in Azure CDN beheren met regels voor opslaan in cache

> [!NOTE] 
> Caching-regels zijn alleen beschikbaar voor **Azure CDN Standard van Verizon** en Azure **CDN Standard van Akamai-profielen.** Voor **Azure CDN van Microsoft-profielen** moet u de [standaardregelsengine](cdn-standard-rules-engine-reference.md) voor **Azure CDN Premium van Verizon-profielen** gebruiken, moet u de [Verizon Premium-regelsengine](cdn-rules-engine.md) in de portal **Beheren** gebruiken voor vergelijkbare functionaliteit.
 
Cdn (Azure Content Delivery Network) biedt twee manieren om te bepalen hoe uw bestanden in de cache worden opgeslagen: 

- Regels voor cache: in dit artikel wordt beschreven hoe u cdn-cachingregels (content delivery network) gebruiken om het verloopvan de standaardcache wereldwijd en met aangepaste voorwaarden in te stellen of te wijzigen, zoals een URL-pad en bestandsextensie. Azure CDN biedt twee typen regels voor opslaan in cache:

   - Globale regels voor opslaan in cache: u kunt voor elk eindpunt in uw profiel één globale regel voor opslaan in cache instellen, die geldt voor alle aanvragen op het eindpunt. De globale regel voor opslaan in cache overschrijft alle HTTP-headers met cache-instructies, indien aanwezig.

   - Aangepaste regels voor opslaan in cache: u kunt een of meer aangepaste regels voor opslaan in cache instellen voor elk eindpunt in uw profiel. Aangepaste regels voor opslaan in cache komen overeen met specifieke paden en bestandsextensies, worden in volgorde verwerkt en overschrijven de globale regel voor opslaan in cache, indien ingesteld. 

- Querytekenreeksen incachen: u aanpassen hoe het Azure CDN caching voor aanvragen met querytekenreeksen behandelt. Zie Azure [CDN-cachinggedrag met querytekenreeksen beheren voor](cdn-query-string.md)informatie. Als het bestand niet in de cache kan worden opgeslagen, heeft de instelling voor het plaatsen van querytekenreeksen geen effect op basis van cachingregels en cdn-standaardgedrag.

Zie [Hoe caching werkt](cdn-how-caching-works.md)voor informatie over standaardcachinggedrag en caching-richtlijnen. 


## <a name="accessing-azure-cdn-caching-rules"></a>Toegang tot Azure CDN-cachingregels

1. Open de Azure-portal, selecteer een CDN-profiel en selecteer vervolgens een eindpunt.

2. Selecteer in het linkerdeelvenster onder Instellingen de optie **Regels voor opslaan in cache**.

   ![Knop Regels voor CDN-caching](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   De pagina **Regels voor opslaan in cache** wordt weergegeven.

   ![Pagina Regels voor CDN-caching](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="caching-behavior-settings"></a>Gedragsinstellingen voor caching
Voor algemene en aangepaste cachingregels u de volgende **cachinggedragsinstellingen** opgeven:

- **Cache omzeilen:** cache en negeer de door oorsprong geleverde cache-richtlijnheaders niet.

- **Overschrijven:** de duur van de cache negeren; gebruik in plaats daarvan de opgegeven cacheduur. Dit zal cache-controle niet overschrijven: geen cache.

- **Instellen als deze ontbreekt**: Eer als cache-richtlijnvoor oorsprong wordt geleverd, als deze bestaan; anders gebruikt u de opgegeven cacheduur.

![Globale regels voor opslaan in cache](./media/cdn-caching-rules/cdn-global-caching-rules.png)

![Aangepaste regels voor opslaan in cache](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

## <a name="cache-expiration-duration"></a>Verloopperiode cache
Voor algemene en aangepaste cachingregels u de vervaldatum van de cache opgeven in dagen, uren, minuten en seconden:

- Voor de instellingen voor het gedrag **Caching** **overschrijven** en **instellen,** variëren de geldige cacheduur tussen 0 seconden en 366 dagen. Voor een waarde van 0 seconden slaat het CDN de inhoud op, maar moet elke aanvraag opnieuw worden gevalideerd met de oorspronkelijke server.

- Voor de **instelling Voor de cache van Bypass** wordt de cacheduur automatisch ingesteld op 0 seconden en kan deze niet worden gewijzigd.

## <a name="custom-caching-rules-match-conditions"></a>Aangepaste cachingregels komen overeen met de voorwaarden

Voor aangepaste cacheregels zijn twee wedstrijdvoorwaarden beschikbaar:
 
- **Pad**: Deze voorwaarde komt overeen met het pad van de\*URL, met uitzondering van de domeinnaam, en ondersteunt het wildcardsymbool ( ). Bijvoorbeeld _/myfile.html_, _/my/folder/*_, en _/my/images/*.jpg_. De maximale lengte is 260 tekens.

- **Extensie:** deze voorwaarde komt overeen met de bestandsextensie van het aangevraagde bestand. U een lijst met door komma's gescheiden bestandsextensies weergeven. Bijvoorbeeld _.jpg,_ _.mp3_of _.png_. Het maximum aantal extensies is 50 en het maximum aantal tekens per extensie is 16. 

## <a name="global-and-custom-rule-processing-order"></a>Algemene en aangepaste regelverwerkingsorder
Algemene en aangepaste cachingregels worden verwerkt in de volgende volgorde:

- Globale cachingregels hebben voorrang op het standaard gedrag van CDN-caching (http-cache-directive header-instellingen). 

- Aangepaste cachingregels hebben voorrang op algemene cachingregels, waar ze van toepassing zijn. Aangepaste cachingregels worden van boven naar beneden in volgorde verwerkt. Dat wil zeggen, als een aanvraag aan beide voorwaarden voldoet, hebben regels onderaan de lijst voorrang op regels bovenaan de lijst. Daarom moet u meer specifieke regels lager in de lijst plaatsen.

**Voorbeeld**:
- Globale cachingregel: 
   - Caching gedrag: **Overschrijven**
   - Vervaldatum cache: 1 dag

- Aangepaste cachingregel #1:
   - Wedstrijdvoorwaarde: **Pad**
   - Wedstrijdwaarde: _/home/*_
   - Caching gedrag: **Overschrijven**
   - Vervaldatum cache: 2 dagen

- Aangepaste cachingregel #2:
   - Wedstrijdvoorwaarde: **Extensie**
   - Overeenkomenmetwaarde: _.html_
   - Caching gedrag: **Instellen als ontbreekt**
   - Vervaldatum cache: 3 dagen

Wanneer deze regels zijn ingesteld, wordt een aanvraag voor _ &lt;de hostnaam&gt;van eindpunten_.azureedge.net/home/index.html aangepaste cachingregel #2, die is ingesteld op: Instellen als deze **ontbreekt** en 3 dagen. Daarom, als de *index.html* bestand heeft `Cache-Control` of `Expires` HTTP headers, ze worden geëerd; als deze kopteksten niet zijn ingesteld, wordt het bestand 3 dagen in de cache opgeslagen.

> [!NOTE] 
> Bestanden die in de cache worden opgeslagen voordat een regelwijziging wordt gewijzigd, behouden de instelling voor de duur van de oorsprongscache. Als u de cacheduur wilt resetten, moet u [het bestand wissen.](cdn-purge-endpoint.md) 
>
> Azure CDN-configuratiewijzigingen kunnen enige tijd duren voordat het netwerk wordt doorgegeven: 
> - Profielen van **Azure CDN Standard van Akamai** worden doorgaans binnen één minuut doorgegeven. 
> - Voor **Azure CDN Standard van Verizon-profielen** wordt de propagatie meestal in 10 minuten voltooid.  
>

## <a name="see-also"></a>Zie ook

- [Hoe caching werkt](cdn-how-caching-works.md)
- [Zelfstudie: Azure CDN-regels voor opslaan in cache instellen](cdn-caching-rules-tutorial.md)
