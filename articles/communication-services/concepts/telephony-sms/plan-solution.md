---
title: Uw Azure Communication Services-oplossing voor telefonie en sms plannen
titleSuffix: An Azure Communication Services concept document
description: Meer informatie over hoe u uw gebruik van telefoonnummers en telefonie effectief kunt plannen.
author: stkozak
manager: rampras
services: azure-communication-services
ms.author: stkozak
ms.date: 06/23/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 39f88ab8b735438f60d8e20513ea5cbda43d41ee
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943684"
---
# <a name="plan-your-telephony-and-sms-solution"></a>Uw oplossing voor telefonie en sms plannen

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

In dit document worden de verschillende telefonie-abonnementen en nummertypen beschreven die Azure Communication Services te bieden heeft. We overlopen de beslissingsstromen om u te helpen een provider voor spraakoproepen, telefoonnummertypen, abonnementen en opties die beschikbaar zijn via Communication Services te selecteren.

## <a name="about-phone-numbers-in-azure-communications-services"></a>Over telefoonnummers in Azure Communication Services

Met Azure Communication Services kunt u telefoonnummers gebruiken om telefoongesprekken en sms-berichten te doen en te ontvangen. Deze telefoonnummers kunnen gebruikt worden om nummerweergave te configureren voor uitgaande oproepen door uw service.
  
Als u geen bestaand telefoonnummer hebt om te importeren in uw Communication Services-oplossing, dan kunt u best beginnen met een nieuw telefoonnummer van Azure Communication Services. Dit neemt slechts enkele minuten in beslag.

Als u een bestaand telefoonnummer hebt dat u in uw oplossing wilt blijven gebruiken (bijvoorbeeld 1-800-BEDRIJF), dan kunt u het telefoonnummer van de bestaande provider naar Communication Services overzetten.

Het volgende diagram helpt u om de beschikbare opties te vergelijken:

:::image type="content" source="../media/plan-solution/decision-tree-basic.png" alt-text="Diagram waarop wordt getoond hoe u een beslissing kunt nemen over uw telefoonnummers.":::

Laten we de typen telefoonnummers en de beschikbare mogelijkheden met Communication Services bekijken. 

## <a name="microsoft-direct-offer-of-phone-numbers-and-capabilities"></a>Directe aanbieding Microsoft met telefoonnummers en mogelijkheden

Azure Communication Services biedt een uitstekende flexibiliteit voor ontwikkelaars. Voor de meeste telefoonnummers kunt u een reeks abonnementen 'à la carte' configureren. Sommige ontwikkelaars hebben enkel een abonnement voor inkomende oproepen nodig. Andere kiezen misschien liever voor inkomende oproepen en uitgaande sms-berichten. Deze abonnementen kunnen worden geselecteerd wanneer u uw telefoonnummers huurt en/of overzet in Communication Services.

De beschikbare abonnementen zijn afhankelijk van het land en type telefoonnummer dat u gebruikt. In het onderstaande diagram ziet u de beslissingsstroom:    De beschikbare abonnementen zijn afhankelijk van het land en type telefoonnummer dat u gebruikt.

<!-- Tami/team have rejected this multiple times despite updates, says it needs to be higher res - need to work with her to get approval for this image. Commenting out to move our staging forward. :::image type="content" source="../../media/example-decision-flow.png" alt-text="Example for the decision flow"::: -->

Voordat u een type telefoonnummer kunt selecteren, bekijken we het internationaal nummerplan.

### <a name="optional-reading-international-public-telecommunication-numbering-plan-e164"></a>Optionele informatie. Internationaal openbaar nummerplan voor telecommunicatie (E.164)

> [!NOTE]
> We raden u aan om deze informatie na te lezen, zelfs als u vertrouwd bent met het E.164-nummerplan, om beter de verschillende nummertypen en mogelijkheden aangeboden door Azure Communication Services te begrijpen.

Het internationaal openbaar nummerplan voor telecommunicatie is vastgelegd in de aanbeveling E.164 van de International Telecommunication Union (ITU). Nummers die hieraan voldoen zijn maximaal 15 cijfers lang.

Het telefoonnummer bestaat uit

