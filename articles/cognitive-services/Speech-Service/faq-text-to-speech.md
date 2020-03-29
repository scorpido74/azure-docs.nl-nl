---
title: Veelgestelde vragen over tekst naar spraak
titleSuffix: Azure Cognitive Services
description: Krijg antwoorden op de veelgestelde vragen over de tekst-naar-spraakservice.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 19b8be83a3678164197ec0650b07091e941a04d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110508"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Veelgestelde vragen over tekst naar spraak

Als u geen antwoorden op uw vragen vinden in deze veelgestelde vragen, raadpleegt u [andere ondersteuningsopties.](support.md)

## <a name="general"></a>Algemeen

**V: Wat is het verschil tussen een standaard spraakmodel en een aangepast spraakmodel?**

**A**: Het standaardspraakmodel (ook wel _spraaklettertype_genoemd) is getraind met behulp van gegevens die eigendom zijn van Microsoft en is al geïmplementeerd in de cloud. U een aangepast spraakmodel gebruiken om een gemiddeld model aan te passen en het timbre en de expressie van de spraakstijl van de spreker overbrengen of een volledig, nieuw model trainen op basis van de trainingsgegevens die door de gebruiker zijn opgesteld. Tegenwoordig willen steeds meer klanten een unieke, merkstem voor hun bots. Het aangepaste voice-building platform is de juiste keuze voor die optie.

**V: Waar moet ik beginnen als ik een standaard spraakmodel wil gebruiken?**

**A:** Meer dan 80 standaard spraakmodellen in meer dan 45 talen zijn beschikbaar via HTTP-verzoeken. Zorg eerst voor een [abonnementssleutel.](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started) Zie de [REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)voor rustoproepen naar de vooraf geïmplementeerde spraakmodellen.

**V: Als ik een aangepast spraakmodel wil gebruiken, is de API dan hetzelfde als die welke wordt gebruikt voor standaardstemmen?**

**A:** Wanneer een aangepast spraakmodel wordt gemaakt en geïmplementeerd, krijgt u een uniek eindpunt voor uw model. Als u de stem wilt gebruiken om in uw apps te spreken, moet u het eindpunt in uw HTTP-verzoeken opgeven. Dezelfde functionaliteit die beschikbaar is in de REST API voor de tekst-naar-spraakservice is beschikbaar voor uw aangepaste eindpunt. Meer informatie over het [maken en gebruiken van uw aangepaste eindpunt.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-voice-endpoint)

**V: Moet ik de trainingsgegevens voorbereiden om zelf aangepaste spraakmodellen te maken?**

**A:** Ja, u moet de trainingsgegevens zelf voorbereiden op een aangepast spraakmodel.

Een verzameling spraakgegevens is vereist om een aangepast spraakmodel te maken. Deze collectie bestaat uit een set audiobestanden van spraakopnames en een tekstbestand van de transcriptie van elk audiobestand. Het resultaat van uw digitale stem is sterk afhankelijk van de kwaliteit van uw trainingsgegevens. Om een goede tekst-naar-spraakstem te produceren, is het belangrijk dat de opnames worden gemaakt in een rustige kamer met een hoogwaardige, staande microfoon. Een consistente volume-, spreeksnelheid en spreektoonhoogte en zelfs consistentie in expressieve maniertjes van spraak zijn essentieel voor het bouwen van een geweldige digitale stem. We raden ten zeerste aan om de stemmen op te nemen in een opnamestudio.

Momenteel bieden we geen online opnameondersteuning of hebben we aanbevelingen voor opnamestudio's. Zie voor de [indelingsvereiste hoe u opnamen en transcripties voorbereidt.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice-create-voice)

**V: Welke scripts moet ik gebruiken om de spraakgegevens op te nemen voor aangepaste spraaktraining?**

**A:** We beperken de scripts voor spraakopname niet. U uw eigen scripts gebruiken om de toespraak op te nemen. Zorg ervoor dat u voldoende fonetische dekking in uw spraakgegevens. Als u een aangepaste stem wilt trainen, u beginnen met een klein volume spraakgegevens, wat 50 verschillende zinnen (ongeveer 3-5 minuten spraak) kan zijn. Hoe meer gegevens u verstrekt, hoe natuurlijker uw stem zal zijn. U beginnen met het trainen van een volledig spraaklettertype wanneer u opnamen van meer dan 2.000 zinnen (ongeveer 3-4 uur spraak) opgeeft. Om een hoge kwaliteit, volledige stem te krijgen, moet u opnamen van meer dan 6.000 zinnen (ongeveer 8-10 uren van toespraak) voorbereiden.

We bieden aanvullende services om u te helpen scripts voor te bereiden voor opname. Neem contact op met [de klantenservice van Custom Voice](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) voor vragen.

**V: Wat als ik een hogere gelijktijdigheid nodig heb dan de standaardwaarde of wat er in de portal wordt aangeboden?**

**A:** U uw model opschalen in stappen van 20 gelijktijdige aanvragen. Neem contact op met [de klantenservice van Custom Voice](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) voor vragen over hogere schaling.

**V: Kan ik mijn model downloaden en lokaal uitvoeren?**

**A:** Modellen kunnen niet lokaal worden gedownload en uitgevoerd.

**V: Worden mijn verzoeken beperkt?**

**A:** De REST API beperkt aanvragen tot 25 per 5 seconden. Details zijn te vinden in onze pagina's voor [Tekst naar Spraak](text-to-speech.md).

## <a name="next-steps"></a>Volgende stappen

- [Probleemoplossing](troubleshooting.md)
- [Opmerkingen vrijgeven](releasenotes.md)
