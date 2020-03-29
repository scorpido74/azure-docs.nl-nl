---
title: Diagnostische logboeken voor Azure Data Lake Storage Gen1 | Microsoft Documenten
description: 'Informatie over het instellen en openen van diagnostische logboeken voor Azure Data Lake Storage Gen1 '
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: d200f72b3c0e5634c3dca8f60a4754a14351110a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60878684"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-storage-gen1"></a>Diagnostische logboeken openen voor Azure Data Lake Storage Gen1
Lees diagnostische logboekregistratie voor uw Azure Data Lake Storage Gen1-account en hoe u de logboeken weergeven die voor uw account zijn verzameld.

Organisaties kunnen diagnostische logboekregistratie voor hun Azure Data Lake Storage Gen1-account inschakelen om controlepaden voor gegevenstoegang te verzamelen die informatie bevatten, zoals een lijst van gebruikers die toegang hebben tot de gegevens, hoe vaak de gegevens worden geopend, hoeveel gegevens in het account worden opgeslagen, enz. Wanneer ingeschakeld, worden de diagnostiek en/of verzoeken op een best-effort basis geregistreerd. Zowel aanvragen als logboekvermeldingen voor diagnostische gegevens worden alleen gemaakt als er aanvragen zijn ingediend tegen het eindpunt van de service.

## <a name="prerequisites"></a>Vereisten
* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Data Lake Storage Gen1-account**. Volg de instructies bij [Aan de slag met Azure Data Lake Storage Gen1 met behulp van de Azure Portal.](data-lake-store-get-started-portal.md)