-   Een prefix '+'
-   Een internationaal oproepprefix of land-/regiocode (één, twee of drie cijfers) 
-   *(Optioneel)* een nationale bestemmingscode of nummerplan, meestal een netnummer genoemd. De lengte van dit nummer is afhankelijk van het land. In de Verenigde Staten is dit drie cijfers. In Australië en Nieuw-Zeeland is het één cijfer. Duitsland, Japan, Mexico en andere landen hebben netnummers met variabele lengte. In Duitsland kan het netnummer bijvoorbeeld een willekeurige waarde van twee tot vijf cijfers lang kan zijn, terwijl het in Japan tussen één en vijf cijfers lang kan zijn.
-   Een abonneenummer

> [!NOTE]
> De bovenstaande classificatie voldoet niet aan de E. 164-norm van de ITU en dient als vereenvoudigde beschrijving. Het abonneenummer is in de norm bijvoorbeeld opgedeeld. Als u meer wilt leren over internationale nummerplannen, dan begint u best bij de [E.164-norm van ITU](https://www.itu.int/rec/T-REC-E.164).  

Hier zijn enkele voorbeelden om het nummerplan beter te begrijpen:

Een regionaal telefoonnummer in de Verenigde Staten:

:::image type="content" source="../media/plan-solution/regional-us.png" alt-text="Voorbeeld van een regionaal telefoonnummer in de VS":::

Een regionaal telefoonnummer in Canada:

:::image type="content" source="../media/plan-solution/regional-canada.png" alt-text="Voorbeeld van een regionaal telefoonnummer in Canada":::

Een gratis nummer in Noord-Amerika:

:::image type="content" source="../media/plan-solution/tollfree-us.png" alt-text="Voorbeeld van een gratis nummer in Noord-Amerika":::

Een mobiel telefoonnummer in het VK:

:::image type="content" source="../media/plan-solution/mobile-uk.png" alt-text="Voorbeeld van een mobiel telefoonnummer in het VK":::

Laten we vervolgens specifieke typen telefoonnummers bekijken die beschikbaar zijn in Azure Communication Services.

## <a name="phone-number-types-in-azure-communication-services"></a>Typen telefoonnummers in Azure Communication Services

Microsoft biedt regionale en gratis nummerplannen aan voor short codes en mobiele nummers in toepasselijke landen.

In de volgende tabel vindt u een overzicht van deze typen telefoonnummers: 

| Type telefoonnummer | Voorbeeld                              | Beschikbaarheid land    | Algemeen scenario                                                                                                     |
| ----------------- | ------------------------------------ | ----------------------- | ------------------------------------------------------------------------------------------------------------------- |
| Regionaal          | \+ 1 (geografisch netnummer) XXX XX XX  | VS, Canada, Puerto Rico | Telefoonnummers toewijzen aan gebruikers in uw toepassingen of toewijzen aan IVR-systemen (Interactive Voice Response)/bots. |
| Gratis         | \+ 1 (gratis *netnummer*) XXX XX XX | VS, Canada, Puerto Rico | Toewijzen aan IVR-systemen (Interactive Voice Response)/bots, sms-toepassingen                                        |

## <a name="plans"></a>Abonnementen 

Laten we eens kijken naar de mogelijkheden die u kunt gebruiken voor uw telefoonnummers. Deze mogelijkheden verschillen per land als gevolg van wettelijke vereisten. Azure Communication Services biedt de volgende mogelijkheden:

- **Uitgaande sms**, handig voor meldingen en tweeledige verificatie.
- **Inkomende en uitgaande sms**, een vooraf gedefinieerd pakket waarmee het sms-bericht kan worden verzonden en ontvangen als onderdeel van één abonnement.
- **PSTN-oproepen**, u kunt inkomende oproepen selecteren en de nummerweergave gebruiken om uitgaande oproepen te doen.

## <a name="next-steps"></a>Volgende stappen

### <a name="quickstarts"></a>Quickstarts

- [Een telefoonnummer aanvragen](../../quickstarts/telephony-sms/get-phone-number.md)
- [Een oproep doen](../../quickstarts/voice-video-calling/calling-client-samples.md)
- [Een sms verzenden](../../quickstarts/telephony-sms/send.md)

### <a name="conceptual-documentation"></a>Conceptuele documentatie

- [Spraak- en videoconcepten](../voice-video-calling/about-call-types.md)
- [Oproepstromen en sms-stromen](../call-flows.md)
- [Prijzen](../pricing.md)
