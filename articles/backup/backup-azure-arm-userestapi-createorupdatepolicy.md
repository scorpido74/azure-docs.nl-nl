---
title: 'Azure Backup: back-upbeleid maken met behulp van REST API'
description: Back-upbeleid beheren (plannen en bewaren) met behulp van REST API
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: REST API; Back-ups van Azure-VM'S; Azure VM herstellen;
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: dacurwin
ms.assetid: 5ffc4115-0ae5-4b85-a18c-8a942f6d4870
ms.openlocfilehash: 50cc327c69529e420837571fdd60c1b2a1364b10
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73670140"
---
# <a name="create-azure-recovery-services-backup-policies-using-rest-api"></a>Back-upbeleid voor Azure Recovery Services maken met behulp van REST API

De stappen voor het maken van een back-upbeleid voor een Azure Recovery Services-kluis worden beschreven in het [beleid rest API document](https://docs.microsoft.com/rest/api/backup/protectionpolicies(2019-05-13)/createorupdate). Laat ons dit document gebruiken als referentie voor het maken van een beleid voor back-ups van Azure-VM'S.

## <a name="backup-policy-essentials"></a>Basis beginselen van back-upbeleid

- Er wordt een back-upbeleid per kluis gemaakt.
- Er kan een back-upbeleid worden gemaakt voor het maken van een back-up van de volgende werk belastingen
  - Azure VM
  - SQL in azure VM
  - Azure-bestandsshare
- Een beleid kan aan veel resources worden toegewezen. Een Azure VM-back-upbeleid kan worden gebruikt om veel virtuele Azure-machines te beveiligen.
- Een beleid bestaat uit twee onderdelen
  - Planning: wanneer de back-up moet worden gemaakt
  - Bewaren: voor hoe lang elke back-up moet worden bewaard.
- De planning kan worden gedefinieerd als dagelijks of wekelijks met een bepaald tijd punt.
- U kunt Bewaar perioden definiëren voor ' dagelijks ', ' Wekelijks ', ' maandelijks ', ' jaarlijks ' back-uppunten.
- ' Wekelijks ' verwijst naar een back-up op een bepaalde dag van de week, ' maandelijks ' betekent een back-up op een bepaalde dag van de maand en ' jaarlijks ' verwijst naar een back-up op een bepaalde dag van het jaar.
- Het bewaren van ' maandelijks ', ' jaarlijks ' back-uppunten wordt ' LongTermRetention ' genoemd.
- Wanneer een kluis wordt gemaakt, wordt er ook een beleid voor Azure VM-back-ups met de naam ' Defaultpolicy bij ' gemaakt en dit kan worden gebruikt voor het maken van back-ups van virtuele Azure-machines.

Als u een Azure Backup beleid wilt maken of bijwerken, gebruikt u de volgende *put* -bewerking

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupPolicies/{policyName}?api-version=2019-05-13
```

De `{policyName}` en `{vaultName}` zijn opgenomen in de URI. Meer informatie vindt u in de hoofd tekst van de aanvraag.

## <a name="create-the-request-body"></a>De aanvraag tekst maken

Als u bijvoorbeeld een beleid voor Azure VM backup wilt maken, volgt u de onderdelen van de hoofd tekst van de aanvraag.

|Naam  |Vereist  |Type  |Beschrijving  |
|---------|---------|---------|---------|
|properties     |   True      |  ProtectionPolicy:[AzureIaaSVMProtectionPolicy](https://docs.microsoft.com/rest/api/backup/protectionpolicies(2019-05-13)/createorupdate#azureiaasvmprotectionpolicy)      | ProtectionPolicyResource-eigenschappen        |
|tags     |         | Object        |  Resourcetags       |

Raadpleeg het [back-upbeleid rest API document](https://docs.microsoft.com/rest/api/backup/protectionpolicies(2019-05-13)/createorupdate)voor een volledige lijst met definities in de hoofd tekst van de aanvraag.

### <a name="example-request-body"></a>Voorbeeld aanvraag tekst

De volgende aanvraag tekst definieert een back-upbeleid voor back-ups van virtuele Azure-machines.

Het beleid vertelt:

- Neem elke maandag, woensdag, donderdag om 10:00 AM Pacific Standard Time een wekelijkse back-up.
- Bewaar de back-ups die zijn gemaakt op elke maandag, woensdag, donderdag gedurende één week.
- Bewaar de back-ups die zijn gemaakt op elke eerste woensdag en derde donderdag van een maand voor twee maanden (overschrijft de eerdere Bewaar voorwaarden, indien van toepassing).
- Bewaar de back-ups die zijn gemaakt op de vierde maandag en vierde donderdag in februari en november voor vier jaar (overschrijft de eerdere Bewaar voorwaarden, indien van toepassing).

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
> De tijd notaties voor planning en retentie ondersteunen alleen DateTime. Ze ondersteunen geen tijd notatie.

## <a name="responses"></a>Antwoorden

Het maken/bijwerken van het back-upbeleid is een [asynchrone bewerking](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Dit betekent dat met deze bewerking een andere bewerking wordt gemaakt die afzonderlijk moet worden bijgehouden.

Er worden twee antwoorden geretourneerd: 202 (geaccepteerd) wanneer een andere bewerking wordt gemaakt en vervolgens 200 (OK) wanneer deze bewerking is voltooid.

|Naam  |Type  |Beschrijving  |
|---------|---------|---------|
|200 OK     |    [Beveiligings PolicyResource](https://docs.microsoft.com/rest/api/backup/protectionpolicies(2019-05-13)/createorupdate#protectionpolicyresource)     |  OK       |
|202 geaccepteerd     |         |     Afgewezen    |

### <a name="example-responses"></a>Voorbeeld reacties

Zodra u de *put* -aanvraag voor het maken of bijwerken van beleid hebt verzonden, is het eerste antwoord 202 (geaccepteerd) met een locatie header of Azure-async-header.

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

Volg vervolgens de resulterende bewerking met behulp van de locatie header of de Azure-AsyncOperation-header met een eenvoudige *Get* -opdracht.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2019-05-13
```

Zodra de bewerking is voltooid, wordt 200 (OK) geretourneerd met de beleids inhoud in de hoofd tekst van het antwoord.

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

Als er al een beleid wordt gebruikt om een item te beveiligen, wordt bij elke update in het beleid de beveiliging voor alle gekoppelde items [gewijzigd](backup-azure-arm-userestapi-backupazurevms.md#changing-the-policy-of-protection) .

## <a name="next-steps"></a>Volgende stappen

[Schakel de beveiliging voor een niet-beveiligde Azure-vm in](backup-azure-arm-userestapi-backupazurevms.md).

Raadpleeg de volgende documenten voor meer informatie over de Azure Backup REST-Api's:

- [REST API Azure Recovery Services provider](/rest/api/recoveryservices/)
- [Aan de slag gaan met Azure REST API](/rest/api/azure/)
