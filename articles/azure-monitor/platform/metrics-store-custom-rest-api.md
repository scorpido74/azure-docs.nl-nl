---
title: Statistieken verzenden naar de azure monitor-metrische database met rest-API
description: Aangepaste statistieken voor een Azure-bron verzenden naar de Azure Monitor-metrische opslag met behulp van een REST-API
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 84709c022631543101889f784231158ebb96b6f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662261"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-by-using-a-rest-api"></a>Aangepaste statistieken voor een Azure-bron verzenden naar de Azure Monitor-metrische opslag met behulp van een REST-API

In dit artikel ziet u hoe u aangepaste statistieken voor Azure-resources verzenden naar de Azure Monitor-statistiekenopslag via een REST-API. Nadat de statistieken zich in Azure Monitor bevinden, u alle dingen met deze statistieken doen die u met standaardstatistieken doet. Voorbeelden hiervan zijn het in kaart brengen, waarschuwen en routeren naar andere externe hulpprogramma's.  

>[!NOTE]  
>De REST API staat alleen het verzenden van aangepaste statistieken voor Azure-bronnen toe. Als u statistieken wilt verzenden voor resources in verschillende omgevingen of on-premises, u [Application Insights](../../azure-monitor/app/api-custom-events-metrics.md)gebruiken.    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>Een serviceprincipal maken en autoriseren om statistieken uit te stoten 

Maak een serviceprincipal in uw Azure Active Directory-tenant met behulp van de instructies die zijn gevonden bij [Een serviceprincipal maken.](../../active-directory/develop/howto-create-service-principal-portal.md) 

Let op het volgende terwijl u dit proces doorloopt: 

- U elke URL voor de aanmeldings-URL invoeren.  
- Maak een nieuw clientgeheim voor deze app.  
- Bewaar de sleutel en de client-id voor gebruik in latere stappen.  

Geef de app die is gemaakt als onderdeel van stap 1, Monitoring Metrics Publisher, machtigingen voor de resource waartegen u statistieken wilt uitzenden. Als u van plan bent de app te gebruiken om aangepaste statistieken uit te zenden tegen veel bronnen, u deze machtigingen toestaan op resourcegroep- of abonnementsniveau. 

## <a name="get-an-authorization-token"></a>Een autorisatietoken aanvragen
Open een opdrachtprompt en voer de volgende opdracht uit:

```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step>" -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
Sla het toegangstoken op vanuit het antwoord.

![Access-token](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>De statistiek uitzenden via de REST API 

1. Plak de volgende JSON in een bestand en sla het op als **custommetric.json** op uw lokale computer. Werk de tijdparameter in het JSON-bestand bij: 
    
    ```json
    { 
        "time": "2018-09-13T16:34:20", 
        "data": { 
            "baseData": { 
                "metric": "QueueDepth", 
                "namespace": "QueueProcessing", 
                "dimNames": [ 
                  "QueueName", 
                  "MessageType" 
                ], 
                "series": [ 
                  { 
                    "dimValues": [ 
                      "ImagesToProcess", 
                      "JPEG" 
                    ], 
                    "min": 3, 
                    "max": 20, 
                    "sum": 28, 
                    "count": 3 
                  } 
                ] 
            } 
        } 
    } 
    ``` 

1. Plaats de metrische gegevens in het opdrachtpromptvenster: 
   - **azureRegion**. Moet overeenkomen met het implementatiegebied van de resource waarvoor u statistieken uitzendt. 
   - **resourceID**.  Resource-id van de Azure-bron waartegen u de statistiek bijhoudt.  
   - **AccessToken**. Plak het token dat u eerder hebt aangeschaft.

     ```Shell 
     curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId>/metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
     ```
1. Wijzig de tijdstempel en waarden in het JSON-bestand. 
1. Herhaal de vorige twee stappen een paar keer, zodat u gegevens voor enkele minuten hebt.

## <a name="troubleshooting"></a>Problemen oplossen 
Als u een foutbericht ontvangt met een deel van het proces, moet u rekening houden met de volgende informatie over probleemoplossing:

1. U geen statistieken afgeven voor een abonnement of brongroep als uw Azure-bron. 
1. Je geen statistiek in de winkel plaatsen die meer dan 20 minuten oud is. De metrische winkel is geoptimaliseerd voor waarschuwingen en real-time grafieken. 
2. Het aantal dimensienamen moet overeenkomen met de waarden en vice versa. Controleer de waarden. 
2. Mogelijk geeft u statistieken uit tegen een regio die geen aangepaste statistieken ondersteunt. Zie [ondersteunde regio's](../../azure-monitor/platform/metrics-custom-overview.md#supported-regions). 



## <a name="view-your-metrics"></a>Bekijk uw statistieken 

1. Meld u aan bij Azure Portal. 

1. Selecteer **Monitor**in het linkermenu . 

1. Selecteer op de pagina **Monitor** de optie **Metrische gegevens**. 

   ![Statistieken selecteren](./media/metrics-store-custom-rest-api/metrics.png) 

1. Wijzig de aggregatieperiode in **Laatste 30 minuten**.  

1. Selecteer **in** de vervolgkeuzelijst resource de resource waartegen u de statistiek hebt uitgestoten.  

1. Selecteer **QueueProcessing**in het vervolgkeuzemenu **Naamruimten** . 

1. Selecteer **QueueDepth**in het vervolgkeuzemenu **Metstatistieken** .  

 
## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [aangepaste statistieken](../../azure-monitor/platform/metrics-custom-overview.md).

