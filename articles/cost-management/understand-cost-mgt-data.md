---
title: Informatie over Azure Cost Management gegevens | Microsoft Docs
description: Dit artikel helpt u bij het beter begrijpen van gegevens die zijn opgenomen in Azure Cost Management en hoe vaak deze worden verwerkt, verzameld, weer gegeven en gesloten.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 681ccc768b1fa3d5a968847d11987fbd83898b59
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721366"
---
# <a name="understand-cost-management-data"></a>Inzicht in gegevens van Cost Management

Dit artikel helpt u beter inzicht te krijgen in de kosten en gebruiks gegevens van Azure die zijn opgenomen in Azure Cost Management. Hierin wordt uitgelegd hoe vaak gegevens worden verwerkt, verzameld, weer gegeven en gesloten. U wordt maandelijks gefactureerd voor Azure-gebruik. Hoewel facturerings cycli maandelijkse Peri Oden zijn, variëren de begin-en eind datum van de cyclus per abonnements type. Hoe vaak Cost Management het ontvangen van gebruiks gegevens verschilt, is afhankelijk van verschillende factoren. Deze factoren omvatten hoe lang het duurt om de gegevens te verwerken en hoe vaak Azure-Services het gebruik naar het facturerings systeem verzenden.

Cost Management omvat alle gebruik en aankopen, inclusief reserve ringen en aanbiedingen van derden voor Enterprise Agreement (EA). Micro soft-gebruikers overeenkomst accounts en individuele abonnementen met betalen per gebruik-tarieven omvatten alleen verbruik van Azure-en Marketplace-Services. Ondersteuning en andere kosten zijn niet inbegrepen. Kosten worden geschat tot er een factuur wordt gegenereerd en er wordt geen rekening gehouden met de credit bedragen.

## <a name="supported-microsoft-azure-offers"></a>Ondersteunde Microsoft Azure aanbiedingen

