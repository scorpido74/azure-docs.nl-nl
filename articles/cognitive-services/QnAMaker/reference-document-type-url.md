---
title: URL's ondersteund voor import - QnA Maker
description: Begrijpen hoe de typen URL's worden gebruikt om QnA-sets te importeren en te maken.
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: cf2a02f55d7c95ff9b3f0231ea07a783d18b73cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651826"
---
# <a name="urls-supported-for-importing-documents"></a>URL's die worden ondersteund voor het importeren van documenten

Begrijpen hoe de typen URL's worden gebruikt om QnA-sets te importeren en te maken.

## <a name="faq-urls"></a>Veelgestelde URL's

QnA Maker kan FAQ-webpagina's in 3 verschillende vormen ondersteunen:

* Veelgestelde vragen
* Veelgestelde pagina's met koppelingen
* Veelgestelde vragen met een homepage voor onderwerpen

### <a name="plain-faq-pages"></a>Veelgestelde vragen

Dit is het meest voorkomende type FAQ-pagina, waarbij de antwoorden onmiddellijk de vragen op dezelfde pagina volgen.

Hieronder is een voorbeeld van een duidelijke FAQ pagina:

![Voorbeeld van een gewone FAQ-pagina voor een kennisbank](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Veelgestelde pagina's met koppelingen

In dit type FAQ-pagina worden vragen samengevoegd en gekoppeld aan antwoorden die zich in verschillende secties van dezelfde pagina of op verschillende pagina's bevinden.

Hieronder vindt u een voorbeeld van een FAQ-pagina met koppelingen in secties die zich op dezelfde pagina bevinden:

 ![SectieKoppelingfaq-paginavoorbeeld voor een kennisbank](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Paginapagina Bovenliggende onderwerpen naar pagina's met onderliggende antwoorden

Dit type FAQ heeft een pagina Onderwerpen waar elk onderwerp is gekoppeld aan een overeenkomstige set vragen en antwoorden op een andere pagina. QnA Maker crawlt alle gekoppelde pagina's om de bijbehorende vragen & antwoorden te extraheren.

Hieronder vindt u een voorbeeld van een pagina Onderwerpen met koppelingen naar FAQ-secties op verschillende pagina's.

 ![Deep link FAQ pagina voorbeeld voor een kennisbank](./media/qnamaker-concepts-datasources/topics-faq.png)

## <a name="support-urls"></a>URL's ondersteunen

QnA Maker kan semi-gestructureerde ondersteuningswebpagina's verwerken, zoals webartikelen waarin wordt beschreven hoe een bepaalde taak moet worden uitgevoerd, hoe u een bepaald probleem diagnosticeren en oplossen en wat de beste praktijken zijn voor een bepaald proces. Extractie werkt het beste op inhoud die een duidelijke structuur heeft met hiërarchische koppen.

> [!NOTE]
> Extractie voor ondersteuningsartikelen is een nieuwe functie en bevindt zich in een vroeg stadium. Het werkt het beste voor eenvoudige pagina's, die goed gestructureerd zijn en geen complexe headers/footers bevatten.

![QnA Maker ondersteunt extractie van semi-gestructureerde webpagina's waar een duidelijke structuur wordt gepresenteerd met hiërarchische koppen](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)