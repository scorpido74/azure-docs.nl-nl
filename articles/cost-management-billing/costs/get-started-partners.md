---
title: Aan de slag met Azure Cost Management voor partners
description: In dit artikel wordt uitgelegd hoe partners Azure Cost Management-functies gebruiken en hoe ze Cost Management toegang voor hun klanten mogelijk maken.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: aparnag
ms.custom: secdec18
ms.openlocfilehash: b3e2b6fbfb11c0ee89e56cd29fa3bf606c336235
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278542"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Aan de slag met Azure Cost Management voor partners

Azure Cost Management is standaard beschikbaar voor partners die hun klanten hebben ingewerkt voor een micro soft-klant overeenkomst en [een Azure-abonnement hebben aangeschaft](/partner-center/purchase-azure-plan). In dit artikel wordt uitgelegd hoe partners [Azure Cost Management](../index.yml) -functies gebruiken om de kosten voor abonnementen in het Azure-abonnement weer te geven. Ook wordt beschreven hoe partners Cost Management toegang bieden voor hun klanten. Klanten kunnen Cost Management-functies gebruiken wanneer ze zijn ingeschakeld door hun CSP-partner.

CSP-partners gebruiken Cost Management voor het volgende:

- Inzicht krijgen in gefactureerde kosten en de kosten aan de klant, abonnementen, resource groepen en services koppelen.
- Krijg een intuïtieve weer gave van de kosten [analyse](quick-acm-cost-analysis.md) van Azure met mogelijkheden voor het analyseren van kosten per klant, abonnement, resource groep, resource, meter, service en vele andere dimensies.
- Bekijk de resource kosten die een partner hebben ontvangen (PEC) die in de kosten analyse worden toegepast.
- Stel meldingen en automatisering in met behulp van programmeer [budgetten](tutorial-acm-create-budgets.md) en-waarschuwingen wanneer de kosten de budget overschrijden.
- Schakel het Azure Resource Manager-beleid in dat klanten toegang biedt tot Cost Management gegevens. Klanten kunnen gegevens over verbruiks kosten weer geven voor hun abonnementen met [betalen naar](https://azure.microsoft.com/pricing/calculator/)gebruik-tarieven.
- Exporteer hun kosten-en gebruiks gegevens naar een opslag-blob met een betalen per gebruik-abonnement.

Hier volgt een voor beeld van de kosten voor alle klanten.
![voor beeld van de kosten voor alle klanten](./media/get-started-partners/customer-costs1.png)

Hier volgt een voor beeld van de kosten voor één klant.
![voor beeld van de kosten voor één klant](./media/get-started-partners/customer-costs2.png)

Alle functionaliteit die beschikbaar is in Azure Cost Management is ook beschikbaar met REST Api's. Gebruik de Api's om kosten beheer taken te automatiseren.

## <a name="prerequisites"></a>Vereisten

Als partner is Azure Cost Management standaard alleen beschikbaar voor abonnementen die zich in het Azure-abonnement bevinden.

Als u Azure Cost Management in het Azure Portal wilt inschakelen, moet u de klant overeenkomst van micro soft hebben bevestigd (namens de klant) en de klant hebben overgezet naar het Azure-abonnement. Alleen de kosten voor abonnementen die zijn overgezet naar het Azure-abonnement, zijn beschikbaar in Azure Cost Management.

Azure Cost Management hebt lees toegang tot uw facturerings account of-abonnement nodig.

Zie [gebruikers rollen en machtigingen toewijzen](/partner-center/permissions-overview)voor meer informatie over het inschakelen en toewijzen van toegang tot Azure Cost Management voor een facturerings account. De rol van **globale beheerder** en **beheerder agent** kan de kosten voor een facturerings account beheren.

Om toegang te krijgen tot Azure Cost Management op het abonnements bereik, kunnen gebruikers met RBAC-toegang tot een abonnement kosten bekijken op tarieven van Retail (betalen naar gebruik). Het beleid voor de zichtbaarheid van kosten voor de Tenant van de klant moet echter zijn ingeschakeld. Zie [inzicht in cost management gegevens](understand-cost-mgt-data.md)voor een volledige lijst met ondersteunde account typen.


## <a name="how-cost-management-uses-scopes"></a>Hoe Cost Management scopes gebruikt?

Met bereiken beheert u facturerings gegevens, beschikt u over functies die specifiek zijn voor betalingen, het weer geven van facturen en het uitvoeren van algemeen account beheer. Facturerings-en account rollen worden afzonderlijk beheerd vanaf scopes die worden gebruikt voor resource beheer, die gebruikmaakt van RBAC. Om het doel van de afzonderlijke bereiken duidelijk te onderscheiden, met inbegrip van de verschillen in toegangs beheer, worden ze respectievelijk facturerings bereiken en RBAC-bereiken genoemd.

Zie [begrijpen en werken met bereiken](understand-work-scopes.md)voor meer informatie over de facturerings bereiken en RBAC-bereiken en hoe cost management werkt met scopes.

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>Kosten beheren met het facturerings bereik van een partner-Tenant

Nadat u uw klanten aan een micro soft-klant overeenkomst hebt toegevoegd, zijn de volgende _facturerings bereiken_ beschikbaar in uw Tenant. Gebruik de bereiken voor het beheren van de kosten in Cost Management.

### <a name="billing-account-scope"></a>Bereik van facturerings account

Gebruik het bereik van het facturerings account om kosten voor preheffingen voor al uw klanten en facturerings profielen weer te geven. Factuur kosten worden alleen weer gegeven voor producten op basis van verbruik van de klant van micro soft. Factuur kosten worden echter weer gegeven voor op aankopen gebaseerde producten voor klanten op zowel de klant overeenkomst van micro soft als de CSP-aanbieding. Op dit moment is de standaard valuta voor het weer geven van de kosten in het bereik Amerikaanse dollars. Budgetten die zijn ingesteld voor het bereik, zijn ook in USD.

In het kader van verschillende klant-gefactureerde valuta's gebruiken partners het bereik van facturerings accounts om budgetten in te stellen en kosten in USD te beheren voor hun klanten, abonnementen, resources en resource groepen.

Partners filteren ook kosten in een specifieke factuur valuta voor klanten in de weer gave kosten analyse. Selecteer de lijst met **werkelijke kosten** om de kosten in ondersteunde facturerings valuta's van klanten weer te geven.

![Voor beeld van de werkelijke kosten selectie voor valuta's](./media/get-started-partners/actual-cost-selector.png)

Gebruik de [weer gave afgeschreven kosten](quick-acm-cost-analysis.md#customize-cost-views) in facturerings bereik om de afgeschreven kosten van gereserveerde instanties voor een reserverings termijn weer te geven.

### <a name="billing-profile-scope"></a>Bereik van facturerings profiel

Gebruik het bereik van het facturerings profiel om de kosten voor preheffing in de facturerings valuta voor al uw klanten te bekijken voor alle producten en abonnementen die in een factuur zijn opgenomen. U kunt de kosten in een facturerings profiel voor een specifieke factuur filteren met behulp van het **InvoiceID** -filter. Het filter toont het verbruik en de aanschaf kosten van het product voor een specifieke factuur. U kunt ook de kosten voor een specifieke klant op de factuur filteren om de kosten voor de belasting vooraf te bekijken.

Nadat u klanten onboardt naar een micro soft-klant overeenkomst, ontvangt u een factuur met alle kosten voor alle producten (verbruik, aankopen en rechten) voor deze klanten op de micro soft-klant overeenkomst. Wanneer in dezelfde valuta wordt gefactureerd, omvatten deze facturen ook de kosten voor rechte en aangeschafte producten zoals SaaS, Azure Marketplace en reserve ringen voor klanten die zich nog in de CSP-aanbieding bevinden.

Voor het afstemmen van de kosten voor de klant factuur kunt u met het bereik van het facturerings profiel alle kosten bekijken die toenemen voor een factuur voor uw klanten. Net als bij de factuur toont het bereik de kosten voor elke klant in de nieuwe klant overeenkomst van micro soft. In het bereik worden ook de kosten voor klant rechten weer gegeven die nog in de huidige CSP-aanbieding zijn opgenomen.

De scopes van het facturerings profiel en het betaal account zijn de enige toepasselijke bereiken die de kosten voor rechten en op aankoop gebaseerde producten zoals Azure Marketplace en reserverings aankopen tonen.

In facturerings profielen worden de abonnementen gedefinieerd die zijn opgenomen in een factuur. Facturerings profielen zijn het functionele equivalent van een Enter prise Agreement-inschrijving. Een facturerings profiel is het bereik waarin facturen worden gegenereerd.

Op dit moment is de facturerings valuta van de klant de standaard valuta bij het weer geven van de kosten in het bereik van het facturerings profiel. Budgetten die zijn ingesteld op het bereik van het facturerings profiel, worden in de facturerings valuta opgenomen.

Partners kunnen het bereik gebruiken voor het afstemmen op facturen. En ze gebruiken het bereik om budgetten in te stellen in de facturerings valuta voor de volgende items:

- Specifieke gefilterde factuur
- Klant
- Abonnement
- Resourcegroep
- Bron
- Azure-service
- Naar gebruik
- ResellerMPNID

### <a name="customer-scope"></a>Bereik van klant

Partners gebruiken de scope voor het beheren van de kosten die zijn gekoppeld aan klanten die onboarding voor de micro soft-klant overeenkomst zijn. Met het bereik kunnen partners de kosten van de preheffing voor een specifieke klant weer geven. U kunt ook de kosten vóór belastingen voor een specifiek abonnement, resource groep of resource filteren.

Het bereik van de klant bevat geen klanten die zich op de huidige CSP-aanbieding bevinden. De scope bevat alleen klanten die een micro soft-klant overeenkomst hebben. De rechten kosten, niet voor Azure-gebruik, voor huidige CSP-aanbieding klanten zijn beschikbaar op het facturerings account en het bereik van facturerings profielen wanneer u het klant filter toepast.

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Partner toegang tot de facturerings bereiken in Cost Management

Alleen gebruikers met de rol van **globale beheerder** en **beheerder** kunnen kosten voor facturerings accounts, facturerings profielen en klanten rechtstreeks in de Azure-Tenant van de partner beheren en weer geven. Zie [gebruikers rollen en machtigingen toewijzen](/partner-center/permissions-overview)voor meer informatie over de functies van het partner centrum.

## <a name="enable-cost-management-in-the-customer-tenant"></a>Kosten beheer inschakelen in de Tenant van de klant

Partners kunnen toegang tot Cost Management mogelijk maken nadat klanten zijn opvolgd voor een klant overeenkomst van micro soft. Vervolgens kunnen partners een beleid inschakelen waarmee klanten hun kosten bekijken die worden berekend op basis van betalen naar gebruik-tarieven. Kosten worden weer gegeven in de facturerings valuta van de klant voor het verbruikte gebruik van het abonnement en de resource groepen van het RBAC.

Wanneer het beleid voor de zicht baarheid van kosten wordt ingeschakeld door de partner, kan elke gebruiker met Azure Resource Manager toegang tot het abonnement kosten op basis van betalen naar gebruik-tarieven beheren en analyseren. Effectief kunnen wederverkopers en klanten die de juiste RBAC-toegang hebben tot de Azure-abonnementen, kosten bekijken.

Ongeacht het beleid kunnen partners ook de kosten weer geven als ze toegang hebben tot het abonnement en de resource groep.

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>Het beleid inschakelen voor het weer geven van Azure-gebruiks kosten

Partners gebruiken de volgende informatie om het beleid in te scha kelen voor het weer geven van Azure-gebruiks kosten voor hun klanten.

Meld u aan bij de partner-Tenant in het Azure Portal en selecteer **Cost Management + facturering**. Selecteer een facturerings account en selecteer vervolgens **klanten**. De lijst met klanten is gekoppeld aan het facturerings account.

Selecteer in de lijst met klanten de klant waarvoor u de kosten wilt weer geven.

![Klanten selecteren in Cost Management](./media/get-started-partners/customer-list.png)

Onder **instellingen**selecteert u **beleid**.

Het huidige beleid voor de zichtbaarheid van kosten wordt weer gegeven voor **Azure-gebruiks** kosten die zijn gekoppeld aan de abonnementen voor de geselecteerde klant.
![beleid waarmee klanten kosten voor betalen per gebruik kunnen weer geven](./media/get-started-partners/cost-management-billing-policies.png)

Wanneer het beleid is ingesteld op **Nee**, is Azure Cost Management niet beschikbaar voor abonnements gebruikers die zijn gekoppeld aan de klant. Tenzij deze is ingeschakeld door een partner, wordt het beleid voor kosten zichtbaarheid standaard uitgeschakeld voor alle gebruikers van het abonnement.

Wanneer het kosten beleid is ingesteld op **Ja**, kunnen abonnements gebruikers die zijn gekoppeld aan de Tenant van de klant, de gebruiks kosten bekijken bij betalen naar gebruik-tarieven.

Wanneer het beleid voor het berekenen van de kosten is ingeschakeld, worden voor alle services waarvoor het abonnements abonnement geldt kosten op basis van betalen naar gebruik-tarieven weer gegeven. Het gebruik van de reserve ring wordt weer gegeven met nul kosten voor de werkelijke en afgeschreven kosten. Aankopen en rechten zijn niet gekoppeld aan een specifiek abonnement. Aankopen worden dus niet weer gegeven in het abonnements bereik.

Als u de kosten voor de Tenant van de klant wilt weer geven, opent u Cost Management + facturering en selecteert u vervolgens facturerings accounts. Selecteer een facturerings account in de lijst met facturerings accounts.

![Een facturerings account selecteren](./media/get-started-partners/select-billing-account.png)

Onder **facturering**selecteert u **Azure-abonnementen**en selecteert u vervolgens een klant.

![Een klant van een Azure-abonnement selecteren](./media/get-started-partners/subscriptions-select-customer.png)

Selecteer **kosten analyse** en Bekijk de kosten.
Kosten analyse, budgetten en waarschuwingen zijn beschikbaar voor het abonnement en de RBAC-scopes van de resource groep tegen kosten op basis van betalen per gebruik-tarief.

![Kosten analyse als een klant weer geven ](./media/get-started-partners/customer-tenant-view-cost-analysis.png)

Bij afgeschreven weer gaven en werkelijke kosten voor gereserveerde instanties in de RBAC-bereiken worden nulwaarden weer gegeven. Kosten voor gereserveerde instanties worden alleen weer gegeven in facturerings bereik waar de aankopen zijn gedaan.

## <a name="analyze-costs-in-cost-analysis"></a>Kosten analyseren in kosten analyse

Partners met toegang tot de facturerings bereiken in de partner-Tenant kunnen gefactureerde kosten in kosten analyse over klanten verkennen en analyseren voor een specifieke klant of voor een factuur. In de weer gave [kosten analyse](quick-acm-cost-analysis.md) kunt u ook [weer gaven opslaan](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) en gegevens exporteren naar [CSV-en PNG-bestanden](quick-acm-cost-analysis.md#automation-and-offline-analysis).

RBAC-gebruikers met toegang tot het abonnement in de Tenant van de klant kunnen ook retail kosten analyseren voor abonnementen in de Tenant van de klant, weer gaven opslaan en gegevens exporteren naar CSV-en PNG-bestanden.

U kunt filteren en groeperen op functies in Cost Analysis gebruiken om kosten te analyseren op basis van meerdere velden. In de volgende sectie worden de partner-specifieke velden weer gegeven.

## <a name="data-fields"></a>Gegevens velden

De volgende gegevens velden zijn te vinden in detail bestanden voor gebruik en Cost Management-Api's. Indien beschikbaar, wordt gelijkwaardige informatie over Partner Center weer gegeven. Voor de volgende vetgedrukte velden kunnen partners filteren en groeperen op functies in Cost Analysis gebruiken om kosten te analyseren op basis van meerdere velden. Vetgedrukte velden zijn alleen van toepassing op micro soft-klant overeenkomsten die door partners worden ondersteund.

| **Veld naam** | **Beschrijving** | **Partner centrum equivalent** |
| --- | --- | --- |
| InvoiceId | Factuur-ID die wordt weer gegeven op de factuur voor de specifieke trans actie. | Factuur nummer waar de trans actie wordt weer gegeven. |
| previousInvoiceID | Verwijzing naar een oorspronkelijke factuur er is een restitutie (negatieve kosten). Alleen ingevuld als er een restitutie is. | N/A |
| billingAccountName | De naam van het facturerings account dat de partner voor stelt. Alle kosten worden in rekening gebracht voor alle klanten die hebben gekantt aan een micro soft-klant overeenkomst en de CSP-klanten die recht hebben op aankopen als SaaS, Azure Marketplace en reserve ringen. | N/A |
| billingAccountID | De id voor het facturerings account dat de partner voor stelt. | MCAPI partner commerce-basis-ID. Wordt gebruikt in een aanvraag, maar is niet opgenomen in een antwoord.|
| billingProfileID | De id voor het facturerings profiel waarmee de kosten voor facturen worden gegroepeerd in een enkele facturerings valuta voor klanten die hebben gereageerd op een micro soft-klant overeenkomst en de CSP-klanten die recht hebben op aankopen als SaaS, Azure Marketplace en ringen. | Facturerings groep-ID van MCAPI-partner. Wordt gebruikt in een aanvraag, maar is niet opgenomen in een antwoord. |
| billingProfileName | De naam van het facturerings profiel waarmee de kosten voor facturen worden gegroepeerd in een enkele facturerings valuta voor de klanten die een micro soft-klant overeenkomst hebben opgedaan en de CSP-klanten die recht hebben op aankopen als SaaS, Azure Marketplace en ringen. | N/A |
| invoiceSectionName | De naam van het project dat in de factuur wordt gefactureerd. Niet van toepassing op micro soft-klant overeenkomsten die worden uitgevoerd door partners. | N/A |
| invoiceSectionID | De id van het project dat in de factuur wordt gefactureerd. Niet van toepassing op micro soft-klant overeenkomsten die worden uitgevoerd door partners. | N/A |
| **CustomerTenantID** | De id van de Azure Active Directory Tenant van het abonnement van de klant. | Organisatie-ID van de klant: de Azure Active Directory TenantID van de klant. |
| **CustomerName** | De naam van de Azure Active Directory Tenant voor het abonnement van de klant. | De organisatie naam van de klant, zoals weer gegeven in het partner centrum. Belang rijk voor het afstemmen van de factuur met uw systeem gegevens. |
| **CustomerTenantDomainName** | De domein naam voor de Azure Active Directory Tenant van het abonnement van de klant. | Tenant domein van klant Azure Active Directory. |
| **PartnerTenantID** | Id voor de Azure Active Directory-Tenant van de partner. | Partner Azure Active Directory Tenant-ID, in GUID-indeling, aangeduid als partner-ID. |
| **PartnerName** | De naam van de partner Azure Active Directory Tenant. | De naam van de partner. |
| **ResellerMPNID** | MPNID voor de wederverkoper die aan het abonnement is gekoppeld. | MPN-ID van de reseller op record voor het abonnement. Niet beschikbaar voor huidige activiteit. |
| costCenter | Kosten plaats die aan het abonnement is gekoppeld. | N/A |
| billingPeriodStartDate | De begin datum van de facturerings periode, zoals op de factuur wordt weer gegeven. | N/A |
| billingPeriodEndDate | De eind datum van de facturerings periode, zoals op de factuur wordt weer gegeven. | N/A |
| servicePeriodStartDate | De begin datum voor de beoordelings periode wanneer het service verbruik is beoordeeld op kosten. De prijzen voor Azure-Services worden bepaald voor de beoordelings periode. | ChargeStartDate in partner centrum. De begin datum van de facturerings cyclus, met uitzonde ring van de datum van voorheen niet-gefactureerde ongebruikte gegevens van een eerdere facturerings cyclus. De tijd is altijd het begin van de dag, 0:00. |
| servicePeriodEndDate | Eind datum voor de periode waarin het service verbruik is beoordeeld op kosten. De prijzen voor Azure-Services worden bepaald op basis van de waarderings periode. | N/A |
| date | Voor Azure-verbruiks gegevens wordt de datum van gebruik weer gegeven als geclassificeerd. Voor een gereserveerde instantie wordt de aangeschafte datum weer gegeven. Voor terugkerende kosten en eenmalige kosten, zoals Marketplace en ondersteuning, wordt de aankoop datum weer gegeven. | N/A |
| productID | Id voor het product dat de transitorische kosten per verbruik of aankoop heeft. Het is de aaneengeschakelde sleutel van productID en SKuID, zoals wordt weer gegeven in het partner centrum. | De ID van het product. |
| product | Naam van het product met transitorische kosten per verbruik of aankoop, zoals op de factuur wordt weer gegeven. | De product naam in de catalogus. |
| serviceFamily | Toont de service familie voor het product dat is gekocht of in rekening wordt gebracht. Bijvoorbeeld Storage of compute. | N/A |
| productOrderID | De id van de Asset of Azure-plan naam waarvan het abonnement deel uitmaakt. Bijvoorbeeld Azure-abonnement. | N/A |
| productOrderName | De naam van het Azure-abonnement waarvan het abonnement deel uitmaakt. Bijvoorbeeld Azure-abonnement. | N/A|
| consumedService | Verbruikte service (verouderde taxonomie) zoals gebruikt in verouderde EA-gebruiks gegevens. | De service die wordt weer gegeven in het partner centrum. Bijvoorbeeld micro soft. Storage, micro soft. Compute en micro soft. operationalinsights. |
| meterId | Gecontroleerde id voor het gemeten verbruik. | De ID van de gebruikte meter. |
| meterName | Hiermee wordt de naam van de meter voor gemeten verbruik geïdentificeerd. | De naam van de verbruikte meter. |
| meterCategory | Identificeert de service op het hoogste niveau voor gebruik. | De service op het hoogste niveau voor het gebruik. |
| meterSubCategory | Hiermee wordt het type of de subcategorie van de Azure-service gedefinieerd die van invloed kan zijn op het aantal. | Het type Azure-service dat van invloed kan zijn op het aantal.|
| meterRegion | De datacenterlocatie voor bepaalde services waarbij de prijs is gebaseerd op de datacenterlocatie. | De regionale locatie van een Data Center voor services, indien van toepassing en gevuld. |
| subscription ID | De unieke door micro soft gegenereerde id voor het Azure-abonnement. | N/A |
| subscriptionName | De naam van het Azure-abonnement. | N/A |
| Termijn | Hiermee wordt de geldigheidstermijn van de aanbieding weergegeven. Gereserveerde instanties tonen bijvoorbeeld 12 maanden van een jaar van de gereserveerde instantie. Voor eenmalige aankopen of terugkerende aankopen wordt voor de periode één maand voor SaaS, Azure Marketplace en ondersteuning weer gegeven. Niet van toepassing op Azure-verbruik. | N/A |
| publisherType (firstParty, thirdPartyReseller, thirdPartyAgency) | Type uitgever die de uitgever identificeert als eerste partij, wederverkoper van derden of Bureau van derden. | N/A |
| partNumber | Onderdeel nummer voor het niet-gebruikte gereserveerde exemplaar en Azure Marketplace-Services. | N/A |
| publisherName | De naam van de uitgever van de service, inclusief micro soft-of uitgevers van derden. | De naam van de uitgever van het product.|
| reservationId | De id voor de gereserveerde instantie aankoop. | N/A |
| reservationName | De naam van de gereserveerde instantie. | N/A |
| reservationOrderId | OrderID voor het gereserveerde exemplaar. | N/A |
| frequency | De betalings frequentie voor een gereserveerde instantie. | N/A |
| resourceGroup | De naam van de Azure-resource groep die wordt gebruikt voor levenscyclus resource beheer. | De naam van de resource groep. |
| instanceID (of) ResourceID | De id van het resource-exemplaar. | Wordt weer gegeven als een ResourceURI die volledige resource-eigenschappen bevat. |
| resourceLocation | De naam van de resource locatie. | De locatie van de resource. |
| Locatie | De genormaliseerde locatie van de resource. | N/A |
| effectivePrice | De werkelijke eenheids prijs van de service, in de valuta prijs. Uniek voor een product, een service familie, een meter en een aanbieding. Wordt gebruikt met prijzen in het prijs overzicht voor het facturerings account. Als er gelaagde prijzen of een inbegrepen hoeveelheid zijn, wordt de overvloei prijs voor verbruik weer gegeven. | De eenheids prijs na aanpassingen wordt gemaakt. |
| Hoeveelheid | Gemeten hoeveelheid die is gekocht of verbruikt. De hoeveelheid meter die wordt gebruikt tijdens de facturerings periode. | Aantal eenheden. Zorg ervoor dat deze overeenkomt met de informatie in uw facturerings systeem tijdens het afstemmen. |
| unitOfMeasure | Hiermee wordt de eenheid geïdentificeerd waarin de service wordt gefactureerd. Bijvoorbeeld GB en uren. | Hiermee wordt de eenheid geïdentificeerd waarin de service wordt gefactureerd. Bijvoorbeeld GB, uren en 10.000 s. |
| pricingCurrency | De valuta waarin de eenheids prijs wordt gedefinieerd. | De valuta in de prijs lijst.|
| billingCurrency | De valuta waarin de gefactureerde kosten worden gedefinieerd. | De valuta van de geografische regio van de klant. |
| chargeType | Definieert het type kosten dat de kosten vertegenwoordigen in Azure Cost Management zoals aankoop en terugbetaling. | Het type kosten of correctie. Niet beschikbaar voor huidige activiteit. |
| costinBillingCurrency | ExtendedCost of overvloei kosten vóór belasting in de gefactureerde valuta. | N/A |
| costinPricingCurrency | ExtendedCost of overvloei kosten voor BTW in de prijs valuta om te correleren met prijzen. | N/A |
| **costinUSD** | Geschatte ExtendedCost of overvloei kosten vóór belasting in USD. | N/A |
| **paygCostInBillingCurrency** | Hiermee worden de kosten weer gegeven als de prijzen een verkoop prijs zijn. Hiermee worden de prijzen voor betalen naar gebruik in de facturerings valuta weer gegeven. Alleen beschikbaar voor RBAC-bereiken. | N/A |
| **paygCostInUSD** | Hiermee worden de kosten weer gegeven als de prijzen een verkoop prijs zijn. Toont de prijzen voor betalen naar gebruik in USD. Alleen beschikbaar voor RBAC-bereiken. | N/A |
| exchangeRate | De wissel koers die wordt gebruikt om de prijs valuta om te zetten in de facturerings valuta. | Wordt aangeduid als PCToBCExchangeRate in het partner centrum. De prijs valuta voor factuur wisselkoers.|
| exchangeRateDate | De datum voor de wissel koers die wordt gebruikt om van de prijs valuta te converteren naar de facturerings valuta. | Wordt aangeduid als PCToBCExchangeRateDat in het partner centrum. De prijs valuta voor de factuur wisselkoers datum.|
| isAzureCreditEligible | Geeft aan of de kosten in aanmerking komen voor betaling door Azure-tegoed. | N/A |
| serviceInfo1 | Verouderd veld waarin optionele servicespecifieke meta gegevens worden vastgelegd. | Interne meta gegevens van Azure-service. |
| serviceInfo2 | Verouderd veld waarin optionele servicespecifieke meta gegevens worden vastgelegd. | Service-informatie. Bijvoorbeeld een afbeeldings type voor de naam van een virtuele machine en ISP voor ExpressRoute.|
| additionalInfo | Servicespecifieke metagegevens. Bijvoorbeeld een installatiekopie voor een virtuele machine. | Aanvullende informatie die niet wordt behandeld in andere kolommen. De servicespecifieke meta gegevens. Bijvoorbeeld een installatiekopie voor een virtuele machine.|
| tags | Label dat u toewijst aan de meter. Tags gebruiken om facturerings records te groeperen. U kunt bijvoorbeeld Tags gebruiken om kosten te verdelen door de afdeling die gebruikmaakt van de meter. | Tags die door de klant worden toegevoegd.|
| **partnerEarnedCreditRate** | Het tarief van de korting wordt toegepast als er een door de partner aangehaalde credit (PEC) is op basis van de toegang tot de partner beheerder. | De frequentie van het tegoed van de partner (PEC). Bijvoorbeeld 0% of 15%. |
| **partnerEarnedCreditApplied** | Geeft aan of het tegoed van de partner is toegepast. | N/A |

## <a name="view-partner-earned-credit-pec-resource-costs"></a>Resource kosten voor het ontvangen van partners (PEC) weer geven

In Azure Cost Management kunnen partners kosten analyse gebruiken om de kosten te bekijken die de PEC-voor delen hebben ontvangen.

Meld u aan bij de partner-Tenant in het Azure Portal en selecteer **Cost Management + facturering**. Selecteer onder **Cost Management** **kosten analyse**.

In de weer gave kosten analyse worden de kosten van het facturerings account voor de partner weer gegeven. Selecteer het **bereik** dat nodig is voor de partner, een specifieke klant of een facturerings profiel om facturen af te stemmen.

Selecteer in een ring diagram de vervolg keuzelijst en selecteer **PartnerEarnedCreditApplied** om in te zoomen op PEC-kosten.

![Voor beeld van het weer geven van het tegoed van een partner](./media/get-started-partners/cost-analysis-pec1.png)

Wanneer de eigenschap **PartnerEarnedCreditApplied** is ingesteld op _True_, hebben de bijbehorende kosten het voor deel van de gehaalde beheerder van de partner.

Wanneer de eigenschap **PartnerEarnedCreditApplied** _False_is, voldoen de gekoppelde kosten niet aan het vereiste recht voor het tegoed. Of de aangeschafte service komt niet in aanmerking voor het tegoed van de partner.

Service gebruiks gegevens hebben doorgaans 8-24 uur om weer te geven in Cost Management. Zie de [Update frequentie van gebruiks gegevens varieert](understand-cost-mgt-data.md#usage-data-update-frequency-varies)voor meer informatie. PEC-tegoeden worden binnen 48 uur na de toegangs tijd weer gegeven in Azure Cost Management.


U kunt ook groeperen en filteren op de eigenschap **PartnerEarnedCreditApplied** met behulp van de **Group by** -opties. Gebruik de opties om kosten te onderzoeken die geen PEC hebben.

![Groeperen of filteren op het tegoed van de partner](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="export-cost-data-to-azure-storage"></a>Kosten gegevens exporteren naar Azure Storage

Partners met toegang tot de facturerings bereiken in een partner-Tenant kunnen hun kosten-en gebruiks gegevens exporteren naar een Azure Storage blob. De BLOB moet zich in een abonnement benemen in de partner-Tenant die geen gedeeld service abonnement of een klant abonnement is. Om het exporteren van kosten gegevens in te scha kelen, wordt u aangeraden een onafhankelijk betalen per gebruik-abonnement in de partner-Tenant in te stellen om de geëxporteerde kosten gegevens te hosten. Het export opslag account wordt gemaakt op de Azure Storage-blob die wordt gehost in het abonnement voor betalen naar gebruik. Op basis van het bereik waarin de partner de export maakt, worden de bijbehorende gegevens automatisch op regel matige basis geëxporteerd naar het opslag account.

Gebruikers met RBAC-toegang tot het abonnement kunnen de kosten gegevens ook exporteren naar een Azure Storage-blob die wordt gehost in een abonnement in de Tenant van de klant.

### <a name="create-an-export-in-a-partner-tenant-or-customer-tenant"></a>Een export maken in een partner Tenant of klant Tenant

Meld u aan bij de partner-Tenant of de Tenant van de klant in het Azure Portal en selecteer **Cost Management + facturering**. Selecteer een geschikt bereik, bijvoorbeeld een facturerings account, en selecteer vervolgens **kosten analyse**. Wanneer de pagina wordt geladen, selecteert u **exporteren**. Selecteer **alle exporteren weer geven** onder planning exporteren.

![Selecteer exporteren en alle exports weer geven](./media/get-started-partners/export01.png)

Selecteer vervolgens **toevoegen** en typ de naam en selecteer een export type. Selecteer het tabblad **opslag** en voer de vereiste gegevens in.

![Tabblad nieuwe export toevoegen en opslag selecteren](./media/get-started-partners/export02.png)

Wanneer u een export maakt in de partner Tenant, selecteert u het abonnement voor betalen naar gebruik in de partner-Tenant. Maak een Azure Storage-account met dat abonnement.

Voor RBAC-gebruikers in de Tenant van de klant selecteert u een abonnement in de Tenant van de klant. Maak een Azure Storage-account met behulp van het abonnement.

Bekijk de inhoud en selecteer vervolgens **maken** om een export te plannen.

Als u gegevens wilt controleren in de lijst exporteren, selecteert u de naam van het opslag account. Selecteer op de pagina opslag account de optie **containers** en selecteer vervolgens de container. Ga naar de bijbehorende map en selecteer het CSV-bestand. Selecteer **downloaden** om het CSV-bestand op te halen en te openen. De geëxporteerde gegevens die worden geëxporteerd, zijn vergelijkbaar met kosten gegevens die gelijk zijn aan de gebruiks gegevens van de Azure Portal.

![Voor beeld van geëxporteerde gegevens](./media/get-started-partners/example-export-data.png)

## <a name="cost-management-rest-apis"></a>Cost Management REST-Api's

Partners en klanten kunnen Cost Management Api's gebruiken die worden beschreven in de volgende secties voor algemene taken.

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>Azure Cost Management-Api's-directe en indirecte providers

Partners met toegang tot de facturerings bereiken in een partner-Tenant kunnen de volgende Api's gebruiken om gefactureerde kosten weer te geven.

Api's in het abonnements bereik kunnen worden aangeroepen door een partner, ongeacht het kosten beleid als ze toegang hebben tot het abonnement. Andere gebruikers met toegang tot het abonnement, zoals de klant of de wederverkoper, kunnen de Api's pas aanroepen nadat de partner het kosten beleid voor de Tenant van de klant heeft ingeschakeld.


#### <a name="to-get-a-list-of-billing-accounts"></a>Een lijst met facturerings accounts ophalen

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-customers"></a>Een lijst met klanten ophalen

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-subscriptions"></a>Een lijst met abonnementen ophalen

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-invoices-for-a-period-of-time"></a>Een lijst met facturen voor een bepaalde tijd ophalen

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/invoices?api-version=2019-10-01-preview&periodStartDate={periodStartDate}&periodEndDate={periodEndDate}
```

De API-aanroep retourneert een matrix van facturen met elementen die vergelijkbaar zijn met de volgende JSON-code.

```
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/{billingAccountID}/billingProfiles/{BillingProfileID}/invoices/{InvoiceID}",
      "name": "{InvoiceID}",
      "properties": {
        "amountDue": {
          "currency": "USD",
          "value": x.xx
        },
        ...
    }
```

Gebruik de waarde van het voor gaande geretourneerde ID-veld en vervang deze in het volgende voor beeld als het bereik dat u wilt doorzoeken op gebruiks gegevens.

```
GET https://management.azure.com/{id}/providers/Microsoft.Consumption/UsageDetails?api-version=2019-10-01
```

In het voor beeld worden de gebruiks records geretourneerd die zijn gekoppeld aan de specifieke factuur.


#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>Om het beleid te verkrijgen voor klanten om de kosten te bekijken

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>Het beleid voor klanten instellen om kosten te bekijken

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-get-azure-service-usage-for-a-billing-account"></a>Azure-service gebruik voor een facturerings account ophalen

```
GET https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-download-a-customers-azure-service-usage"></a>Het Azure-service gebruik van een klant downloaden

De volgende aanroep Get is een asynchrone bewerking.

```
GET https://management.azure.com/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/usageDetails/download?api-version=2019-10-01 -verbose
```

Roep de `Location` URI aan die in het antwoord is geretourneerd om de bewerkings status te controleren. Wanneer de status *voltooid*is, bevat de eigenschap `downloadUrl` een koppeling die u kunt gebruiken om het gegenereerde rapport te downloaden.


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>Het prijzen overzicht voor verbruikte Azure-Services ophalen of downloaden

Gebruik eerst het volgende bericht.

```
POST https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

Roep vervolgens de eigenschaps waarde van de asynchrone bewerking aan. Bijvoorbeeld:

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheetDownloadOperations/{operation}?sessiontoken=0:11186&api-version=2019-10-01-preview
```
De voor gaande aanroep Get retourneert de download koppeling met het prijzen overzicht.


#### <a name="to-get-aggregated-costs"></a>Om geaggregeerde kosten te verkrijgen

```
POST https://management.azure.com/providers/microsoft.billing/billingAccounts/{billingAccountName}/providers/microsoft.costmanagement/query?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-partner"></a>Een budget voor een partner maken

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-customer"></a>Een budget voor een klant maken

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2019-10-01
```

#### <a name="delete-a-budget"></a>Een budget verwijderen

```
DELETE
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/budgets/{budgetName}?api-version=2019-10-01
```


## <a name="next-steps"></a>Volgende stappen
- [Begin](quick-acm-cost-analysis.md) met het analyseren van de kosten in Cost Management
- [Budgetten in cost management maken en beheren](tutorial-acm-create-budgets.md)
