---
title: Informatie over Azure Cost Management gegevens | Microsoft Docs
description: Dit artikel helpt u bij het beter begrijpen van gegevens die zijn opgenomen in Azure Cost Management en hoe vaak deze worden verwerkt, verzameld, weer gegeven en gesloten.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: bf3c2224361cd2890a0b8b4686506eac6858d9cb
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122997"
---
# <a name="understand-cost-management-data"></a>Inzicht in gegevens van Cost Management

Dit artikel helpt u beter inzicht te krijgen in de kosten en gebruiks gegevens van Azure die zijn opgenomen in Azure Cost Management. Hierin wordt uitgelegd hoe vaak gegevens worden verwerkt, verzameld, weer gegeven en gesloten. U wordt maandelijks gefactureerd voor Azure-gebruik. Hoewel facturerings cycli maandelijkse Peri Oden zijn, variëren de begin-en eind datum van de cyclus per abonnements type. Hoe vaak Cost Management het ontvangen van gebruiks gegevens verschilt, is afhankelijk van verschillende factoren. Deze factoren omvatten hoe lang het duurt om de gegevens te verwerken en hoe vaak Azure-Services het gebruik naar het facturerings systeem verzenden.

Cost Management omvat alle gebruik en aankopen, inclusief reserve ringen en aanbiedingen van derden voor Enterprise Agreement (EA). Micro soft-gebruikers overeenkomst accounts en individuele abonnementen met betalen per gebruik-tarieven omvatten alleen verbruik van Azure-en Marketplace-Services. Ondersteuning en andere kosten zijn niet inbegrepen. Kosten worden geschat tot er een factuur wordt gegenereerd en er wordt geen rekening gehouden met de credit bedragen.

## <a name="supported-microsoft-azure-offers"></a>Ondersteunde Microsoft Azure aanbiedingen

