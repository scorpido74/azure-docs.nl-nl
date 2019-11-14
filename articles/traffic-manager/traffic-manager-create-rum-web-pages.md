---
title: Real-user-metingen met webpagina's-Azure Traffic Manager
description: In dit artikel leert u hoe u uw webpagina's kunt instellen om Real-user-metingen te verzenden naar Azure Traffic Manager.
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 6e3cf5af5aaa894b1595d67c0056073a458b0a88
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74040300"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Real-user-metingen naar Azure Traffic Manager verzenden met behulp van webpagina's

U kunt uw webpagina's zo configureren dat Real-user-metingen naar Traffic Manager worden verzonden door een Real-user-metingen (RUM)-sleutel te verkrijgen en de gegenereerde code op de webpagina in te sluiten.

## <a name="obtain-a-real-user-measurements-key"></a>Een Real-user-metingen sleutel verkrijgen

De metingen die u uitvoert en verzenden naar Traffic Manager vanuit uw client toepassing worden geïdentificeerd door de service met behulp van een unieke teken reeks, de **real-User-metingen (rum)-sleutel**. U kunt een RUM-sleutel ophalen met behulp van de Azure Portal, een REST API of door gebruik te maken van de Power shell of Azure CLI.

Voor het verkrijgen van de sleutel RUM met behulp van Azure Portal:
1. Meld u aan bij de Azure Portal vanuit een browser. Als u nog geen account hebt, kunt u zich aanmelden voor een gratis proef versie van één maand.
2. Zoek in de zoek balk van de portal naar de naam van het Traffic Manager profiel dat u wilt wijzigen en klik vervolgens op het Traffic Manager profiel in de resultaten die worden weer gegeven.
3. Klik in de Blade Traffic Manager profiel op **real-User-metingen** onder **instellingen**.
4. Klik op **sleutel genereren** om een nieuwe sleutel rum te maken.
 
   ![Real-user-metingen sleutel genereren](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Afbeelding 1: genereren van sleutels Real-user-metingen**

5. Op de Blade worden nu de gegenereerde RUM-sleutel en een Java script-code fragment weer gegeven dat moet worden inge sloten in de HTML-pagina.
 
    ![Java script-code voor Real-user-metingen sleutel](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **Afbeelding 2: Real-user-metingen Key en meting java script**
 
6. Klik op de knop **kopiëren** om de Java script-code te kopiëren. 

>[!IMPORTANT]
> Gebruik de gegenereerde java script-functie om Real-user-metingen goed te laten functioneren. Wijzigingen in dit script of de scripts die door Real-user-metingen worden gebruikt, kunnen leiden tot onvoorspelbaar gedrag.

## <a name="embed-the-code-to-an-html-web-page"></a>De code insluiten op een HTML-webpagina

Nadat u de sleutel RUM hebt verkregen, is de volgende stap het insluiten van deze gekopieerde java script naar een HTML-pagina die uw eind gebruikers bezoeken. Het bewerken van HTML kan op verschillende manieren worden uitgevoerd en het gebruik van verschillende hulpprogram ma's en werk stromen. In dit voor beeld ziet u hoe u een HTML-pagina bijwerkt om dit script toe te voegen. U kunt deze richt lijnen gebruiken om deze aan te passen aan uw HTML-bron beheer werk stroom.

1.  Open de HTML-pagina in een tekst editor
2.  Plak de Java script-code die u in de vorige stap hebt gekopieerd naar de hoofd sectie van de HTML (de gekopieerde code bevindt zich op regel 8 & 9, zie afbeelding 3).
 
    ![Java script-code insluiten in een webpagina voor Real-user-metingen](./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png)  

    **Afbeelding 3: eenvoudige HTML met Inge sloten Real-user-metingen java script**

3.  Sla het HTML-bestand op en host het op een webserver die is verbonden met internet. 
4. De volgende keer dat deze pagina wordt weer gegeven in een webbrowser, wordt de Java script waarnaar wordt verwezen gedownload en worden de metings-en rapportage bewerkingen uitgevoerd door het script.


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [real-User-metingen](traffic-manager-rum-overview.md)
- Meer informatie [over de werking van Traffic Manager](traffic-manager-overview.md)
- Meer informatie over de [routerings methoden voor verkeer](traffic-manager-routing-methods.md) die door Traffic Manager worden ondersteund
- Meer informatie over het [maken van een Traffic Manager profiel](traffic-manager-create-profile.md)