De volgende informatie bevat de momenteel ondersteunde [Microsoft Azure aanbiedingen](https://azure.microsoft.com/support/legal/offer-details/) in azure Cost Management. Een Azure-aanbieding is het type Azure-abonnement dat u hebt. Gegevens zijn beschikbaar in Cost Management vanaf de datum **van de beschik bare gegevens** . Als een abonnement wordt gewijzigd, zijn de kosten voor de wijzigings datum van de aanbieding niet beschikbaar.

| **Categorie**  | **Naam van aanbieding** | **Quotum-ID** | **Aanbiedings nummer** | **Beschik bare gegevens van** |
| --- | --- | --- | --- | --- |
| **Azure Government** | Azure Government Enter prise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | Mei 2014<sup>1</sup> |
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

| Category  | **Naam van aanbieding** | **Quotum-ID** | **Aanbiedings nummer** |
| --- | --- | --- | --- |
| **Azure Duitsland** | [Azure Duitsland-betalen per gebruik](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
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

**Metagegevensarchiefmethode**

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

## <a name="cost-management-data-fields"></a>Cost Management gegevens velden

De volgende gegevens velden zijn te vinden in detail bestanden voor gebruik en Cost Management-Api's. Voor de volgende vetgedrukte velden kunnen partners filteren en groeperen op functies in Cost Analysis gebruiken om kosten te analyseren op basis van meerdere velden. Vetgedrukte velden zijn alleen van toepassing op micro soft-klant overeenkomsten die door partners worden ondersteund.

| **Veld naam** | **Beschrijving** |
| --- | --- |
| InvoiceId | Factuur-ID die wordt weer gegeven op de factuur voor de specifieke trans actie. |
| previousInvoiceID | Verwijzing naar een oorspronkelijke factuur er is een restitutie (negatieve kosten). Alleen ingevuld als er een restitutie is. |
| billingAccountName | De naam van het facturerings account dat de partner voor stelt. Alle kosten worden in rekening gebracht voor alle klanten die hebben gekantt aan een micro soft-klant overeenkomst en de CSP-klanten die recht hebben op aankopen als SaaS, Azure Marketplace en reserve ringen. |
| billingAccountID | De id voor het facturerings account dat de partner voor stelt. |
| billingProfileID | De id voor het facturerings profiel waarmee de kosten voor facturen worden gegroepeerd in een enkele facturerings valuta voor klanten die hebben gereageerd op een micro soft-klant overeenkomst en de CSP-klanten die recht hebben op aankopen als SaaS, Azure Marketplace en ringen. |
| billingProfileName | De naam van het facturerings profiel waarmee de kosten voor facturen worden gegroepeerd in een enkele facturerings valuta voor de klanten die een micro soft-klant overeenkomst hebben opgedaan en de CSP-klanten die recht hebben op aankopen als SaaS, Azure Marketplace en ringen. |
| invoiceSectionName | De naam van het project dat in de factuur wordt gefactureerd. Niet van toepassing op micro soft-klant overeenkomsten die worden uitgevoerd door partners. |
| invoiceSectionID | De id van het project dat in de factuur wordt gefactureerd. Niet van toepassing op micro soft-klant overeenkomsten die worden uitgevoerd door partners. |
| **CustomerTenantID** | De id van de Azure Active Directory Tenant van het&#39;abonnement van de klant. |
| **CustomerName** | De naam van de Azure Active Directory Tenant voor het&#39;abonnement van de klant s. |
| **CustomerTenantDomainName** | De domein naam voor de Azure Active Directory-Tenant van&#39;het abonnement van de klant s. |
| **PartnerTenantID** | De id voor de&#39;partner-Azure Active Directory Tenant. |
| **PartnerName** | De naam van de partner Azure Active Directory Tenant. |
| **ResellerMPNID** | MPNID voor de wederverkoper die aan het abonnement is gekoppeld. |
| costCenter | Kosten plaats die aan het abonnement is gekoppeld. |
| billingPeriodStartDate | De begin datum van de facturerings periode, zoals op de factuur wordt weer gegeven. |
| billingPeriodEndDate | De eind datum van de facturerings periode, zoals op de factuur wordt weer gegeven. |
| servicePeriodStartDate | De begin datum voor de beoordelings periode wanneer het service verbruik is beoordeeld op kosten. De prijzen voor Azure-Services worden bepaald voor de beoordelings periode. |
| servicePeriodEndDate | Eind datum voor de periode waarin het service verbruik is beoordeeld op kosten. De prijzen voor Azure-Services worden bepaald op basis van de waarderings periode. |
| date | Voor Azure-verbruiks gegevens wordt de datum van gebruik weer gegeven als geclassificeerd. Voor een gereserveerde instantie wordt de aangeschafte datum weer gegeven. Voor terugkerende kosten en eenmalige kosten, zoals Marketplace en ondersteuning, wordt de aankoop datum weer gegeven. |
| productID | Id voor het product dat de transitorische kosten per verbruik of aankoop heeft. Het is de aaneengeschakelde sleutel van productID en SKuID, zoals wordt weer gegeven in het partner centrum. |
| product | Naam van het product met transitorische kosten per verbruik of aankoop, zoals op de factuur wordt weer gegeven. |
| serviceFamily | Toont de service familie voor het product dat is gekocht of in rekening wordt gebracht. Bijvoorbeeld Storage of compute. |
| productOrderID | De id van de Asset of Azure-plan naam waarvan het abonnement deel uitmaakt. Bijvoorbeeld Azure-abonnement. |
| productOrderName | De naam van het Azure-abonnement waarvan het abonnement deel uitmaakt. Bijvoorbeeld Azure-abonnement. |
| consumedService | Verbruikte service (verouderde taxonomie) zoals gebruikt in verouderde EA-gebruiks gegevens. |
| meterId | Gecontroleerde id voor het gemeten verbruik. |
| meterName | Hiermee wordt de naam van de meter voor gemeten verbruik geïdentificeerd. |
| meterCategory | Identificeert de service op het hoogste niveau voor gebruik. |
| meterSubCategory | Hiermee wordt het type of de subcategorie van de Azure-service gedefinieerd die van invloed kan zijn op het aantal. |
| meterRegion | De datacenterlocatie voor bepaalde services waarbij de prijs is gebaseerd op de datacenterlocatie. |
| subscription ID | De unieke door micro soft gegenereerde id voor het Azure-abonnement. |
| subscriptionName | De naam van het Azure-abonnement. |
| Termijn | Hiermee wordt de geldigheidstermijn van de aanbieding weergegeven. Gereserveerde instanties tonen bijvoorbeeld 12 maanden van een jaar van de gereserveerde instantie. Voor eenmalige aankopen of terugkerende aankopen wordt voor de periode één maand voor SaaS, Azure Marketplace en ondersteuning weer gegeven. Niet van toepassing op Azure-verbruik. |
| publisherType (firstParty, thirdPartyReseller, thirdPartyAgency) | Type uitgever die de uitgever identificeert als eerste partij, wederverkoper van derden of Bureau van derden. |
| PartNumber | Onderdeel nummer voor het niet-gebruikte gereserveerde exemplaar en Azure Marketplace-Services. |
| publisherName | De naam van de uitgever van de service, inclusief micro soft-of uitgevers van derden. |
| reservationId | De id voor de gereserveerde instantie aankoop. |
| reservationName | De naam van de gereserveerde instantie. |
| reservationOrderId | OrderID voor het gereserveerde exemplaar. |
| frequency | De betalings frequentie voor een gereserveerde instantie. |
| resourceGroup | De naam van de Azure-resource groep die wordt gebruikt voor levenscyclus resource beheer. |
| instanceID (of) ResourceID | De id van het resource-exemplaar. |
| resourceLocation | De naam van de resource locatie. |
| Locatie | De genormaliseerde locatie van de resource. |
| effectivePrice | De werkelijke eenheids prijs van de service, in de valuta prijs. Uniek voor een product, een service familie, een meter en een aanbieding. Wordt gebruikt met prijzen in het prijs overzicht voor het facturerings account. Als er gelaagde prijzen of een inbegrepen hoeveelheid zijn, wordt de overvloei prijs voor verbruik weer gegeven. |
| Aantal | Gemeten hoeveelheid die is gekocht of verbruikt. De hoeveelheid meter die wordt gebruikt tijdens de facturerings periode. |
| unitOfMeasure | Hiermee wordt de eenheid geïdentificeerd waarin de service wordt gefactureerd. Bijvoorbeeld GB en uren. |
| pricingCurrency | De valuta waarin de eenheids prijs wordt gedefinieerd. |
| billingCurrency | De valuta waarin de gefactureerde kosten worden gedefinieerd |
| chargeType | Definieert het type kosten dat de kosten vertegenwoordigen in Azure Cost Management zoals aankoop en terugbetaling. |
| costinBillingCurrency | ExtendedCost of overvloei kosten vóór belasting in de gefactureerde valuta. |
| costinPricingCurrency | ExtendedCost of overvloei kosten voor BTW in de prijs valuta om te correleren met prijzen. |
| **costinUSD** | Geschatte ExtendedCost of overvloei kosten vóór belasting in USD. |
| **paygCostInBillingCurrency** | Hiermee worden de kosten weer gegeven als de prijzen een verkoop prijs zijn. Hiermee worden de prijzen voor betalen naar gebruik in de facturerings valuta weer gegeven. Alleen beschikbaar voor RBAC-bereiken. |
| **paygCostInUSD** | Hiermee worden de kosten weer gegeven als de prijzen een verkoop prijs zijn. Toont de prijzen voor betalen naar gebruik in USD. Alleen beschikbaar voor RBAC-bereiken. |
| exchangeRate | De wissel koers die wordt gebruikt om de prijs valuta om te zetten in de facturerings valuta. |
| exchangeRateDate | De datum voor de wissel koers die&#39;wordt gebruikt om van de prijs valuta te converteren naar de facturerings valuta. |
| isAzureCreditEligible | Geeft aan of de kosten in aanmerking komen voor betaling door Azure-tegoed. |
| serviceInfo1 | Verouderd veld waarin optionele servicespecifieke meta gegevens worden vastgelegd. |
| serviceInfo2 | Verouderd veld waarin optionele servicespecifieke meta gegevens worden vastgelegd. |
| additionalInfo | Servicespecifieke metagegevens. Bijvoorbeeld een installatiekopie voor een virtuele machine. |
| tags | Label dat u toewijst aan de meter. Tags gebruiken om facturerings records te groeperen. U kunt bijvoorbeeld Tags gebruiken om kosten te verdelen door de afdeling die gebruikmaakt van de meter. |
| **partnerEarnedCreditRate** | Het tarief van de korting wordt toegepast als er een door de partner aangehaalde credit (PEC) is op basis van de toegang tot de partner beheerder. |
| **partnerEarnedCreditApplied** | Geeft aan of het tegoed van de partner is toegepast. |


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

- Als u de eerste Snelstartgids voor Cost Management nog niet hebt voltooid, kunt u deze lezen bij het analyseren van de [kosten](quick-acm-cost-analysis.md).
