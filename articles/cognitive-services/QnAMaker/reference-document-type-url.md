---
title: Typen Url's die worden ondersteund voor import-QnA Maker
description: Begrijpen hoe de typen Url's worden gebruikt voor het importeren en maken van QnA sets.
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: cf2a02f55d7c95ff9b3f0231ea07a783d18b73cf
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651826"
---
# <a name="urls-supported-for-importing-documents"></a>Url's die worden ondersteund voor het importeren van documenten

Begrijpen hoe de typen Url's worden gebruikt voor het importeren en maken van QnA sets.

## <a name="faq-urls"></a>Veelgestelde vragen over URL 's

QnA Maker kunnen webpagina's met veelgestelde vragen in drie verschillende vormen ondersteunen:

* Plain Veelgestelde vragen over pagina 's
* Veelgestelde vragen over pagina's met koppelingen
* Pagina met veelgestelde vragen met een start pagina voor onderwerpen

### <a name="plain-faq-pages"></a>Plain Veelgestelde vragen over pagina 's

Dit is de meest voorkomende type van de pagina met veelgestelde vragen, waarin de antwoorden onmiddellijk de vragen in dezelfde pagina volgen.

Hieronder volgt een voorbeeld van een eenvoudige pagina met veelgestelde vragen:

![Plain Veelgestelde vragen over pagina-voorbeeld voor een knowledge base](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>Veelgestelde vragen over pagina's met koppelingen

In dit type pagina met veelgestelde vragen, vragen bij elkaar worden opgeteld en antwoorden die in andere gedeelten van dezelfde pagina of in verschillende pagina's zijn gekoppeld.

Hieronder volgt een voorbeeld van een pagina met koppelingen in de secties die zich op dezelfde pagina met veelgestelde vragen:

 ![Voorbeeld van sectie koppeling Veelgestelde vragen over de pagina voor een knowledge base](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>Pagina bovenliggende onderwerpen koppelingen naar pagina's met onderliggende antwoorden

Dit type Veelgestelde vragen bevat een pagina onderwerpen waarin elk onderwerp wordt gekoppeld aan een bijbehorende set vragen en antwoorden op een andere pagina. QnA Maker verkent alle gekoppelde pagina's om de bijbehorende vragen & antwoorden uit te pakken.

Hieronder ziet u een voor beeld van een pagina met onderwerpen met koppelingen naar secties met veelgestelde vragen op verschillende pagina's.

 ![Voorbeeld van veelgestelde vragen over de dieptekoppeling naar de pagina voor een knowledge base](./media/qnamaker-concepts-datasources/topics-faq.png)

## <a name="support-urls"></a>Url's voor ondersteuning

QnA Maker kunt semi-gestructureerde ondersteunings webpagina's verwerken, zoals webartikelen waarin wordt beschreven hoe u een bepaalde taak uitvoert, hoe u een bepaald probleem opspoort en oplost en wat de aanbevolen procedures voor een bepaald proces zijn. Extractie werkt het beste voor inhoud die een duidelijke structuur met hiërarchische koppen heeft.

> [!NOTE]
> Uitpakken voor ondersteunings artikelen is een nieuwe functie en is in vroege stadia. Het werkt het beste voor eenvoudige pagina's, die goed zijn gestructureerd en geen complexe kop-en voet teksten bevatten.

![QnA Maker ondersteunt de extractie van semi-gestructureerde webpagina's waarbij een duidelijke structuur wordt weer gegeven met hiërarchische koppen](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)