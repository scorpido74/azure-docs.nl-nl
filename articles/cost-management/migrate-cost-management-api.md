---
title: EA migreren naar micro soft-gebruikers overeenkomst Api's-Azure
description: Dit artikel helpt u inzicht te krijgen in de gevolgen van het migreren van een micro soft Enterprise Agreement (EA) naar een micro soft-klant overeenkomst.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/25/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: micflan
ms.custom: ''
ms.openlocfilehash: 7fef417a7b19d463a98d32b7cf3cce515d1137a1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441005"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Migreren van Enterprise Agreement naar Api's voor micro soft-gebruikers overeenkomsten

Dit artikel helpt u bij het begrijpen van de gegevens structuur, API en andere verschillen tussen de systeem integratie tussen de Enterprise Agreement (EA) en de micro soft Customer Agreement (MCA)-accounts. Azure Cost Management ondersteunt Api's voor beide account typen. Bekijk het micro soft-artikel [voor het instellen van het facturerings account voor](../billing/mca-setup-account.md) de klant overeenkomst voordat u doorgaat.

Organisaties met een bestaand EA-account moeten dit artikel raadplegen in combi natie met het instellen van een MCA-account. Voorheen moest het vernieuwen van een EA-account een minimale hoeveelheid werk hebben om van een oude inschrijving naar een nieuw item te gaan. Voor de migratie naar een MCA-account is echter extra inspanning vereist. Verdere inspanningen zijn vanwege wijzigingen in het onderliggende subsysteem voor facturering, wat van invloed is op alle kosten-gerelateerde Api's en service aanbiedingen.

## <a name="mca-apis-and-integration"></a>MCA Api's en integratie

Met MCA Api's en nieuwe integratie kunt u:

- Volledige API-Beschik baarheid via systeem eigen Azure-Api's.
- Meerdere facturen configureren in één facturerings account.
- Krijg toegang tot een gecombineerde API met Azure-service gebruik, gebruik van Marketplace van derden en Marketplace-aankopen.
- Bekijk de kosten voor de facturerings profielen (hetzelfde als inschrijvingen) met behulp van Azure Cost Management.
- Open nieuwe Api's om kosten weer te geven, ontvang een melding wanneer de kosten de vooraf gedefinieerde drempel waarden overschrijden en onbewerkte gegevens automatisch exporteren.

## <a name="migration-checklist"></a>Migratie Controlelijst

Met de volgende items kunt u overstappen naar MCA-Api's.