## <a name="enable-diagnostic-logging-for-your-data-lake-storage-gen1-account"></a>Diagnostische logboekregistratie inschakelen voor uw Data Lake Storage Gen1-account
1. Meld u aan bij de nieuwe [Azure-portal](https://portal.azure.com).
2. Open uw Data Lake Storage Gen1-account en klik vanaf uw Data Lake Storage Gen1-accountblad op **Diagnostische instellingen**.
3. Klik in het **blad Diagnostische instellingen** op Diagnostische gegevens **inschakelen**.

    ![registratie in het diagnoselogboek inschakelen](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "Diagnostische logboeken inschakelen")

3. Breng in het blad **Diagnostische instellingen** de volgende wijzigingen aan om diagnostische logboekregistratie te configureren.
   
    ![registratie in het diagnoselogboek inschakelen](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Diagnostische logboeken inschakelen")
   
   * Voer voor **Naam**een waarde in voor de configuratie van het diagnostisch logboek.
   * U ervoor kiezen om de gegevens op verschillende manieren op te slaan/verwerken.
     
        * Selecteer de optie om **te archiveren naar een opslagaccount** om logboeken op te slaan in een Azure Storage-account. U gebruikt deze optie als u de gegevens wilt archiveren die op een later tijdstip worden verwerkt. Als u deze optie selecteert, moet u een Azure Storage-account opgeven om de logboeken op te slaan.
        
        * Selecteer de optie streamen **naar een gebeurtenishub** om logboekgegevens naar een Azure Event Hub te streamen. Hoogstwaarschijnlijk gebruikt u deze optie als u een downstream verwerkingspijplijn hebt om binnenkomende logboeken in realtime te analyseren. Als u deze optie selecteert, moet u de gegevens opgeven voor de Azure Event Hub die u wilt gebruiken.

        * Selecteer de optie die u **wilt verzenden naar logboekanalyse** om de Azure Monitor-service te gebruiken om de gegenereerde logboekgegevens te analyseren. Als u deze optie selecteert, moet u de gegevens opgeven voor de werkruimte Log Analytics die u de loganalyse zou gebruiken. Zie [Gegevens weergeven of analyseren die zijn verzameld met Azure Monitor-logboeken, zoeken naar](../azure-monitor/learn/tutorial-viewdata.md) details over het gebruik van Azure Monitor-logboeken.
     
   * Geef op of u controlelogboeken of aanvraaglogboeken of beide wilt opvragen.
   * Geef het aantal dagen op waarvoor de gegevens moeten worden bewaard. Bewaar is alleen van toepassing als u Azure-opslagaccount gebruikt om logboekgegevens te archiveren.
   * Klik op **Opslaan**.

Zodra u diagnostische instellingen hebt ingeschakeld, u de logboeken bekijken op het tabblad **Diagnostische logboeken.**

## <a name="view-diagnostic-logs-for-your-data-lake-storage-gen1-account"></a>Diagnostische logboeken weergeven voor uw Data Lake Storage Gen1-account
Er zijn twee manieren om de logboekgegevens voor uw Data Lake Storage Gen1-account te bekijken.

* Vanuit de weergave Gegevensmeeropslag Gen1-accountinstellingen
* Vanuit het Azure Storage-account waar de gegevens worden opgeslagen

### <a name="using-the-data-lake-storage-gen1-settings-view"></a>De instellingenweergave Voor Gegevensmeeropslag Gen1 gebruiken
1. Klik in het blade **Settings** Van uw Data Lake Storage Gen1-accountinstellingen op **Diagnostische logboeken**.
   
    ![Diagnostische logboeken weergeven](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Diagnostische logboeken weergeven") 
2. In het blad **Diagnostische logboeken** ziet u de logboeken die zijn gecategoriseerd **door controlelogboeken** en **aanvraaglogboeken**.
   
   * Aanvraaglogboeken leggen elke API-aanvraag vast die is gedaan op het Data Lake Storage Gen1-account.
   * Audit logs zijn vergelijkbaar met het aanvragen van logboeken, maar bieden een veel meer gedetailleerde uitsplitsing van de bewerkingen die worden uitgevoerd op de Data Lake Storage Gen1 account. Een enkele api-aanroep voor uploaden in aanvraaglogboeken kan bijvoorbeeld resulteren in meerdere bewerkingen 'Toevoegen' in de controlelogboeken.
3. Als u de logboeken wilt downloaden, klikt u op de **koppeling Downloaden** voor elke logboekvermelding.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Vanuit het Azure Storage-account dat logboekgegevens bevat
1. Open het Azure Storage-accountblad dat is gekoppeld aan Data Lake Storage Gen1 voor logboekregistratie en klik op Blobs. Het **Blob-serviceblad** bevat twee containers.
   
    ![Diagnostische logboekregistratie weergeven](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Diagnostische logboeken weergeven")
   
   * De container **insights-logs-audit** bevat de auditlogs.
   * De container **insights-logs-requests** bevatten de aanvraaglogboeken.
2. Binnen deze containers worden de logboeken opgeslagen onder de volgende structuur.
   
    ![Diagnostische logboekregistratie weergeven](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Diagnostische logboeken weergeven")
   
    Zo kan het volledige pad naar een controlelogboek`https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    Op dezelfde manier kan het volledige pad naar een aanvraaglogboek`https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>De structuur van de loggegevens begrijpen
De controle- en aanvraaglogboeken zijn in een JSON-indeling. In deze sectie kijken we naar de structuur van JSON voor aanvraag- en auditlogs.

### <a name="request-logs"></a>Logboeken aanvragen
Hier is een voorbeeld vermelding in de JSON-geformatteerd aanvraaglogboek. Elke blob heeft één hoofdobject met records genaamd **records** die een array van logboekobjecten bevat.

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
             "category": "Requests",
             "operationName": "GETCustomerIngressEgress",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>Logboekschema aanvragen
| Name | Type | Beschrijving |
| --- | --- | --- |
| tijd |Tekenreeks |De tijdstempel (in UTC) van het logboek |
| resourceId |Tekenreeks |De id van de resource waarop de bewerking plaatsvond |
| category |Tekenreeks |De logcategorie. Bijvoorbeeld **aanvragen**. |
| operationName |Tekenreeks |Naam van de bewerking die is geregistreerd. Bijvoorbeeld getfilestatus. |
| resultType |Tekenreeks |De status van de bewerking, bijvoorbeeld 200. |
| callerIpAddress |Tekenreeks |Het IP-adres van de klant die het verzoek indient |
| correlationId |Tekenreeks |De id van het logboek dat kan worden gebruikt om een reeks gerelateerde logboekvermeldingen samen te voegen |
| identity |Object |De identiteit die het logboek heeft gegenereerd |
| properties |JSON |Zie hieronder voor meer informatie |

#### <a name="request-log-properties-schema"></a>Schema voor logboekeigenschappen aanvragen
| Name | Type | Beschrijving |
| --- | --- | --- |
| HttpMethod |Tekenreeks |De HTTP-methode die voor de bewerking wordt gebruikt. Bijvoorbeeld, GET. |
| Pad |Tekenreeks |Het pad waarop de bewerking is uitgevoerd |
| AanvraagContentLengte |int |De inhoudsduur van de HTTP-aanvraag |
| ClientRequestId |Tekenreeks |De id die dit verzoek op unieke wijze identificeert |
| StartTime |Tekenreeks |Het tijdstip waarop de server het verzoek heeft ontvangen |
| EndTime |Tekenreeks |Het tijdstip waarop de server een antwoord heeft verzonden |

### <a name="audit-logs"></a>Auditlogboeken
Hier is een voorbeeld vermelding in de JSON-geformatteerd audit log. Elke blob heeft één hoofdobject met records genaamd **records** die een array van logboekobjecten bevat

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-08T19:08:59.359Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
             "category": "Audit",
             "operationName": "SeOpenStream",
             "resultType": "0",
             "resultSignature": "0",
             "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
             "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
             "properties": {"StreamName":"adl://<data_lake_storage_gen1_account_name>.azuredatalakestore.net/logs.csv"}
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Schema van auditlogboek
| Name | Type | Beschrijving |
| --- | --- | --- |
| tijd |Tekenreeks |De tijdstempel (in UTC) van het logboek |
| resourceId |Tekenreeks |De id van de resource waarop de bewerking plaatsvond |
| category |Tekenreeks |De logcategorie. Bijvoorbeeld **Audit**. |
| operationName |Tekenreeks |Naam van de bewerking die is geregistreerd. Bijvoorbeeld getfilestatus. |
| resultType |Tekenreeks |De status van de bewerking, bijvoorbeeld 200. |
| resultSignature |Tekenreeks |Meer informatie over de bewerking. |
| correlationId |Tekenreeks |De id van het logboek dat kan worden gebruikt om een reeks gerelateerde logboekvermeldingen samen te voegen |
| identity |Object |De identiteit die het logboek heeft gegenereerd |
| properties |JSON |Zie hieronder voor meer informatie |

#### <a name="audit-log-properties-schema"></a>Programma voor controlelogboekeigenschappen
| Name | Type | Beschrijving |
| --- | --- | --- |
| StreamName |Tekenreeks |Het pad waarop de bewerking is uitgevoerd |

## <a name="samples-to-process-the-log-data"></a>Voorbeelden om de loggegevens te verwerken
Wanneer logboeken worden verzonden van Azure Data Lake Storage Gen1 naar Azure Monitor-logboeken (zie [Gegevens weergeven of analyseren die zijn verzameld met Azure Monitor-logboeken zoeken naar](../azure-monitor/learn/tutorial-viewdata.md) details over het gebruik van Azure Monitor-logboeken), retourneert de volgende query een tabel met een lijst met weergavenamen van gebruikers, het tijdstip van de gebeurtenissen en het aantal gebeurtenissen voor de gebeurtenis, samen met een visueel diagram. Het kan eenvoudig worden aangepast om GUID of andere kenmerken van de gebruiker weer te geven:

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


Azure Data Lake Storage Gen1 biedt een voorbeeld over het verwerken en analyseren van de loggegevens. U het [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample)monster vinden op. 

## <a name="see-also"></a>Zie ook
* [Overzicht van Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md)

