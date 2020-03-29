---
title: Diagnostische logboeken voor Azure Data Lake Analytics inschakelen en weergeven
description: Informatie over het instellen en openen van diagnostische logboeken voor Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: cf5633d4-bc43-444e-90fc-f90fbd0b7935
ms.topic: conceptual
ms.date: 02/12/2018
ms.openlocfilehash: 7fd88383e909ebd6be64c22721b813946e37179e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60616490"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Diagnostische logboeken openen voor Azure Data Lake Analytics

Diagnostische logging u gegevens toegang audit paden te verzamelen. Deze logboeken bieden informatie zoals:

* Een lijst met gebruikers die toegang hebben tot de gegevens.
* Hoe vaak de gegevens worden geopend.
* Hoeveel gegevens in het account zijn opgeslagen.

## <a name="enable-logging"></a>Logboekregistratie inschakelen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Open uw Data Lake Analytics-account en selecteer **Diagnostische logboeken** in de sectie __Monitor.__ Selecteer vervolgens __Diagnostische gegevens inschakelen__.

    ![Diagnostische gegevens inschakelen om controle- en aanvraaglogboeken te verzamelen](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. Voer __in diagnose-instellingen__een __naam__ in voor deze logboekconfiguratie en selecteer vervolgens logboekopties.

    ![Diagnostische gegevens inschakelen om controle- en aanvraaglogboeken te verzamelen](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Diagnostische logboeken inschakelen")

   * U ervoor kiezen om de gegevens op drie verschillende manieren op te slaan/verwerken.

     * Selecteer __Archiveren naar een opslagaccount__ om logboeken op te slaan in een Azure-opslagaccount. Gebruik deze optie als u de gegevens wilt archiveren. Als u deze optie selecteert, moet u een Azure-opslagaccount opgeven om de logboeken op te slaan.

     * Selecteer **Streamen naar een gebeurtenishub** om logboekgegevens naar een Azure Event Hub te streamen. Gebruik deze optie als u een downstream verwerkingspijplijn hebt die binnenkomende logboeken in realtime analyseert. Als u deze optie selecteert, moet u de gegevens opgeven voor de Azure Event Hub die u wilt gebruiken.

     * Selecteer __Verzenden naar logboekanalyse__ om de gegevens naar de Azure Monitor-service te verzenden. Gebruik deze optie als u Azure Monitor-logboeken wilt gebruiken om logboeken te verzamelen en te analyseren.
   * Geef op of u controlelogboeken of aanvraaglogboeken of beide wilt opvragen.  Een aanvraaglogboek legt elke API-aanvraag vast. In een controlelogboek worden alle bewerkingen bijdie door die API-aanvraag worden geactiveerd.

   * Geef __voor Archiveren aan een opslagaccount__het aantal dagen op om de gegevens te bewaren.

   * Klik op __Opslaan__.

        > [!NOTE]
        > U moet __Archiveren naar een opslagaccount__selecteren, __streamen naar een gebeurtenishub__ of __Analytics verzenden voordat__ u op __Opslaan__ klikt.

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>Het Azure Storage-account gebruiken dat logboekgegevens bevat

1. Als u de blobcontainers met logboekregistratiegegevens wilt weergeven, opent u het Azure Storage-account dat wordt gebruikt voor Gegevensmeeranalyse voor logboekregistratie en klikt u op __Blobs__.

   * De container **insights-logs-audit** bevat de auditlogs.
   * De container **insights-logs-requests** bevatten de aanvraaglogboeken.

2. Binnen de containers worden de logboeken opgeslagen onder de volgende bestandsstructuur:

        resourceId=/
          SUBSCRIPTIONS/
            <<SUBSCRIPTION_ID>>/
              RESOURCEGROUPS/
                <<RESOURCE_GRP_NAME>>/
                  PROVIDERS/
                    MICROSOFT.DATALAKEANALYTICS/
                      ACCOUNTS/
                        <DATA_LAKE_ANALYTICS_NAME>>/
                          y=####/
                            m=##/
                              d=##/
                                h=##/
                                  m=00/
                                    PT1H.json

   > [!NOTE]
   > De `##` vermeldingen in het pad bevatten het jaar, de maand, de dag en het uur waarin het logboek is gemaakt. Data Lake Analytics maakt elk `m=` uur één bestand, dus bevat altijd een waarde van `00`.

    Het volledige pad naar een controlelogboek kan bijvoorbeeld zijn:

        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    Op dezelfde manier kan het volledige pad naar een aanvraaglogboek zijn:

        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>Logboekstructuur

De audit- en aanvraaglogboeken zijn in een gestructureerde JSON-indeling.

### <a name="request-logs"></a>Logboeken aanvragen

Hier is een voorbeeld vermelding in de JSON-geformatteerd aanvraaglogboek. Elke blob heeft één hoofdobject met records genaamd **records** die een array van logboekobjecten bevat.

    {
    "records":
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
             "category": "Requests",
             "operationName": "GetAggregatedJobHistory",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {
                 "HttpMethod":"POST",
                 "Path":"/JobAggregatedHistory",
                 "RequestContentLength":122,
                 "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
                 "StartTime":"2016-07-07T21:02:52.472Z",
                 "EndTime":"2016-07-07T21:02:53.456Z"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>Logboekschema aanvragen

| Name | Type | Beschrijving |
| --- | --- | --- |
| tijd |Tekenreeks |De tijdstempel (in UTC) van het logboek |
| resourceId |Tekenreeks |De identificatie van de resource waarop de bewerking heeft plaatsgevonden op |
| category |Tekenreeks |De logcategorie. Bijvoorbeeld **aanvragen**. |
| operationName |Tekenreeks |Naam van de bewerking die is geregistreerd. Bijvoorbeeld GetAggregatedJobHistory. |
| resultType |Tekenreeks |De status van de bewerking, bijvoorbeeld 200. |
| callerIpAddress |Tekenreeks |Het IP-adres van de klant die het verzoek indient |
| correlationId |Tekenreeks |De id van het logboek. Deze waarde kan worden gebruikt om een reeks gerelateerde logboekvermeldingen te groeperen. |
| identity |Object |De identiteit die het logboek heeft gegenereerd |
| properties |JSON |Zie de volgende sectie (Programma voor logboekeigenschappen aanvragen) voor meer informatie |

#### <a name="request-log-properties-schema"></a>Schema voor logboekeigenschappen aanvragen

| Name | Type | Beschrijving |
| --- | --- | --- |
| HttpMethod |Tekenreeks |De HTTP-methode die voor de bewerking wordt gebruikt. Bijvoorbeeld, GET. |
| Pad |Tekenreeks |Het pad waarop de bewerking is uitgevoerd |
| AanvraagContentLengte |int |De inhoudsduur van de HTTP-aanvraag |
| ClientRequestId |Tekenreeks |De id die deze aanvraag op unieke wijze identificeert |
| StartTime |Tekenreeks |Het tijdstip waarop de server het verzoek heeft ontvangen |
| EndTime |Tekenreeks |Het tijdstip waarop de server een antwoord heeft verzonden |

### <a name="audit-logs"></a>Auditlogboeken

Hier is een voorbeeld vermelding in de JSON-geformatteerd audit log. Elke blob heeft één hoofdobject met records genaamd **records** die een array van logboekobjecten bevat.

    {
    "records":
      [        
        . . . .
        ,
        {
             "time": "2016-07-28T19:15:16.245Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
             "category": "Audit",
             "operationName": "JobSubmitted",
             "identity": "user@somewhere.com",
             "properties": {
                 "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
                 "JobName": "New Job",
                 "JobRuntimeName": "default",
                 "SubmitTime": "7/28/2016 7:14:57 PM"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Schema van auditlogboek

| Name | Type | Beschrijving |
| --- | --- | --- |
| tijd |Tekenreeks |De tijdstempel (in UTC) van het logboek |
| resourceId |Tekenreeks |De identificatie van de resource waarop de bewerking heeft plaatsgevonden op |
| category |Tekenreeks |De logcategorie. Bijvoorbeeld **Audit**. |
| operationName |Tekenreeks |Naam van de bewerking die is geregistreerd. Bijvoorbeeld JobSubmitted. |
| resultType |Tekenreeks |Een substatus voor de taakstatus (operationName). |
| resultSignature |Tekenreeks |Meer informatie over de taakstatus (operationName). |
| identity |Tekenreeks |De gebruiker die de bewerking heeft aangevraagd. Bijvoorbeeld susan@contoso.com. |
| properties |JSON |Zie de volgende sectie (Programma eigenschappen van controlelogboeken) voor meer informatie |

> [!NOTE]
> **resultType** en **resultSignature** geven informatie over het resultaat van een bewerking en bevatten alleen een waarde als een bewerking is voltooid. Ze bevatten bijvoorbeeld alleen een waarde wanneer **operationName** een waarde van **JobStarted** of **JobEnded**bevat.
>
>

#### <a name="audit-log-properties-schema"></a>Programma voor controlelogboekeigenschappen

| Name | Type | Beschrijving |
| --- | --- | --- |
| JobId |Tekenreeks |De id die aan de taak is toegewezen |
| JobName |Tekenreeks |De naam die voor de taak is opgegeven |
| JobRunTime |Tekenreeks |De runtime die wordt gebruikt om de taak te verwerken |
| VerzendenTijd |Tekenreeks |De tijd (in UTC) dat de taak is ingediend |
| StartTime |Tekenreeks |De tijd dat de taak is gestart na indiening (in UTC) |
| EndTime |Tekenreeks |Het moment dat de taak is beëindigd |
| Parallellisme |Tekenreeks |Het aantal Data Lake Analytics-eenheden dat voor deze taak wordt aangevraagd tijdens het indienen |

> [!NOTE]
> **SubmitTime,** **StartTime,** **EndTime**en **Parallelisme** geven informatie over een bewerking. Deze vermeldingen bevatten alleen een waarde als die bewerking is gestart of voltooid. **SubmitTime** bevat bijvoorbeeld alleen een waarde nadat **operationName** de waarde **JobSubmitted**heeft .

## <a name="process-the-log-data"></a>De loggegevens verwerken

Azure Data Lake Analytics biedt een voorbeeld over het verwerken en analyseren van de loggegevens. U het [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample)monster vinden op.

## <a name="next-steps"></a>Volgende stappen
* [Overzicht van Data Lake Analytics](data-lake-analytics-overview.md)