- Meer vertrouwd zijn met het nieuwe [micro soft-account voor klant overeenkomsten](../billing/billing-mca-overview.md).
- Bepaal welke Api's u gebruikt en hoe deze worden vervangen in de volgende sectie.
- Raadpleeg de [Azure Resource Manager rest api's](/rest/api/azure).
- Als u Azure Resource Manager-Api's nog niet gebruikt, moet u [uw client-app registreren bij Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Werk de programma code bij voor het [gebruik van Azure AD-verificatie](/rest/api/azure/#create-the-request).
- Werk elke programmerings code bij om EA API-aanroepen te vervangen door MCA API-aanroepen.
- Fout afhandeling bijwerken om nieuwe fout codes te gebruiken.
- Bekijk aanvullende integratie aanbiedingen, zoals Cloudyn en Power BI, voor andere benodigde actie.

## <a name="ea-apis-replaced-with-mca-apis"></a>EA Api's vervangen door MCA Api's

EA-Api's gebruiken een API-sleutel voor verificatie en autorisatie. MCA Api's gebruiken Azure AD-verificatie.

| Doel | EA-API | MCA API |
| --- | --- | --- |
| Saldo en tegoed | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| Gebruik (JSON) | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Gebruik (CSV) | [/usagedetails/Download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/Submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Marketplace-gebruik (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Facturerings perioden | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Prijzenoverzicht | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Micro soft. Bill/billingAccounts/billingProfiles/prijzen overzicht/default/load format = json|csv Microsoft.Billing/billingAccounts/…/billingProfiles/…/invoices/… /pricesheet/default/Download-indeling = JSON|csv Microsoft.Billing/billingAccounts/../billingProfiles/../providers/Microsoft.Consumption/pricesheets/download  |
| Reserveringsaankopen | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| Aanbevelingen voor reserve ring | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations) [/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) [SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Micro soft. verbruik/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Reserverings gebruik | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> Azure-service en het gebruik van Marketplace van derden zijn beschikbaar met de [gebruiks Details-API](/rest/api/consumption/usagedetails).

De volgende Api's zijn beschikbaar voor MCA-facturerings accounts:

| Doel | Micro soft Customer Agreement (MCA) API |
| --- | --- |
| Facturerings accounts<sup>2</sup> | Microsoft.Billing/billingAccounts |
| Facturerings profielen<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| Factuur secties<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| Facturen | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Facturerings abonnementen | {scope}/billingSubscriptions |

<sup>2</sup> api's retour neren lijsten met objecten, die bereiken zijn, waarbij Cost Management ervaringen in de Azure Portal en api's worden toegepast. Zie voor meer informatie over Cost Management scopes [begrijpen en werken met scopes](understand-work-scopes.md).

Als u bestaande EA-Api's gebruikt, moet u deze bijwerken voor ondersteuning van MCA-facturerings accounts. De volgende tabel bevat andere wijzigingen in de integratie:

| Doel | Oude aanbieding | Nieuwe aanbieding |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) |
| Power BI | [Micro soft consumptie Insights](/power-bi/desktop-connect-azure-consumption-insights) -inhouds pakket en-connector | [Microsoft Azure Consumption Insights Power bi app](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview) en [Azure consumption Insights connector](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>Api's om saldo en tegoeden te verkrijgen

De [overzichts](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) -API ophalen geeft een maandelijks overzicht van:

- Tegoeden
- Nieuwe aankopen
- Kosten voor Azure Marketplace-service
- Aanpassingen
- Kosten voor service overschrijding

Alle verbruiks-Api's worden vervangen door systeem eigen Azure-Api's die gebruikmaken van Azure AD voor verificatie en autorisatie. Zie aan de slag [met rest](/rest/api/azure/#create-the-request)voor meer informatie over het aanroepen van Azure rest api's.

De API Get Balanced Summary wordt vervangen door de API micro soft. billing/billingAccounts/billingProfiles/availableBalance.

Beschik bare saldi ophalen met de API beschik bare saldo:

| Methode | Aanvraag-URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>Api's om kosten en gebruik te verkrijgen

Ontvang een dagelijkse uitsplitsing van de kosten van Azure-service gebruik, gebruik van Marketplace van derden en andere Marketplace-aankopen met de volgende Api's. De volgende afzonderlijke Api's zijn samengevoegd voor Azure-Services en het gebruik van Marketplace van derden. De oude Api's worden vervangen door de API [micro soft. verbruik/usageDetails](/rest/api/consumption/usagedetails) . Marketplace-aankopen worden toegevoegd, die eerder alleen werden weer gegeven in de saldo samenvatting tot nu toe.

- [Details van gebruik ophalen/downloaden](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Details van gebruik/verzenden ophalen](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Details van gebruik/usagedetails ophalen](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Details van gebruik/usagedetailsbycustomdate ophalen](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Kosten-marketplacecharges voor Marketplace Store ophalen](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Kosten-marketplacechargesbycustomdate voor Marketplace Store ophalen](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

Alle verbruiks-Api's worden vervangen door systeem eigen Azure-Api's die gebruikmaken van Azure AD voor verificatie en autorisatie. Zie aan de slag [met rest](/rest/api/azure/#create-the-request)voor meer informatie over het aanroepen van Azure rest api's.

Alle voor gaande Api's worden vervangen door de details van de API voor verbruik/gebruik.

Gebruiks Details ophalen met de API gebruiks gegevens:

| Methode | Aanvraag-URI |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

De gebruiks Details-API, net als bij alle Cost Management Api's, is beschikbaar op meerdere bereiken. Gebruik voor gefactureerde kosten, net als bij een inschrijvings niveau, het bereik van het facturerings profiel.  Zie voor meer informatie over Cost Management scopes [begrijpen en werken met scopes](understand-work-scopes.md).

| Type | ID-indeling |
| --- | --- |
| Factureringsaccount | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Factureringsprofiel | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Abonnement | `/subscriptions/{subscriptionId}` |
| Resourcegroep | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

Gebruik de volgende query string-para meters om programma code bij te werken.

| Oude para meters | Nieuwe para meters |
| --- | --- |
| `billingPeriod={billingPeriod}` | Niet ondersteund |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

De hoofd tekst van het antwoord is ook gewijzigd.

Hoofd tekst van oud bericht:

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

Nieuwe antwoord tekst:

```
{
  "value": [{
    "id": "{scope}/providers/Microsoft.Consumption/usageDetails/###",
    "name": "###",
    "type": "Microsoft.Consumption/usageDetails",
    "tags": {...},
    "properties": [{...}, ...],
    "nextLink": "string"
  }, ...]
}
```

De naam van de eigenschap met de matrix met gebruiks records is gewijzigd van gegevens in _waarden_. Elke record die wordt gebruikt om een lijst met gedetailleerde eigenschappen te gebruiken. Elke record bevat nu echter alle details in een geneste eigenschap met de naam _Eigenschappen_, met uitzonde ring van tags. De nieuwe structuur is consistent met andere Azure-Api's. Sommige eigenschapnamen zijn gewijzigd. In de volgende tabel worden de bijbehorende eigenschappen weer gegeven.

| Oude eigenschap | Nieuwe eigenschap | Opmerkingen |
| --- | --- | --- |
| AccountId | N/A | De maker van het abonnement wordt niet bijgehouden. Gebruik invoiceSectionId (zelfde als departmentId). |
| AccountNameAccountOwnerId en AccountOwnerEmail | N/A | De maker van het abonnement wordt niet bijgehouden. Gebruik invoiceSectionName (hetzelfde als afdelings). |
| AdditionalInfo | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | Houd er rekening mee dat deze eigenschappen tegenovergesteld zijn. Als isAzureCreditEnabled is ingesteld op True, is ChargesBilledSeparately false. |
| ConsumedQuantity | quantity | &nbsp; |
| ConsumedService | consumedService | De exacte teken reeks waarden kunnen verschillen. |
| ConsumedServiceId | Geen | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| Datum en usageStartDate | date | &nbsp;  |
| Dag | Geen | De datum van de dag wordt geparseerd. |
| DepartmentId | invoiceSectionId | De exacte waarden verschillen. |
| DepartmentName | invoiceSectionName | De exacte teken reeks waarden kunnen verschillen. Zo nodig kunt u factuur secties configureren zodat deze overeenkomen met afdelingen. |
| ExtendedCost en kosten | costInBillingCurrency | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| Is terugkerende kosten | Geen | &nbsp;  |
| Locatie | location | &nbsp;  |
| MeterCategory | meterCategory | De exacte teken reeks waarden kunnen verschillen. |
| MeterId | meterId | De exacte teken reeks waarden verschillen. |
| MeterName | meterName | De exacte teken reeks waarden kunnen verschillen. |
| MeterRegion | meterRegion | De exacte teken reeks waarden kunnen verschillen. |
| MeterSubCategory | meterSubCategory | De exacte teken reeks waarden kunnen verschillen. |
| Month | Geen | De datum van de maand wordt geparseerd. |
| Naam van aanbieding: | Geen | Gebruik publishernaam en productOrderName. |
| OfferID | Geen | &nbsp;  |
| Bestelnummer | Geen | &nbsp;  |
| PartNumber | Geen | Gebruik meterId en productOrderName om prijzen uniek te identificeren. |
| Naam van het plan | productOrderName | &nbsp;  |
| Product | Product |   |
| ProductId | productId | De exacte teken reeks waarden verschillen. |
| Naam van de uitgever | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| GUID | meterId | De exacte teken reeks waarden verschillen. |
| ResourceLocation | resourceLocation | &nbsp;  |
| ResourceLocationId | Geen | &nbsp;  |
| ResourceRate | effectivePrice | &nbsp;  |
| ServiceAdministratorId | N/A | &nbsp;  |
| ServiceInfo1 | serviceInfo1 | &nbsp;  |
| ServiceInfo2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | De exacte teken reeks waarden kunnen verschillen. |
| ServiceTier | meterSubCategory | De exacte teken reeks waarden kunnen verschillen. |
| StoreServiceIdentifier | N/A | &nbsp;  |
| SubscriptionGuid | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| SubscriptionName | subscriptionName | &nbsp;  |
| Tags | tags | De eigenschap Tags is van toepassing op het hoofd object, niet op de eigenschap geneste eigenschappen. |
| UnitOfMeasure | unitOfMeasure | De exacte teken reeks waarden verschillen. |
| usageEndDate | date | &nbsp;  |
| Jaar | Geen | De datum van het jaar wordt geparseerd. |
| (nieuw) | billingCurrency | De valuta die wordt gebruikt voor de kosten. |
| (nieuw) | billingProfileId | De unieke ID voor het facturerings Profiel (hetzelfde als inschrijving). |
| (nieuw) | billingProfileName | De naam van het facturerings Profiel (hetzelfde als inschrijving). |
| (nieuw) | chargeType | Gebruiken om onderscheid te maken tussen Azure-service gebruik, Marketplace-gebruik en aankopen. |
| (nieuw) | InvoiceId | De unieke ID voor de factuur. Leeg voor de huidige, open maand. |
| (nieuw) | publisherType | Het type Uitgever voor aankopen. Leeg voor gebruik. |
| (nieuw) | serviceFamily | Het type aankoop. Leeg voor gebruik. |
| (nieuw) | servicePeriodEndDate | Eind datum voor de aangeschafte service. |
| (nieuw) | servicePeriodStartDate | Begin datum voor de aangeschafte service. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>API voor facturerings periodes vervangen door facturen API

MCA-facturerings accounts gebruiken geen facturerings perioden. In plaats daarvan gebruiken ze facturen om de kosten te beperken tot specifieke facturerings perioden. De [API voor facturerings perioden](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) wordt vervangen door de factuur-API. Alle verbruiks-Api's worden vervangen door systeem eigen Azure-Api's die gebruikmaken van Azure AD voor verificatie en autorisatie. Zie aan de slag [met rest](/rest/api/azure/#create-the-request)voor meer informatie over het aanroepen van Azure rest api's.

Facturen ophalen met de API voor facturen:

| Methode | Aanvraag-URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>Prijzen lijst-Api's

In deze sectie worden de bestaande prijzen voor prijs modellen besproken en worden aanbevelingen gedaan om over te stappen op de prijzen lijst-API voor micro soft-klant overeenkomsten. Ook wordt de prijs lijst-API voor micro soft-klant overeenkomsten beschreven en worden de velden in de prijs lijsten uitgelegd. De [Enter prise prijs lijst voor prijzen ophalen](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) en [Enter prise-facturerings periodes](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) worden vervangen door de prijs lijst-API voor micro soft-klant overeenkomsten (micro soft. billing/billingAccounts/billingProfiles/prijzen overzicht). De nieuwe API ondersteunt zowel JSON-als CSV-indelingen, in asynchrone REST-indelingen. Alle verbruiks-Api's worden vervangen door systeem eigen Azure-Api's die gebruikmaken van Azure AD voor verificatie en autorisatie. Zie aan de slag [met rest](/rest/api/azure/#create-the-request)voor meer informatie over het aanroepen van Azure rest api's.

### <a name="billing-enterprise-apis"></a>API voor Enter prise-Services

U hebt facturering-Api's voor ondernemingen gebruikt met inschrijvingen voor de prijs en de facturerings periode. Verificatie en autorisatie die worden gebruikt Azure Active Directory webtokens.

Voor het verkrijgen van toepasselijke prijzen voor de opgegeven Enter prise-inschrijving met het prijzen overzicht en de facturerings periode-Api's:

| Methode | Aanvraag-URI |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>Prijs lijst-API voor micro soft-klant overeenkomsten

Gebruik de prijs lijst-API voor micro soft-klanten overeenkomsten om de prijzen voor alle verbruiks services van Azure en Marketplace te bekijken. De prijzen die voor het facturerings profiel worden weer gegeven, zijn van toepassing op alle abonnementen die deel uitmaken van het facturerings profiel.

Gebruik de prijs lijst-API voor het weer geven van alle prijzen lijst gegevens van Azure consumptie Services in CSV-indeling:

| Methode | Aanvraag-URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

Gebruik de prijs lijst-API voor het weer geven van alle prijzen lijst gegevens van het Azure-verbruiks Services in JSON-indeling:

| Methode | Aanvraag-URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

Met de API wordt het prijs overzicht voor het hele account geretourneerd. U kunt echter ook een verkorte versie van het prijzen overzicht in PDF-indeling krijgen. Het overzicht bevat Azure-verbruik en services voor het gebruik van Marketplace die voor een specifieke factuur worden gefactureerd. De factuur wordt geïdentificeerd door {invoiceId}. Dit is hetzelfde als het **factuur nummer** dat wordt weer gegeven in de PDF-bestanden met factuur overzichten. Hier volgt een voorbeeld.

![Voorbeeld afbeelding met het factuur nummer dat overeenkomt met de InvoiceId](./media/migrate-cost-management-api/invoicesummary.png)

Factuur gegevens weer geven met de API voor het prijzen overzicht in CSV-indeling:

| Methode | Aanvraag-URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Factuur gegevens weer geven met de API voor het prijzen overzicht in JSON-indeling:

| Methode | Aanvraag-URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

U kunt ook geschatte prijzen bekijken voor elke service voor Azure-verbruik of Marketplace-verbruik in de huidige openstaande facturerings cyclus of service periode.

U kunt als volgt geschatte prijzen voor verbruiks services weer geven met de API voor het prijzen overzicht in CSV-indeling:

| Methode | Aanvraag-URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

U kunt als volgt geschatte prijzen voor verbruiks services weer geven met de API voor het prijzen overzicht in JSON-indeling:

| Methode | Aanvraag-URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

De micro soft-licentie voor prijzen lijst van klanten is *ASYNCHRONE rest api's*. De antwoorden voor de Api's zijn gewijzigd ten opzichte van de oudere synchrone Api's. De hoofd tekst van de API-reactie is ook gewijzigd.

#### <a name="old-response-body"></a>Hoofd tekst van oud antwoord

Hier volgt een voor beeld van het synchrone REST API antwoord:

```
[
        {
            "id": "enrollments/573549891/billingperiods/2016011/products/343/pricesheets",
            "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
            "meterName": "A1 VM",
            "unitOfMeasure": "100 Hours",
            "includedQuantity": 0,
            "partNumber": "N7H-00015",
            "unitPrice": 0.00,
            "currencyCode": "USD"
        },
        {
    ]
```

#### <a name="new-response-body"></a>Nieuwe antwoord tekst

De Api's bieden ondersteuning voor de [asynchrone Azure rest](../azure-resource-manager/resource-manager-async-operations.md) -indeling. Roep de API aan met GET en ontvang het volgende antwoord:

```
No Response Body

HTTP Status 202 Accepted
```

De volgende headers worden verzonden met de locatie van de uitvoer:

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

Maak een andere GET-aanroep naar de locatie. Het antwoord op de GET-aanroep is hetzelfde als de bewerking is voltooid of mislukt. Wanneer dit is voltooid, wordt de download-URL door de reactie op de aanroep locatie ophalen geretourneerd. Net alsof de bewerking op hetzelfde moment is uitgevoerd. Hier volgt een voorbeeld:

```
HTTP Status 200

{
  "id": "providers/Microsoft.Consumption/operationresults/{operationId}",
  "name": {operationId},
  "type": “Microsoft.Consumption/operationResults",
  "properties" : {
    "downloadUrl": {urltoblob},
    "validTill": "Date"
  }
}
```

De client kan ook een GET-aanroep voor de `Azure-AsyncOperation`maken. Het eind punt retourneert de status voor de bewerking.

De volgende tabel bevat velden in de oudere onderneming prijs lijst-API ophalen. Het bevat overeenkomende velden in het nieuwe prijzen overzicht voor micro soft-klant overeenkomsten:

| Oude eigenschap | Nieuwe eigenschap | Opmerkingen |
| --- | --- | --- |
| billingPeriodId  | _Niet van toepassing_ | Niet van toepassing. Voor klanten overeenkomsten van micro soft is het concept van billingPeriodId vervangen door de factuur en het bijbehorende prijs overzicht. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | De exacte teken reeks waarden kunnen verschillen. |
| includedQuantity  | includedQuantity | Niet van toepassing op Services in micro soft-klant overeenkomsten. |
| partNumber  | _Niet van toepassing_ | Gebruik in plaats daarvan een combi natie van productOrderName (hetzelfde als offerID) en meterID. |
| unitPrice  | unitPrice | Eenheids prijs is van toepassing op Services die worden gebruikt in micro soft-klant overeenkomsten. |
| currencyCode  | pricingCurrency | Micro soft-klant overeenkomsten hebben prijs representaties in de prijs-en facturerings valuta. De currencyCode komt overeen met de pricingCurrency in micro soft-klant overeenkomsten. |
| offerID | productOrderName | In plaats van OfferID kunt u productOrderName gebruiken, maar dit is niet hetzelfde als OfferID. ProductOrderName en meter bepalen prijzen in micro soft-klanten overeenkomsten met betrekking tot meterId en OfferID in verouderde inschrijvingen. |

## <a name="consumption-price-sheet-api-operations"></a>API-bewerkingen voor verbruik prijzen overzicht

Voor Enter prise agreements hebt u de verbruikte prijs lijst API [Get](/rest/api/consumption/pricesheet/get) en get by trans acties van de [facturerings periode](/rest/api/consumption/pricesheet/getbybillingperiod) gebruikt voor een scope van subscriptionId of een facturerings periode. De API maakt gebruik van Azure resource management-verificatie.

De prijslijst informatie voor een bereik met de API voor prijzen overzicht ophalen:

| Methode | Aanvraag-URI |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Prijslijst informatie ophalen per facturerings periode met de prijs lijst-API:

| Methode | Aanvraag-URI |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Gebruik in plaats van de bovenstaande API-eind punten de volgende voor micro soft-klant overeenkomsten:

**Prijs lijst-API voor micro soft-klant overeenkomsten (asynchroon REST API)**

Deze API is voor micro soft-klanten overeenkomsten en biedt extra kenmerken.

**Prijzen overzicht voor een facturerings profiel bereik in een facturerings account**

Deze API is de bestaande API. Het is bijgewerkt om het prijs overzicht voor een facturerings profiel in een facturerings account op te geven.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Prijs lijst voor een bereik per facturerings account

Azure Resource Manager-verificatie wordt gebruikt wanneer u het prijzen overzicht in het inschrijvings bereik in een facturerings account ophaalt.

Het prijzen overzicht ophalen bij het inschrijvings account in een facturerings account:

| Methode | Aanvraag-URI |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Voor een micro soft-klant overeenkomst gebruikt u de informatie in de volgende sectie. Deze bevat de veld eigenschappen die worden gebruikt voor micro soft-klant overeenkomsten.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Prijzen overzicht voor een facturerings profiel bereik in een facturerings account

Het bijgewerkte prijzen overzicht per facturerings account-API haalt het prijs overzicht op in CSV-indeling. U kunt als volgt het prijs overzicht voor het profiel van de facturerings bereik voor een MCA ophalen:

| Methode | Aanvraag-URI |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Bij het inschrijvings bereik van EA zijn de API-reactie en eigenschappen identiek. De eigenschappen komen overeen met dezelfde MCA-eigenschappen.

De oudere eigenschappen voor [Azure Resource Manager prijs lijst-api's](/rest/api/consumption/pricesheet) en dezelfde nieuwe eigenschappen bevinden zich in de volgende tabel.

| Verouderde Azure Resource Manager prijs lijst API-eigenschap  | Nieuwe micro soft-gebruikers overeenkomst prijs lijst API-eigenschap   | Beschrijving |
| --- | --- | --- |
| Id van de meter | _meterId_ | Unieke identificatie voor de meter. Hetzelfde als meterID. |
| Meter naam | meterName | Naam van de meter. Meter vertegenwoordigt de resource die kan worden geïmplementeerd voor de Azure-service. |
| Meter categorie  | service | Naam van de classificatiecategorie voor de meter. Hetzelfde als de service in het prijzen overzicht van de micro soft-klanten overeenkomst. De exacte teken reeks waarden verschillen. |
| Subcategorie van de meter | meterSubCategory | De naam van de subclassificatiecategorie voor de meter. Op basis van de classificatie van de functie set-differentiatie op hoog niveau in de service. Bijvoorbeeld Basic SQL DB versus Standard SQL DB. |
| Meter regio | meterRegion | &nbsp;  |
| Eenheid | _Niet van toepassing_ | Kan worden geparseerd vanuit unitOfMeasure. |
| Meeteenheid | unitOfMeasure | &nbsp;  |
| Onderdeel nummer | _Niet van toepassing_ | Gebruik in plaats van een deel nummer productOrderName en MeterID om de prijs van een facturerings profiel uniek te identificeren. Velden worden weer gegeven op de MCA-factuur in plaats van het onderdeel nummer in MCA-facturen. |
| Eenheidsprijs | unitPrice | Eenheids prijs van de klant overeenkomst van micro soft. |
| Valuta code | pricingCurrency | Micro soft-klanten overeenkomsten vertegenwoordigen prijzen in de prijs-en facturerings valuta. De valuta code is hetzelfde als de pricingCurrency in micro soft-klant overeenkomsten. |
| Inbegrepen hoeveelheid | includedQuantity | Niet van toepassing op Services in micro soft-klant overeenkomsten. Weer geven met waarden van nul. |
|  Aanbiedings-ID  | productOrderName | Gebruik productOrderName in plaats van OfferID. Niet hetzelfde als OfferID, maar de productOrderName en meter bepalen prijzen in micro soft-klant overeenkomsten. Gerelateerd aan meterId en OfferID bij verouderde inschrijvingen. |

De prijs voor klant overeenkomsten van micro soft is anders gedefinieerd dan Enter prise Agreements. De prijs voor services in de Enter prise-inschrijving is uniek voor het product, het onderdeel nummer, de meter en de aanbieding. Het onderdeel nummer wordt niet gebruikt in micro soft-klant overeenkomsten.

De prijs van de Azure-verbruiks service die deel uitmaakt van een micro soft-klant overeenkomst is uniek voor productOrderName en meterID. Ze vertegenwoordigen de service meter en het product plan.

Als u wilt afstemmen tussen het prijs overzicht en het gebruik in de API gebruiks Details, kunt u de productOrderName en meterID gebruiken.

Gebruikers met de rechten van de facturerings profiel eigenaar, Inzender, lezer en factuur Manager kunnen het prijzen overzicht downloaden.

Het prijzen overzicht bevat prijzen voor services waarvoor de prijs is gebaseerd op gebruik. De services omvatten Azure-verbruik en Marketplace-verbruik. De laatste prijs aan het einde van elke service periode is vergrendeld en toegepast op gebruik in één service periode. Voor Azure-verbruiks Services is de service periode doorgaans een kalender maand.

### <a name="retired-price-sheet-api-fields"></a>Buiten gebruik gesteld prijs lijst-API-velden

De volgende velden zijn niet beschikbaar in de micro soft-tarieven voor prijzen lijst van klanten of hebben dezelfde velden.

|Buiten gebruik gesteld veld| Beschrijving|
|---|---|
| billingPeriodId | Niet van toepassing. Komt overeen met InvoiceId voor MCA. |
| offerID | Niet van toepassing. Komt overeen met productOrderName in MCA. |
| meterCategory  | Niet van toepassing. Komt overeen met de service in MCA. |
| eenheid | Niet van toepassing. Kan worden geparseerd vanuit unitOfMeasure. |
| currencyCode | Hetzelfde als de pricingCurrency in MCA. |
| meterLocation | Hetzelfde als de meterRegion in MCA. |
| partNumber partnumber | Niet van toepassing omdat het onderdeel nummer niet wordt vermeld in MCA-facturen. Gebruik in plaats van een deel nummer de combi natie van meterId en productOrderName om prijzen uniek te identificeren. |
| totalIncludedQuantity | Niet van toepassing. |
| pretaxStandardRate  | Niet van toepassing. |

## <a name="reservation-instance-charge-api-replaced"></a>API voor reserverings instantie is vervangen

U kunt facturerings transacties voor reserverings aankopen ontvangen met de [API voor gereserveerde instantie kosten](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). De nieuwe API omvat alle aankopen, inclusief Marketplace-aanbiedingen van derden. Alle verbruiks-Api's worden vervangen door systeem eigen Azure-Api's die gebruikmaken van Azure AD voor verificatie en autorisatie. Zie aan de slag [met rest](/rest/api/azure/#create-the-request)voor meer informatie over het aanroepen van Azure rest api's. De API voor gereserveerde instantie kosten wordt vervangen door de trans actions-API.

Reserve ring van inkoop transacties ophalen met de trans actions-API:

| Methode | Aanvraag-URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>Aanbevelingen-Api's vervangen

Gereserveerde instanties kopen aanbevelingen voor het gebruik van virtuele machines in de afgelopen 7, 30 of 60 dagen. Api's bieden ook aanbevelingen voor reserverings aankopen. Deze omvatten:

- [Aanbevolen API voor gedeeld gereserveerde instanties](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [API voor enkelvoudige gereserveerde instanties](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

Alle verbruiks-Api's worden vervangen door systeem eigen Azure-Api's die gebruikmaken van Azure AD voor verificatie en autorisatie. Zie aan de slag [met rest](/rest/api/azure/#create-the-request)voor meer informatie over het aanroepen van Azure rest api's. De eerder genoemde reserverings aanbevelingen worden vervangen door de API [micro soft. verbruik/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) .

Reserverings aanbevelingen ophalen met de reserverings aanbevelingen API:

| Methode | Aanvraag-URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>Gebruiks-Api's voor reserve ring zijn vervangen

U kunt reserverings gebruik ophalen in een inschrijving met de Gebruik gereserveerde instanties-API. Als er meer dan één gereserveerde instantie is in een registratie, kunt u ook het gebruik van alle gereserveerde exemplaar aankopen ophalen met deze API.

Deze omvatten:

- [Gebruik gereserveerde instanties Details](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)
- [Gebruik gereserveerde instanties overzicht](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

Alle verbruiks-Api's worden vervangen door systeem eigen Azure-Api's die gebruikmaken van Azure AD voor verificatie en autorisatie. Zie aan de slag [met rest](/rest/api/azure/#create-the-request)voor meer informatie over het aanroepen van Azure rest api's. De eerder genoemde reserverings aanbevelingen worden vervangen door de api's [micro soft. verbruik/reservationDetails](/rest/api/consumption/reservationsdetails) en [micro soft. verbruik/reservationSummaries](/rest/api/consumption/reservationssummaries) .

Reserverings Details ophalen met de reserverings details API:

| Methode | Aanvraag-URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Reserverings overzichten ophalen met de API reserverings overzichten:

| Methode | Aanvraag-URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Van Cloudyn naar Cost Management verplaatsen

Organisaties die [Cloudyn](https://cloudyn.com) gebruiken, moeten gebruikmaken van [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) voor kosten beheer behoeften. Cost Management is beschikbaar in het Azure Portal zonder onboarding en een latentie van acht uur. Zie de [Cost Management-documentatie](index.yml)voor meer informatie.

Met Azure Cost Management kunt u:

- Kosten gedurende een periode op basis van een vooraf gedefinieerd budget weer geven. Dagelijkse kostenpatronen analyseren om afwijkende uitgaven te identificeren en te stoppen. Onderbreek de kosten per label, resource groep, service en locatie.
- Maak budgetten om limieten in te stellen voor het gebruik en de kosten en ontvang een melding wanneer er belang rijke drempel waarden worden bereikt. Automatisering instellen voor actiegroepen om aangepaste gebeurtenissen te activeren en definitieve limieten af te dwingen volgens uw eigen regels.
- Optimaliseer kosten en gebruik met aanbevelingen van Azure Advisor. Analyseer aankoop optimalisaties met reserve ringen, krimpen-ondergebruikte virtuele machines en Verwijder ongebruikte resources om binnen budgetten te blijven.
- Plan een kosten-en gebruiks gegevens export om dagelijks een CSV-bestand naar uw opslag account te publiceren. Automatiseer de integratie met externe systemen om facturerings gegevens synchroon en up-to-date te houden.

## <a name="power-bi-integration"></a>Integratie met Power BI

U kunt ook Power BI gebruiken voor kosten rapportage. De [Azure Cost Management connector](/power-bi/desktop-connect-azure-cost-management) voor Power bi Desktop kan worden gebruikt om krachtige, aangepaste rapporten te maken waarmee u uw Azure-uitgaven beter kunt begrijpen. De Azure Cost Management-connector biedt momenteel ondersteuning voor klanten met een micro soft-klant overeenkomst of een Enterprise Agreement (EA).

## <a name="next-steps"></a>Volgende stappen

- Lees de [Cost Management documentatie](index.yml) voor meer informatie over het bewaken en beheren van Azure-uitgaven. Of als u het resource gebruik wilt optimaliseren met Cost Management.
