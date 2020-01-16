---
title: Factureringsgegevens van het Azure Enterprise-enrollment controleren met REST API | Microsoft Docs
description: Meer informatie over het gebruik van Azure REST API's om de factureringsgegevens voor Enterprise-enrollment te controleren.
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: cost-management-billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 222b0358f027e0a6687ca0710e3cf5b80f292c4e
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75993464"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Facturering van Enterprise-enrollment controleren met behulp van REST API's

Met de rapportage-API van Azure kunt u uw Azure-kosten controleren en beheren.

In dit artikel vindt u informatie over het ophalen van de factureringsgegevens voor factureringsaccounts, afdelingen of Enterprise-overeenkomsten (EA) met behulp van de REST API's van Azure. 

## <a name="individual-account-billing"></a>Facturering voor individuele accounts

Gebruiksdetails voor accounts in een afdeling ophalen:

```http
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

De parameter `{billingAccountId}` is vereist en moet de id voor het account bevatten.

De volgende headers zijn vereist: 

|Aanvraagheader|Beschrijving|  
|--------------------|-----------------|  
|*Content-Type:*|Vereist. Ingesteld op `application/json`.|  
|*Authorization:*|Vereist. Stel in op een geldige `Bearer` [API-sleutel](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

In dit voorbeeld ziet u een synchrone aanroep waarmee details voor de huidige factureringscyclus worden geretourneerd. Uit prestatieoverwegingen retourneren synchrone aanroepen informatie over de afgelopen maand.  U kunt de [API ook asynchroon aanroepen](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) om gegevens te retourneren voor 36 maanden.


## <a name="response"></a>Antwoord  

Statuscode 200 (OK) wordt geretourneerd bij een geslaagd antwoord dat een lijst met gedetailleerde kosten voor het account bevat.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/BillingAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        ...
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        "isEstimated": false,
        ...
      }
    }
  ]
}
```  

Dit voorbeeld is ingekort; zie [Get usage details for a billing account](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslist-legacy) (Gebruiksgegevens ophalen voor een factureringsaccount) voor een volledige beschrijving van elk antwoordveld en foutafhandeling.

## <a name="department-billing"></a>Afdelingsfacturering 

Gebruiksdetails ophalen voor alle accounts in een afdeling. 

```http
GET https://management.azure.com/providers/Microsoft.Billing/departments/{departmentId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

De parameter `{departmentId}` is vereist en moet de id voor de afdeling in het inschrijvingsaccount bevatten.

De volgende headers zijn vereist: 

|Aanvraagheader|Beschrijving|  
|--------------------|-----------------|  
|*Content-Type:*|Vereist. Ingesteld op `application/json`.|  
|*Authorization:*|Vereist. Stel in op een geldige `Bearer` [API-sleutel](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

In dit voorbeeld ziet u een synchrone aanroep waarmee details voor de huidige factureringscyclus worden geretourneerd. Uit prestatieoverwegingen retourneren synchrone aanroepen informatie over de afgelopen maand.  U kunt de [API ook asynchroon aanroepen](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) om gegevens te retourneren voor 36 maanden.

### <a name="response"></a>Antwoord  

Statuscode 200 (OK) wordt geretourneerd voor een geslaagde reactie, die een lijst bevat met gedetailleerde gebruiksgegevens en -kosten voor een bepaalde factureringsperiode en factuur-id voor de afdeling.


In het volgende voorbeeld ziet u de uitvoer van de REST API voor de afdeling `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "instanceLocation": "eastasia",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
```  

Dit voorbeeld is ingekort; zie [Get usage details for a department](/rest/api/consumption/usagedetails/list#departmentusagedetailslist-legacy) (Gebruiksgegevens ophalen voor een afdeling) voor een volledige beschrijving van elk antwoordveld en foutafhandeling.

## <a name="enrollment-account-billing"></a>Facturering van inschrijvingsaccount

Geaggregeerde gebruiksgegevens ophalen voor het inschrijvingsaccount.

```http
GET GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/{enrollmentAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

De parameter `{enrollmentAccountId}` is vereist en moet de id van het inschrijvingsaccount bevatten.

De volgende headers zijn vereist: 

|Aanvraagheader|Beschrijving|  
|--------------------|-----------------|  
|*Content-Type:*|Vereist. Ingesteld op `application/json`.|  
|*Authorization:*|Vereist. Stel in op een geldige `Bearer` [API-sleutel](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

In dit voorbeeld ziet u een synchrone aanroep waarmee details voor de huidige factureringscyclus worden geretourneerd. Uit prestatieoverwegingen retourneren synchrone aanroepen informatie over de afgelopen maand.  U kunt de [API ook asynchroon aanroepen](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) om gegevens te retourneren voor 36 maanden.

### <a name="response"></a>Antwoord  

Statuscode 200 (OK) wordt geretourneerd voor een geslaagde reactie, die een lijst bevat met gedetailleerde gebruiksgegevens en -kosten voor een bepaalde factureringsperiode en factuur-id voor de afdeling.

In het volgende voorbeeld ziet u de uitvoer van de REST API voor de Enterprise-enrollment `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        ....
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
``` 

Dit voorbeeld is ingekort; zie [Get usage details for an enrollment account](/rest/api/consumption/usagedetails/list#enrollmentaccountusagedetailslist-legacy) (Gebruiksgegevens ophalen voor een enrollmentaccount) voor een volledige beschrijving van elk antwoordveld en foutafhandeling.

## <a name="next-steps"></a>Volgende stappen 
- Bekijk [Overzicht van Enterprise Reporting](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Onderzoek [REST API voor Enterprise-facturering](https://docs.microsoft.com/rest/api/billing/)   
- [Aan de slag gaan met Azure REST API](https://docs.microsoft.com/rest/api/azure/)   
