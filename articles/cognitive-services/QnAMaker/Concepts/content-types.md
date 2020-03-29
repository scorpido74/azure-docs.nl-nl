---
title: Inhoudstypen - QnA Maker
description: Inhoudstypen bevatten veel standaard gestructureerde documenten zoals PDF, DOC en TXT.
services: cognitive-services
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 7c78f9ea261fa636cce50b69524802d0900e9d7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650194"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Inhoudstypen documenten die u toevoegen aan een kennisbank
Inhoudstypen bevatten veel standaard gestructureerde documenten zoals PDF, DOC en TXT.

## <a name="file-and-url-data-types"></a>Bestands- en URL-gegevenstypen

In de onderstaande tabel worden de typen inhoud en bestandsindelingen samengevat die worden ondersteund door QnA Maker.

|Brontype|Type inhoud| Voorbeelden|
|--|--|--|
|URL|Veelgestelde vragen<br> (Plat, met secties of met een onderwerpen homepage)<br>Ondersteuningspagina's <br> (Enkele pagina how-to artikelen, het oplossen van problemen artikelen, enz.)|[Gewoon FAQ](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Veelgestelde vragen met links](https://www.microsoft.com/en-us/software-download/faq),<br> [Veelgestelde vragen met onderwerpen homepage](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Ondersteuningsartikel](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF / DOC|Faqs<br> Producthandleiding,<br> Brochures<br> Papier<br> Flyer Beleid,<br> Ondersteuningsgids,<br> Gestructureerde QnA,<br> enz.|**Zonder multi-turn**<br>[Gestructureerde QnA.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Voorbeeld producthandleiding.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Voorbeeld semi-structured.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Voorbeeld whitepaper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br><br>**Multi-turn**:<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso Voordelen (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso-voordelen (pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|*Excel|Gestructureerd QnA-bestand<br> (inclusief RTF, HTML-ondersteuning)|[Voorbeeld van QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|*TXT/TSV|Gestructureerd QnA-bestand|[Proef chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Als u verificatie nodig hebt voor uw gegevensbron, u de volgende methoden overwegen om die inhoud in QnA Maker te krijgen:

* Bestand handmatig downloaden en importeren in QnA Maker
* Bestand toevoegen vanaf geverifieerde [Sharepoint-locatie](../how-to/add-sharepoint-datasources.md)

## <a name="url-content"></a>URL-inhoud

Twee soorten documenten kunnen worden geïmporteerd via **URL** in QnA Maker:

* Veelgestelde URL's
* URL's ondersteunen

Elk type geeft een verwachte indeling aan.

## <a name="file-based-content"></a>Inhoud op basis van bestanden

U bestanden toevoegen aan een kennisbank van een openbare bron of uw lokale bestandssysteem in de [QnA Maker-portal.](https://www.qnamaker.ai)

## <a name="content-format-guidelines"></a>Richtlijnen voor inhoudsindeling

Meer informatie over de [indelingsrichtlijnen](../reference-document-format-guidelines.md) voor de verschillende bestanden.

## <a name="next-steps"></a>Volgende stappen

Begrijp welke informatie wordt opgeslagen in een [vraag- en antwoordset (QnA).](question-answer-set.md)