De volgende informatie bevat de momenteel ondersteunde [Microsoft Azure aanbiedingen](https://azure.microsoft.com/support/legal/offer-details/) in azure Cost Management. Een Azure-aanbieding is het type Azure-abonnement dat u hebt. Gegevens zijn beschikbaar in Cost Management vanaf de datum **van de beschik bare gegevens** . Als een abonnement wordt gewijzigd, zijn de kosten voor de wijzigings datum van de aanbieding niet beschikbaar.

| **Categorie**  | **Naam van aanbieding** | **Quotum-ID** | **Aanbiedings nummer** | **Beschik bare gegevens van** |
| --- | --- | --- | --- | --- |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | Mei 2014<sup>1</sup> |
| **Enterprise Agreement (EA)** | Enterprise Dev/Test                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | Mei 2014<sup>1</sup> |
| **Enterprise Agreement (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | Mei 2014<sup>1</sup> |
| **Microsoft-klantovereenkomst** | [Microsoft Azure plan](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | N.v.t. | <sup>3</sup> maart 2019 |
| **Microsoft-klantovereenkomst** | [Microsoft Azure plan voor dev/test](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | N.v.t. | <sup>3</sup> maart 2019 |
| **Micro soft-klant overeenkomst ondersteund door partners** | Microsoft Azure Plan | CSP_2015-05-01, CSP_MG_2017-12-01 en CSPDEVTEST_2018-05-01<br><br>De quotum-ID wordt opnieuw gebruikt voor micro soft-klanten overeenkomst en verouderde CSP-abonnementen. Op dit moment worden alleen micro soft-klanten overeenkomst abonnementen ondersteund. | N.v.t. | Oktober 2019 |
| **Micro soft Developer Network (MSDN)** | [MSDN platforms](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2 oktober 2018<sup>2</sup> |
| **Betalen per gebruik** | [Betalen per gebruik](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2 oktober 2018<sup>2</sup> |
| **Betalen per gebruik** | [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2 oktober 2018<sup>2</sup> |
| **Betalen per gebruik** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | 2 oktober 2018<sup>2</sup> |
| **Betalen per gebruik** | [Gratis proef versie](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2 oktober 2018<sup>2</sup> |
| **Betalen per gebruik** | [Azure in open](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2 oktober 2018<sup>2</sup> |
| **Betalen per gebruik** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P-MS-AZR-0125P, MS-AZR-0128P-MS-AZR-0130P | 2 oktober 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enter prise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2 oktober 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2 oktober 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2 oktober 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2 oktober 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enter prise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2 oktober 2018<sup>2</sup> |

_<sup>**1**</sup> voor gegevens vóór 2014 mei gaat u naar de [Azure Enter prise Portal](https://ea.azure.com)._

_<sup>**2**</sup> voor gegevens vóór 2 oktober 2018 gaat u naar de [Azure-Accountcentrum](https://account.azure.com/subscriptions)._

_<sup>**3**</sup> micro soft-klant overeenkomsten zijn gestart in maart 2019 en bevatten vóór dit punt geen historische gegevens._

_<sup>**4**</sup> historische gegevens voor tegoed-en kasvoorschot abonnementen komen mogelijk niet overeen met je factuur. Zie [historische gegevens komen mogelijk niet overeen met de factuur](#historical-data-might-not-match-invoice) hieronder._

De volgende aanbiedingen worden nog niet ondersteund:

| Categorie  | **Naam van aanbieding** | **Quotum-ID** | **Aanbiedings nummer** |
| --- | --- | --- | --- |
| **Azure Duitsland** | [Azure Duitsland-betalen per gebruik](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Government** | Azure Government Pay-As-You-Go | PayAsYouGo_2014-09-01 | MS-AZR-USGOV-0003P |
| **Cloud Solution Provider (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **Cloud Solution Provider (CSP)** | Azure Government CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **Cloud Solution Provider (CSP)** | Azure Duitsland in CSP voor Microsoft Cloud Duitsland   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Betalen per gebruik**                 | Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Betalen per gebruik** | [Azure for students](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **Betalen per gebruik**                 | [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Ondersteuningsplannen** | Standard Support                    | Default_2014-09-01 | MS-AZR-0041P |
| **Ondersteuningsplannen** | Ondersteuning voor Professional Direct         | Default_2014-09-01 | MS-AZR-0042P |
| **Ondersteuningsplannen** | Ondersteuning voor ontwikkel aars                   | Default_2014-09-01 | MS-AZR-0043P |
| **Ondersteuningsplannen** | Duitsland-ondersteunings plan                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Ondersteuningsplannen** | Azure Government Standard Support   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Ondersteuningsplannen** | Azure Government Pro-direct-ondersteuning | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Ondersteuningsplannen** | Azure Government Developer Support  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

## <a name="determine-your-offer-type"></a>Uw aanbiedings type bepalen
Als u geen gegevens voor een abonnement ziet en u wilt bepalen of uw abonnement onder de ondersteunde aanbiedingen valt, kunt u controleren of uw abonnement wordt ondersteund. Als u wilt valideren dat een Azure-abonnement wordt ondersteund, meldt u zich aan bij de [Azure Portal](https://portal.azure.com). Selecteer vervolgens **alle services** in het linkerdeel venster. Selecteer in de lijst met Services **abonnementen**. Klik in het menu abonnements lijst op het abonnement dat u wilt controleren. Uw abonnement wordt weer gegeven op het tabblad Overzicht en u kunt de ID van de **aanbieding** en de **aanbieding**bekijken. In de volgende afbeelding ziet u een voorbeeld.

![Voor beeld van het tabblad Overzicht van het abonnement met de ID aanbieding en aanbieding](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Kosten opgenomen in Cost Management

In de volgende tabellen worden de gegevens weer gegeven die zijn opgenomen of zich niet bevinden in Cost Management. Alle kosten worden geschat totdat er een factuur wordt gegenereerd. De weer gegeven kosten omvatten geen gratis en vooruitbetaalde tegoeden.

**Kosten-en gebruiks gegevens**

| **Omvat** | **Niet inbegrepen** |
| --- | --- |
| Azure-service gebruik<sup>5</sup>        | Ondersteunings kosten-Zie [factuur voorwaarden](../billing/billing-understand-your-invoice.md)voor meer informatie. |
| Marketplace met gebruiks versie<sup>6</sup> | Belastingen: Zie [factuur voorwaarden](../billing/billing-understand-your-invoice.md)voor meer informatie. |
| Marketplace-aankopen<sup>6</sup>      | Tegoed-Zie [factuur voorwaarden](../billing/billing-understand-your-invoice.md)voor meer informatie. |
| Reserverings aankopen<sup>7</sup>      |  |
| Afschrijving van reserve ringen<sup>7</sup>      |  |

_<sup>**5**</sup> Azure-service gebruik is gebaseerd op de reserve ring en de overeengekomen prijzen._

_<sup>**6**</sup> Marketplace-aankopen zijn op dit moment niet beschikbaar voor betalen per gebruik, MSDN en Visual Studio._

_<sup>**7**</sup> reserve ringen zijn op dit moment alleen beschikbaar voor Enterprise Agreement (EA)-accounts._

**Metadata**

| **Omvat** | **Niet inbegrepen** |
| --- | --- |
| Resource tags<sup>8</sup> | Tags voor de resource groep |

_<sup>**8**</sup> resource Tags worden toegepast wanneer het gebruik van elke service wordt verzonden en niet met terugwerkende kracht naar historisch gebruik kan worden gesteld._

## <a name="rated-usage-data-refresh-schedule"></a>Vernieuwings schema gebruiks gegevens

Kosten-en gebruiks gegevens zijn beschikbaar in Cost Management en facturering in de Azure Portal en [ondersteunende api's](index.yml). Houd rekening met de volgende punten als u de kosten bekijkt:

- Geschatte kosten voor de huidige facturerings periode worden zes keer per dag bijgewerkt.
- Geschatte kosten voor de huidige facturerings periode kunnen veranderen naarmate u meer gebruikt.
- Elke update is cumulatief en bevat alle regel items en informatie van de vorige update.
- Azure voltooit of _sluit_ de huidige facturerings periode tot 72 uur (drie kalender dagen) nadat de facturerings periode is beëindigd.

In de volgende voor beelden ziet u hoe facturerings perioden kunnen worden beëindigd.

Enterprise Agreement (EA)-abonnementen: als de facturerings maand eindigt op 31 maart, worden de geschatte kosten later tot 72 uur bijgewerkt. In dit voor beeld wordt door middernacht (UTC) 4 april.

Betalen per gebruik-abonnementen: als de facturerings maand eindigt op 15 mei, worden de geschatte kosten mogelijk later bijgewerkt tot 72 uur. In dit voor beeld wordt door middernacht (UTC) 19 mei.

### <a name="rerated-data"></a>Opnieuw geclassificeerde gegevens

Of u de [Cost Management-api's](index.yml), Power bi of de Azure Portal gebruikt om gegevens op te halen, de kosten van de huidige facturerings periode moeten worden geclassificeerd en daarom worden gewijzigd totdat de factuur is gesloten.

## <a name="usage-data-update-frequency-varies"></a>De update frequentie van gebruiks gegevens varieert

De beschik baarheid van uw gemaakte gebruiks gegevens in Cost Management is afhankelijk van een aantal factoren, waaronder:

- Hoe vaak Azure-Services (zoals opslag, compute, CDN en SQL) het gebruik van het verzend proces.
- De tijd die nodig is om de gebruiks gegevens te verwerken met behulp van de waarderings engine en de kosten beheer pijplijnen.

Sommige services verzenden het gebruik vaker dan andere. Het is dus mogelijk dat de gegevens in Cost Management voor sommige services eerder dan andere services worden weer gegeven die minder vaak gegevens verzenden. Normaal gesp roken duurt het gebruik voor Services 8-24 uur in Cost Management. Houd er rekening mee dat gegevens voor een open maand worden vernieuwd wanneer u meer gebruik maakt omdat updates cumulatief zijn.

## <a name="historical-data-might-not-match-invoice"></a>Historische gegevens komen mogelijk niet overeen met factuur

Historische gegevens voor aanbiedingen op basis van tegoed en voor uitbetalingen kunnen mogelijk niet overeenkomen met uw factuur. Bij sommige aanbiedingen van Azure betalen per gebruik, MSDN en Visual Studio kunnen Azure-tegoeden en geavanceerde betalingen op de factuur worden toegepast. De historische gegevens die in Cost Management worden weer gegeven, zijn echter alleen gebaseerd op uw geschatte verbruiks kosten. Cost Management historische gegevens bevatten geen betalingen en tegoeden. Als gevolg hiervan komen de historische gegevens die worden weer gegeven voor de volgende aanbiedingen mogelijk niet exact overeen met uw factuur.

- Azure for students (MS-AZR-0170P)
- Azure in Open (MS-AZR-0111P)
- Azure Pass (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Gratis proef versie (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>Zie ook

- Als u dit nog niet hebt al de eerste snelstartgids voltooid voor kostenbeheer, lezen via [kosten analyseren](quick-acm-cost-analysis.md).
