---
title: Aan de slag met Azure Cost Management voor partners
description: In dit artikel wordt uitgelegd hoe partners Azure Cost Management-functies gebruiken en hoe ze Cost Management toegang voor hun klanten mogelijk maken.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: aparnag
ms.custom: secdec18
ms.openlocfilehash: 9d95e23cf92c7ee98291831d60088d610c3e5c52
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377692"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Aan de slag met Azure Cost Management voor partners

Azure Cost Management is standaard beschikbaar voor partners die hun klanten hebben opvolgd voor een klant overeenkomst van micro soft. In dit artikel wordt uitgelegd hoe partners [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/) -functies gebruiken. Ook wordt beschreven hoe partners Cost Management toegang bieden voor hun klanten. CSP-klanten kunnen Cost Management-functies gebruiken wanneer ze zijn ingeschakeld door hun CSP-partner.

CSP-partners gebruiken Cost Management voor het volgende:

- Inzicht krijgen in gefactureerde kosten en de kosten aan de klant, abonnementen, resource groepen en services koppelen.
- Krijg een intuïtieve weer gave van de kosten [analyse](quick-acm-cost-analysis.md) van Azure met mogelijkheden voor het analyseren van kosten per klant, abonnement, resource groep, resource, meter, service en vele andere dimensies.
- Bekijk de resource kosten die een partner hebben ontvangen (PEC) die in de kosten analyse worden toegepast.
- Stel meldingen en automatisering in met behulp van programmeer [budgetten](tutorial-acm-create-budgets.md) en-waarschuwingen wanneer de kosten de budget overschrijden.
- Schakel het Azure Resource Manager-beleid in dat klanten toegang biedt tot Cost Management gegevens. Klanten kunnen gegevens over verbruiks kosten weer geven voor hun abonnementen met [betalen naar](https://azure.microsoft.com/pricing/calculator/)gebruik-tarieven.

Alle functionaliteit die beschikbaar is in Azure Cost Management is ook beschikbaar met REST Api's. Gebruik de Api's om kosten beheer taken te automatiseren.

## <a name="prerequisites"></a>Vereisten

Azure Cost Management hebt lees toegang tot uw facturerings account of-abonnement nodig. Toegang kan op elk niveau boven uw resources worden verleend, van het facturerings account of een beheer groep tot afzonderlijke resource groepen waar u uw apps beheert. Om ervoor te zorgen dat abonnements gebruikers prijzen en kosten kunnen bekijken, moet de toegang tot de kosten voor het weer geven van uw facturerings account zijn ingeschakeld. Zie [toegang tot gegevens toewijzen](assign-access-acm-data.md)voor meer informatie over het inschakelen en toewijzen van toegang tot Azure Cost Management. Zie [inzicht in cost management gegevens](understand-cost-mgt-data.md)voor een volledige lijst met ondersteunde account typen.


## <a name="how-cost-management-uses-scopes"></a>Hoe Cost Management scopes gebruikt?

Met bereiken beheert u facturerings gegevens, beschikt u over functies die specifiek zijn voor betalingen, het weer geven van facturen en het uitvoeren van algemeen account beheer. Facturerings-en account rollen worden afzonderlijk beheerd vanaf scopes die worden gebruikt voor resource beheer, die gebruikmaakt van RBAC. Om het doel van de afzonderlijke bereiken duidelijk te onderscheiden, met inbegrip van de verschillen in toegangs beheer, worden ze respectievelijk facturerings bereiken en RBAC-bereiken genoemd.

Zie [begrijpen en werken met bereiken](understand-work-scopes.md)voor meer informatie over de facturerings bereiken en RBAC-bereiken en hoe cost management werkt met scopes.

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>Kosten beheren met het facturerings bereik van een partner-Tenant

Nadat u uw klanten aan een micro soft-klant overeenkomst hebt toegevoegd, zijn de volgende _facturerings bereiken_ beschikbaar in uw Tenant. Gebruik de bereiken voor het beheren van de kosten in Cost Management.

### <a name="billing-account-scope"></a>Bereik van facturerings account

Gebruik het bereik van het facturerings account om kosten voor preheffingen voor al uw klanten en facturerings profielen weer te geven. U kunt ook factuur kosten voor op verbruik gebaseerde producten voor klanten weer geven op de micro soft-klanten overeenkomst. Factuur kosten worden ook weer gegeven voor aankopen op basis van producten voor klanten op de micro soft-klanten overeenkomst en de CSP-aanbieding. Op dit moment is de standaard valuta voor het weer geven van de kosten in het bereik Amerikaanse dollars. Budgetten die zijn ingesteld voor het bereik, zijn ook in USD.

Ongeacht de klant-gefactureerde valuta, gebruiken partners het bereik om budgetten in te stellen en de kosten in USD te beheren voor hun klanten, abonnementen, resources en resource groepen.

Partners filteren ook kosten in een specifieke factuur valuta voor klanten in de weer gave kosten analyse. Selecteer de lijst met **werkelijke kosten** om de kosten in ondersteunde facturerings valuta's van klanten weer te geven.

![Voor beeld van de werkelijke kosten selectie voor valuta's](./media/get-started-partners/actual-cost-selector.png)

Gebruik de [weer gave afgeschreven kosten](quick-acm-cost-analysis.md#customize-cost-views) in facturerings bereik om de afgeschreven kosten van gereserveerde instanties voor een reserverings termijn weer te geven.

### <a name="billing-profile-scope"></a>Bereik van facturerings profiel

Gebruik het bereik van het facturerings profiel om de kosten voor preheffing in de facturerings valuta voor al uw klanten te bekijken voor alle producten en abonnementen die in een factuur zijn opgenomen. U kunt de kosten in een facturerings profiel voor een specifieke factuur filteren met behulp van het **InvoiceID** -filter. Het filter toont het verbruik en de aanschaf kosten van het product voor een specifieke factuur. U kunt ook de kosten voor een specifieke klant op de factuur filteren om de kosten voor de belasting vooraf te bekijken.

Nadat u klanten hebt opvolgd voor een micro soft-klant overeenkomst, ontvangt u een klant factuur met de kosten voor recht op aangekochte producten, zoals SaaS, Azure Marketplace en reserve ringen. Wanneer de factuur in dezelfde facturerings valuta wordt gefactureerd, worden ook de kosten van klanten weer gegeven die niet zijn opgenomen in de nieuwe micro soft-klant overeenkomst.

Voor het afstemmen van de kosten voor de klant factuur kunt u met het bereik van het facturerings profiel alle kosten bekijken die toenemen voor een factuur voor uw klanten. Net als bij de factuur toont het bereik de kosten voor elke klant in de nieuwe klant overeenkomst van micro soft. In het bereik worden ook de kosten voor klant rechten weer gegeven die nog in de huidige CSP-aanbieding zijn opgenomen.

Het facturerings profiel en het bereik van de facturerings account zijn de enige die de kosten voor rechten en op aankoop gebaseerde producten tonen.

In facturerings profielen worden de abonnementen gedefinieerd die zijn opgenomen in een factuur. Facturerings profielen zijn het functionele equivalent van een Enter prise Agreement-inschrijving. Een inschrijving is het bereik dat facturen worden gegenereerd. Op dezelfde manier zijn aankopen waarvoor geen gebruik is gemaakt, zoals Azure Marketplace en reserve ringen, alleen beschikbaar voor het bereik van het facturerings profiel.

Op dit moment is de facturerings valuta van de klant de standaard valuta bij het weer geven van de kosten in het bereik van het facturerings profiel. Budgetten die zijn ingesteld op het bereik van het facturerings profiel, worden in de facturerings valuta opgenomen.

Partners kunnen het bereik gebruiken voor het afstemmen op facturen. En ze gebruiken het bereik om budgetten in te stellen in de facturerings valuta voor een:

- Specifieke gefilterde factuur
- Gebruikers
- Abonnement
- Resourcegroep
- Bron
- Azure-service
- Naar gebruik
- ResellerMPNID

### <a name="customer-scope"></a>Bereik van klant

Partners gebruiken de scope voor het beheren van de kosten die zijn gekoppeld aan klanten die onboarding voor de micro soft-klant overeenkomst zijn. Met het bereik kunnen partners de kosten van de preheffing voor een specifieke klant weer geven. U kunt ook de kosten vóór belastingen voor een specifiek abonnement, resource groep of resource filteren.

Het bereik van de klant bevat geen klanten die zich op de huidige CSP-aanbieding bevinden. De rechten kosten, niet voor Azure-gebruik, voor huidige CSP-aanbieding klanten zijn beschikbaar op het facturerings account en het bereik van facturerings profielen wanneer u het klant filter toepast.

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Partner toegang tot de facturerings bereiken in Cost Management

Alleen gebruikers met de rol van **globale beheerder** en **beheerder** kunnen kosten voor facturerings accounts, facturerings profielen en klanten rechtstreeks in de Azure-Tenant van de partner beheren en weer geven. Zie [gebruikers rollen en machtigingen toewijzen](/partner-center/permissions-overview)voor meer informatie over de functies van het partner centrum.

### <a name="enable-cost-management-in-the-customer-tenant"></a>Kosten beheer inschakelen in de Tenant van de klant

Partners kunnen toegang tot Cost Management mogelijk maken nadat klanten zijn opvolgd voor een klant overeenkomst van micro soft. Vervolgens kunnen partners een beleid inschakelen waarmee klanten hun kosten bekijken die worden berekend op basis van betalen naar gebruik-tarieven. Kosten worden weer gegeven in de facturerings valuta van de klant voor het verbruikte gebruik van het abonnement en de resource groepen van het RBAC.

Wanneer het beleid voor de zicht baarheid van kosten wordt ingeschakeld door de partner, kan elke gebruiker met Azure Resource Manager toegang tot het abonnement kosten op basis van betalen naar gebruik-tarieven beheren en analyseren. Effectief kunnen wederverkopers en klanten die de juiste RBAC-toegang hebben tot de Azure-abonnementen, kosten bekijken.

Ongeacht het beleid kunnen partners ook de kosten weer geven als ze toegang hebben tot het abonnement en de resource groep.

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>Het beleid inschakelen voor het weer geven van Azure-gebruiks kosten

Partners gebruiken de volgende informatie om het beleid in te scha kelen voor het weer geven van Azure-gebruiks kosten voor hun klanten.

Meld u aan bij de partner-Tenant in het Azure Portal en klik op **Cost Management + facturering**. Selecteer een facturerings account en klik vervolgens op **klanten**. De lijst met klanten is gekoppeld aan het facturerings account.

Selecteer in de lijst met klanten de klant waarvoor u de kosten wilt weer geven.

![Klanten selecteren in Cost Management](./media/get-started-partners/customer-list.png)

Klik onder **instellingen**op **beleid**.

Het huidige beleid voor de zichtbaarheid van kosten wordt weer gegeven voor **Azure-gebruiks** kosten die zijn gekoppeld aan de abonnementen voor de geselecteerde klant.
![Policy zodat klanten betalen per gebruik-kosten kunnen weer geven @ no__t-1

Wanneer het beleid is ingesteld op **Nee**, is Azure Cost Management niet beschikbaar voor abonnements gebruikers die zijn gekoppeld aan de klant. Tenzij deze is ingeschakeld door een partner, wordt het beleid voor kosten zichtbaarheid standaard uitgeschakeld voor alle gebruikers van het abonnement.

Wanneer het kosten beleid is ingesteld op **Ja**, kunnen abonnements gebruikers die zijn gekoppeld aan de Tenant van de klant, de gebruiks kosten bekijken bij betalen naar gebruik-tarieven.

Wanneer het beleid voor het berekenen van de kosten is ingeschakeld, worden voor alle services waarvoor het abonnements abonnement geldt kosten op basis van betalen naar gebruik-tarieven weer gegeven. Het gebruik van de reserve ring wordt weer gegeven met nul kosten voor de werkelijke en afgeschreven kosten. Aankopen en rechten zijn niet gekoppeld aan een specifiek abonnement. Aankopen worden dus niet weer gegeven in het abonnements bereik.

Als u de kosten voor de Tenant van de klant wilt weer geven, opent u Cost Management + facturering en klikt u vervolgens op facturerings accounts. Klik in de lijst met facturerings accounts op een facturerings account.

![Een facturerings account selecteren](./media/get-started-partners/select-billing-account.png)

Klik onder **facturering**op **Azure-abonnementen**en klik vervolgens op een klant.

![Een klant van een Azure-abonnement selecteren](./media/get-started-partners/subscriptions-select-customer.png)

Klik op **kosten analyse** en Bekijk de kosten.
Kosten analyse, budgetten en waarschuwingen zijn nu beschikbaar voor het abonnement en de RBAC-scopes van de resource groep tegen kosten op basis van betalen per gebruik-tarief.

![Kosten analyse als een klant weer geven ](./media/get-started-partners/customer-tenant-view-cost-analysis.png)

Bij afgeschreven weer gaven en werkelijke kosten voor gereserveerde instanties in de RBAC-bereiken worden nulwaarden weer gegeven. Kosten voor gereserveerde instanties worden alleen weer gegeven in facturerings bereik waar de aankopen zijn gedaan.

## <a name="analyze-costs-in-cost-analysis"></a>Kosten analyseren in kosten analyse

Partners kunnen kosten ontdekken en analyseren voor de kosten analyse van klanten voor een specifieke klant of voor een factuur. Met de functies filter en groeperen op kunt u kosten analyseren op basis van meerdere velden, waaronder:

| **Veld** | **Beschrijving** | **Gelijkwaardige kolom in het partner centrum** |
| --- | --- | --- |
| PartnerTenantID | Id voor de Azure Active Directory Tenant van de partner | Partner Azure Active Directory TenantID wordt aangeroepen als partner-ID. In GUID-indeling. |
| PartnerName | De naam van de partner Azure Active Directory Tenant | Naam van de partner |
| CustomerTenantID | Id van de Azure Active Directory Tenant van het abonnement van de klant | Organisatie-ID van de klant. Bijvoorbeeld klant Azure Active Directory TenantID. |
| CustomerName | De naam van de Azure Active Directory Tenant die het abonnement van de klant bevat | De organisatie naam van de klant, zoals gerapporteerd in het partner centrum. Belang rijk voor het afstemmen van de factuur met uw systeem gegevens. |
| ResellerMPNID | MPNID voor de wederverkoper die aan het abonnement is gekoppeld | MPN-ID van de wederverkoper van de record voor het abonnement. Niet beschikbaar voor huidige activiteit. |
| subscription ID | Unieke door micro soft gegenereerde id voor het Azure-abonnement | N/A |
| subscriptionName | De naam van het Azure-abonnement | N/A |
| billingProfileID | De id voor het facturerings profiel. Er worden kosten in rekening voor facturen gegroepeerd in één facturerings valuta voor klanten. | Facturerings groep-ID van MCAPI-partner. Wordt gebruikt in API-aanvragen, maar niet opgenomen in reacties. |
| invoiceID | Factuur-ID voor de factuur waarin de specifieke trans actie wordt weer gegeven | Factuur nummer waar de opgegeven trans actie wordt weer gegeven. |
| resourceGroup | De naam van de Azure-resource groep. Wordt gebruikt voor het beheer van resource levenscyclus. | De naam van de resource groep. |
| partnerEarnedCreditRate | De korting wordt toegepast als er een partner (PEC) wordt gebruikt op basis van de toegang tot de partner beheerder. | De frequentie van het tegoed van de partner (PEC). Bijvoorbeeld 0% of 15%. |
| partnerEarnedCreditApplied | Geeft aan of het tegoed van de partner is toegepast. | N/A |

In de weer gave [kosten analyse](quick-acm-cost-analysis.md) kunt u ook [weer gaven opslaan](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) en gegevens exporteren naar [CSV-en PNG](quick-acm-cost-analysis.md#automation-and-offline-analysis) -bestanden.

## <a name="view-partner-earned-credit-pec-resource-costs"></a>Resource kosten voor het ontvangen van partners (PEC) weer geven

In Azure Cost Management kunnen partners kosten analyse gebruiken om de kosten te bekijken die de PEC-voor delen hebben ontvangen.

Meld u aan bij de partner-Tenant in het Azure Portal en selecteer **Cost Management + facturering**. Klik onder **Cost Management**op **kosten analyse**.

In de weer gave kosten analyse worden de kosten van het facturerings account voor de partner weer gegeven. Selecteer het **bereik** dat nodig is voor de partner, een specifieke klant of een facturerings profiel om facturen af te stemmen.

Klik in een ring diagram op de vervolg keuzelijst en selecteer **PartnerEarnedCreditApplied** om in te zoomen op PEC-kosten.

![Voor beeld van het weer geven van het tegoed van een partner](./media/get-started-partners/cost-analysis-pec1.png)

Wanneer de eigenschap **PartnerEarnedCreditApplied** is ingesteld op _True_, hebben de bijbehorende kosten het voor deel van de gehaalde beheerder van de partner.

Wanneer de eigenschap **PartnerEarnedCreditApplied** _False_is, voldoen de gekoppelde kosten niet aan het vereiste recht voor het tegoed. Of de aangeschafte service komt niet in aanmerking voor het tegoed van de partner.

Service gebruiks gegevens hebben doorgaans 8-24 uur om weer te geven in Cost Management. Zie de [Update frequentie van gebruiks gegevens varieert](understand-cost-mgt-data.md#usage-data-update-frequency-varies)voor meer informatie. PEC-tegoeden worden binnen 48 uur na de toegangs tijd weer gegeven in Azure Cost Management.


U kunt ook groeperen en filteren op de eigenschap **PartnerEarnedCreditApplied** met behulp van de **Group by** -opties. Gebruik de opties om kosten te onderzoeken die geen PEC hebben.

![Groeperen of filteren op het tegoed van de partner](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="cost-management-rest-apis"></a>Cost Management REST-Api's

Partners, indirecte providers en klanten kunnen Cost Management Api's gebruiken die worden beschreven in de volgende secties voor algemene taken.

### <a name="azure-cost-management-apis-for-partners"></a>Azure Cost Management Api's voor partners

Partners en gebruikers met toegang tot de facturerings bereiken in een partner-Tenant kunnen de volgende Api's gebruiken.

#### <a name="to-get-a-list-of-billing-accounts"></a>Een lijst met facturerings accounts ophalen

```
armclient get "providers/Microsoft.billing/billingAccounts?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-customers"></a>Een lijst met klanten ophalen

```
armclient get "providers/Microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers?api-version=2019-10-01-preview"
```
#### <a name="to-get-a-list-of-subscriptions"></a>Een lijst met abonnementen ophalen

```
armclient get "/providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/billingSubscriptions?api-version=2019-10-01-preview"
```

#### <a name="to-create-new-subscription"></a>Nieuw abonnement maken

```
armclient post "/providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview" @createsub.json -verbose
```

#### <a name="to-get-or-download-usage-for-azure-services"></a>Het gebruik van Azure-Services ophalen of downloaden

```
armclient GET /providers/Microsoft.Billing/BillingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-get-a-list-of-billing-profiles"></a>Een lijst met facturerings profielen ophalen

```
armclient get "providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/billingProfiles?api-version=2019-10-01-preview
```

#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>Het prijzen overzicht voor verbruikte Azure-Services ophalen of downloaden

```
armclient post "/providers/Microsoft.Billing/BillingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/BillingProfiles/JUT6-EU3Q-BG7-TGB/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

#### <a name="to-get-customer-costs-for-the-last-two-months-sorted-by-month"></a>De klant kosten voor de afgelopen twee maanden ophalen, gesorteerd op maand

```
armclient post providers/microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryCustomer.json
```

#### <a name="to-get-azure-subscription-costs-for-the-last-two-months-sorted-by-month"></a>Kosten voor Azure-abonnementen voor de afgelopen twee maanden ophalen, gesorteerd op maand

```
armclient post providers/microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQuerySubscription.json
```

#### <a name="to-get-daily-costs-for-the-current-month"></a>Dagelijkse kosten voor de huidige maand ophalen

```
armclient post providers/microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryDaily.json
```

#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>Om het beleid te verkrijgen voor klanten om de kosten te bekijken

```
armclient get "providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/policies/default?api-version=2019-10-01-preview"
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>Het beleid voor klanten instellen om kosten te bekijken

```
armclient put "providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/policies/default?api-version=2019-10-01-preview" @policy.json
```

### <a name="azure-cost-management-apis-for-indirect-providers"></a>Azure Cost Management Api's voor indirecte providers

Indirecte providers met toegang tot RBAC-bereiken in een Tenant van de klant kunnen de volgende Api's gebruiken. Meld u aan als gebruiker of met een Service-Principal om aan de slag te gaan.

#### <a name="to-get-the-billing-account-information"></a>De gegevens van het facturerings account ophalen

```
armclient get "providers/Microsoft.billing/billingAccounts?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-customers"></a>Een lijst met klanten ophalen

```
armclient get "providers/Microsoft.billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-resellers-associated-with-the-customer"></a>Een lijst ophalen met wederverkopers die zijn gekoppeld aan de klant

```
armclient get "/providers/Microsoft.Billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers/b51df1fa-62fa-4c92-9a74-fe860016d4db?api-version=2019-10-01-preview&$expand=resellers
```

#### <a name="to-get-a-list-of-subscriptions-with-reseller-information"></a>Een lijst met abonnementen met wederverkoper-informatie ophalen

```
armclient get "/providers/Microsoft.Billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers/b51df1fa-62fa-4c92-9a74-fe860016d4db/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-create-a-subscription"></a>Een abonnement maken

```
armclient post "/providers/Microsoft.Billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers/b51df1fa-62fa-4c92-9a74-fe860016d4db/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview" @createsub_reseller.json
```

### <a name="azure-cost-management-apis-for-customers"></a>Azure Cost Management-Api's voor klanten

Klanten gebruiken de volgende informatie om toegang te krijgen tot de Api's. Meld u aan als gebruiker om aan de slag te gaan.

#### <a name="to-get-or-download-azure-consumption-usage-information-with-retail-rates"></a>Informatie over gebruik van Azure-verbruik met retail tarieven ophalen of downloaden

```
armclient post /subscriptions/66bada28-271e-4b7a-aaf5-c0ead63923d7/providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryDaily.json
```

## <a name="next-steps"></a>Volgende stappen
- [Begin](quick-acm-cost-analysis.md) met het analyseren van de kosten in Cost Management
- [Budgetten in cost management maken en beheren](tutorial-acm-create-budgets.md)
