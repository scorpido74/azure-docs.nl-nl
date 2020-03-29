---
title: Back-upbeleid maken met REST API
description: In dit artikel leert u hoe u back-upbeleid (planning en retentie) maken en beheren met behulp van REST API.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: 5ffc4115-0ae5-4b85-a18c-8a942f6d4870
ms.openlocfilehash: 0718ebc3612f53f1c2cc279096dd92de69bb5ef6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963849"
---
# <a name="create-azure-recovery-services-backup-policies-using-rest-api"></a>Back-upbeleid voor Azure Recovery Services maken met rest-API

De stappen voor het maken van een back-upbeleid voor een Azure Recovery Services-kluis worden beschreven in het [DOCUMENT REST API-document voor het beleid.](/rest/api/backup/protectionpolicies/createorupdate) Laten we dit document gebruiken als referentie om een beleid voor Azure VM-back-up te maken.

## <a name="create-or-update-a-policy"></a>Een beleid maken of bijwerken

Als u een Azure Backup-beleid wilt maken of bijwerken, gebruikt u de volgende *PUT-bewerking*

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupPolicies/{policyName}?api-version=2019-05-13
```

De `{policyName}` `{vaultName}` en zijn voorzien in de URI. Aanvullende informatie wordt verstrekt in de aanvraaginstantie.

## <a name="create-the-request-body"></a>De aanvraaginstantie maken

Als u bijvoorbeeld een beleid voor Azure VM-back-up wilt maken, volgt u de onderdelen van de aanvraaginstantie.

|Name  |Vereist  |Type  |Beschrijving  |
|---------|---------|---------|---------|
|properties     |   True      |  ProtectionPolicy:[AzureIaaSVMProtectionPolicy](/rest/api/backup/protectionpolicies/createorupdate#azureiaasvmprotectionpolicy)      | ProtectionPolicyResource eigenschappen        |
|tags     |         | Object        |  Resourcetags       |

Raadpleeg voor de volledige lijst met definities in de aanvraaginstantie het [REST API-document voor back-upbeleid](/rest/api/backup/protectionpolicies/createorupdate).

### <a name="example-request-body"></a>Voorbeeldaanvraaginstantie

In de volgende aanvraaginstantie wordt een back-upbeleid voor Azure VM-back-ups gedefinieerd.

Het beleid zegt:

- Neem een wekelijkse back-up elke maandag, woensdag, donderdag om 10:00 AM Pacific Standard Time.
- Bewaar de back-ups genomen op elke maandag, woensdag, donderdag voor een week.
- Bewaar de back-ups genomen op elke eerste woensdag en de derde donderdag van een maand voor twee maanden (overschrijft de vorige retentie voorwaarden, indien van toepassing).
- Bewaar de back-ups genomen op de vierde maandag en de vierde donderdag in februari en november voor vier jaar (overschrijft de vorige retentie voorwaarden, indien van toepassing).

```json
{
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "timeZone": "Pacific Standard Time",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ]
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    }
  }
}
```

> [!IMPORTANT]
> De tijdnotaties voor planning en retentie ondersteunen alleen DateTime. Ze ondersteunen niet alleen het tijdsformaat.

## <a name="responses"></a>Antwoorden

De creatie/update van het back-upbeleid is een [asynchrone bewerking](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Dit betekent dat deze bewerking een andere bewerking maakt die afzonderlijk moet worden bijgehouden.

Het retourneert twee antwoorden: 202 (Geaccepteerd) wanneer een andere bewerking wordt gemaakt en vervolgens 200 (OK) wanneer die bewerking is voltooid.

|Name  |Type  |Beschrijving  |
|---------|---------|---------|
|200 OK     |    [Protection PolicyResource](/rest/api/backup/protectionpolicies/createorupdate#protectionpolicyresource)     |  OK       |
|202 Geaccepteerd     |         |     Geaccepteerd    |

### <a name="example-responses"></a>Voorbeeldreacties

Zodra u de *PUT-aanvraag* voor het maken of bijwerken van het beleid hebt ingediend, is het eerste antwoord 202 (Geaccepteerd) met een locatiekop- of Azure-async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operations/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Houd vervolgens de resulterende bewerking bij met behulp van de locatiekop- of Azure-AsyncOperation-header met een eenvoudige *OPDRACHT GET.*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
```

Zodra de bewerking is voltooid, retourneert deze 200 (OK) met de beleidsinhoud in de reactieinstantie.

```json
{
  "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1",
  "name": "testPolicy1",
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies",
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ],
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleWeeklyFrequency": 0
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    },
    "timeZone": "Pacific Standard Time",
    "protectedItemsCount": 0
  }
}
```

Als een beleid al wordt gebruikt om een item te beschermen, leidt elke update in het beleid tot [het wijzigen van](backup-azure-arm-userestapi-backupazurevms.md#changing-the-policy-of-protection) de beveiliging voor al deze bijbehorende items.

## <a name="next-steps"></a>Volgende stappen

[Beveiliging inschakelen voor een onbeveiligde Azure VM.](backup-azure-arm-userestapi-backupazurevms.md)

Zie de volgende documenten voor meer informatie over de AZURE Backup REST API's:

- [REST-API van Azure Recovery Services-provider](/rest/api/recoveryservices/)
- [Aan de slag gaan met Azure REST API](/rest/api/azure/)
