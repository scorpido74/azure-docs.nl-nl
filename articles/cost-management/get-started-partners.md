---
title: Aan de slag met Azure Cost Management voor partners
description: In dit artikel wordt uitgelegd hoe partners Azure Cost Management-functies gebruiken en hoe ze Cost Management toegang voor hun klanten mogelijk maken.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: cost-management
manager: aparnag
ms.custom: secdec18
ms.openlocfilehash: 611b3e608d9b0de9423c861ec70e9fc2e7ad67d5
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720753"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Aan de slag met Azure Cost Management voor partners

Azure Cost Management is standaard beschikbaar voor partners die hun klanten hebben opvolgd voor een klant overeenkomst van micro soft. In dit artikel wordt uitgelegd hoe partners [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/) -functies gebruiken. Ook wordt beschreven hoe partners Cost Management toegang bieden voor hun klanten. Klanten kunnen Cost Management-functies gebruiken wanneer ze zijn ingeschakeld door hun CSP-partner.

CSP-partners gebruiken Cost Management voor het volgende:

- Inzicht krijgen in gefactureerde kosten en de kosten aan de klant, abonnementen, resource groepen en services koppelen.
- Krijg een intuïtieve weer gave van de kosten [analyse](quick-acm-cost-analysis.md) van Azure met mogelijkheden voor het analyseren van kosten per klant, abonnement, resource groep, resource, meter, service en vele andere dimensies.
- Bekijk de resource kosten die een partner hebben ontvangen (PEC) die in de kosten analyse worden toegepast.
- Stel meldingen en automatisering in met behulp van programmeer [budgetten](tutorial-acm-create-budgets.md) en-waarschuwingen wanneer de kosten de budget overschrijden.
- Schakel het Azure Resource Manager-beleid in dat klanten toegang biedt tot Cost Management gegevens. Klanten kunnen gegevens over verbruiks kosten weer geven voor hun abonnementen met [betalen naar](https://azure.microsoft.com/pricing/calculator/)gebruik-tarieven.

Hier volgt een voor beeld van de kosten voor alle klanten.
![voor beeld van de kosten voor alle klanten](./media/get-started-partners/customer-costs1.png)

Hier volgt een voor beeld van de kosten voor één klant.
![voor beeld van de kosten voor één klant](./media/get-started-partners/customer-costs2.png)

Alle functionaliteit die beschikbaar is in Azure Cost Management is ook beschikbaar met REST Api's. Gebruik de Api's om kosten beheer taken te automatiseren.

## <a name="prerequisites"></a>Vereisten

Azure Cost Management hebt lees toegang tot uw facturerings account of-abonnement nodig. Toegang kan op elk niveau boven uw resources worden verleend, van het facturerings account of een beheer groep tot afzonderlijke resource groepen waar u uw apps beheert. Zie [gebruikers rollen en machtigingen toewijzen](/partner-center/permissions-overview)voor meer informatie over het inschakelen en toewijzen van toegang tot Azure Cost Management voor een facturerings account. De rol van **globale beheerder** en **beheerder agent** kan de kosten voor een facturerings account beheren.

Zie [inzicht in cost management gegevens](understand-cost-mgt-data.md)voor een volledige lijst met ondersteunde account typen.


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
- Resource
- Azure-service
- Meter
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

Meld u aan bij de partner-Tenant in het Azure Portal en klik op **Cost Management + facturering**. Selecteer een facturerings account en klik vervolgens op **klanten**. De lijst met klanten is gekoppeld aan het facturerings account.

Selecteer in de lijst met klanten de klant waarvoor u de kosten wilt weer geven.

![Klanten selecteren in Cost Management](./media/get-started-partners/customer-list.png)

Klik onder **instellingen**op **beleid**.

Het huidige beleid voor de zichtbaarheid van kosten wordt weer gegeven voor **Azure-gebruiks** kosten die zijn gekoppeld aan de abonnementen voor de geselecteerde klant.
![beleid waarmee klanten kosten voor betalen per gebruik kunnen weer geven](./media/get-started-partners/cost-management-billing-policies.png)

Wanneer het beleid is ingesteld op **Nee**, is Azure Cost Management niet beschikbaar voor abonnements gebruikers die zijn gekoppeld aan de klant. Tenzij deze is ingeschakeld door een partner, wordt het beleid voor kosten zichtbaarheid standaard uitgeschakeld voor alle gebruikers van het abonnement.

Wanneer het kosten beleid is ingesteld op **Ja**, kunnen abonnements gebruikers die zijn gekoppeld aan de Tenant van de klant, de gebruiks kosten bekijken bij betalen naar gebruik-tarieven.

Wanneer het beleid voor het berekenen van de kosten is ingeschakeld, worden voor alle services waarvoor het abonnements abonnement geldt kosten op basis van betalen naar gebruik-tarieven weer gegeven. Het gebruik van de reserve ring wordt weer gegeven met nul kosten voor de werkelijke en afgeschreven kosten. Aankopen en rechten zijn niet gekoppeld aan een specifiek abonnement. Aankopen worden dus niet weer gegeven in het abonnements bereik.

Als u de kosten voor de Tenant van de klant wilt weer geven, opent u Cost Management + facturering en klikt u vervolgens op facturerings accounts. Klik in de lijst met facturerings accounts op een facturerings account.

![Een facturerings account selecteren](./media/get-started-partners/select-billing-account.png)

Klik onder **facturering**op **Azure-abonnementen**en klik vervolgens op een klant.

![Een klant van een Azure-abonnement selecteren](./media/get-started-partners/subscriptions-select-customer.png)

Klik op **kosten analyse** en Bekijk de kosten.
Kosten analyse, budgetten en waarschuwingen zijn beschikbaar voor het abonnement en de RBAC-scopes van de resource groep tegen kosten op basis van betalen per gebruik-tarief.

![Kosten analyse als een klant weer geven ](./media/get-started-partners/customer-tenant-view-cost-analysis.png)

Bij afgeschreven weer gaven en werkelijke kosten voor gereserveerde instanties in de RBAC-bereiken worden nulwaarden weer gegeven. Kosten voor gereserveerde instanties worden alleen weer gegeven in facturerings bereik waar de aankopen zijn gedaan.

## <a name="analyze-costs-in-cost-analysis"></a>Kosten analyseren in kosten analyse

Partners kunnen kosten ontdekken en analyseren voor de kosten analyse van klanten voor een specifieke klant of voor een factuur.

De volgende velden zijn te vinden in detail bestanden voor gebruik en Cost Management-Api's. U kunt filteren en groeperen op functies in Cost Analysis gebruiken om kosten te analyseren op basis van meerdere velden. Zie [Cost Management gegevens velden](understand-cost-mgt-data.md#cost-management-data-fields)om een volledige lijst met velden weer te geven.

| Veldnaam | Beschrijving |
| --- | --- |
| CustomerTenantID | De id van de Azure Active Directory Tenant van het&#39;abonnement van de klant. |
| CustomerName | De naam van de Azure Active Directory Tenant voor het&#39;abonnement van de klant s. |
| CustomerTenantDomainName | De domein naam voor de Azure Active Directory-Tenant van&#39;het abonnement van de klant s. |
| PartnerTenantID | De id voor de&#39;partner-Azure Active Directory Tenant. |
| PartnerName | De naam van de partner Azure Active Directory Tenant. |
| ResellerMPNID | MPNID voor de wederverkoper die aan het abonnement is gekoppeld. |
| costinUSD | Geschatte ExtendedCost of overvloei kosten vóór belasting in USD. |
| paygCostInBillingCurrency | Hiermee worden de kosten weer gegeven als de prijzen een verkoop prijs zijn. Hiermee worden de prijzen voor betalen naar gebruik in de facturerings valuta weer gegeven. Alleen beschikbaar voor RBAC-bereiken. |
| paygCostInUSD | Hiermee worden de kosten weer gegeven als de prijzen een verkoop prijs zijn. Toont de prijzen voor betalen naar gebruik in USD. Alleen beschikbaar voor RBAC-bereiken. |
| partnerEarnedCreditRate | Het tarief van de korting wordt toegepast als er een door de partner aangehaalde credit (PEC) is op basis van de toegang tot de partner beheerder. |
| partnerEarnedCreditApplied | Geeft aan of het tegoed van de partner is toegepast. |

In de weer gave [kosten analyse](quick-acm-cost-analysis.md) kunt u ook [weer gaven opslaan](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) en gegevens exporteren naar [CSV-en PNG-bestanden](quick-acm-cost-analysis.md#automation-and-offline-analysis).

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
PUT
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/budgets/{budgetName}?api-version=2019-10-01
```


## <a name="next-steps"></a>Volgende stappen
- [Begin](quick-acm-cost-analysis.md) met het analyseren van de kosten in Cost Management
- [Budgetten in cost management maken en beheren](tutorial-acm-create-budgets.md)
