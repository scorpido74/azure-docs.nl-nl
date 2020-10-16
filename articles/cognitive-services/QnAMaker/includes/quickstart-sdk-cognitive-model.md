---
ms.openlocfilehash: c87b9dc22ecd937abb27417aeddc1e30c0d724e7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "85114519"
---

## <a name="using-this-example-knowledge-base"></a>Deze voorbeeld-Knowledge Base gebruiken

De Knowledge Base in deze quickstart begint met twee vraag-en-antwoordparen. Dit is met opzet gedaan om het voorbeeld te vereenvoudigen en om zeer voorspelbare id's te gebruiken in de bijwerkmethode, waarmee u vervolgprompts met vragen kunt koppelen aan nieuwe paren. Dit is gepland en geïmplementeerd in een specifieke volgorde voor deze quickstart.

Als u van plan bent om uw Knowledge Base in de loop der tijd te ontwikkelen met vervolgprompts die afhankelijk zijn van bestaande vraag-en-antwoordparen, kunt u kiezen uit de volgende mogelijkheden:
* Voor grotere Knowledge Bases beheert u de Knowledge Base in een teksteditor of een hulpprogramma met door tabs gescheiden waarden dat automatisering ondersteunt, en vervangt u de KB vervolgens volledig door een update.
* Voor kleinere Knowledge Bases beheert u de vervolgprompts volledig in de QnA Maker-portal.

Details over de vraag-en-antwoordparen die worden gebruikt in deze quickstart:
* Typen vraag-en-antwoordparen: na de update zijn er twee typen vraag-en-antwoordparen in deze Knowledge Base: chitchat en domein-specifieke informatie. Dit is gebruikelijk als uw Knowledge Base is gekoppeld aan een conversatietoepassing zoals een chatbot.
* Hoewel de antwoorden in de Knowledge Base kunnen worden gefilterd op metagegevens of er vervolgprompts kunnen worden gebruikt, komt dat niet terug in deze quickstart. Klik [hier](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md) als u taalagnostische voorbeelden van het genereren van antwoorden wilt bekijken.
* Antwoordtekst is markdown en kan [allerlei soorten markdown](../reference-markdown-format.md) bevatten, zoals afbeeldingen (openbaar beschikbare afbeeldingen op internet), koppelingen (naar openbaar beschikbare URL's) en opsommingstekens, maar de variëteit in deze quickstart is beperkt.
