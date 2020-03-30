---
title: Azure API-beheer integreren met Azure Application Insights
titleSuffix: Azure API Management
description: Meer informatie over het registreren en weergeven van gebeurtenissen vanuit Azure API Management in Azure Application Insights.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 12aeea8393a00d7d2662c826f847265bdbdc0119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442713"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Azure API Management integreren met Azure Application Insights

Azure API Management maakt eenvoudige integratie met Azure Application Insights mogelijk - een uitbreidbare service voor webontwikkelaars die apps op meerdere platforms bouwen en beheren. Deze handleiding doorloopt elke stap van dergelijke integratie en beschrijft strategieën voor het verminderen van de impact op de prestaties op uw API Management-serviceinstantie.

## <a name="prerequisites"></a>Vereisten

Als u deze handleiding wilt volgen, moet u een Azure API Management-exemplaar hebben. Als u er geen hebt, voltooit u eerst de [zelfstudie.](get-started-create-service-instance.md)

## <a name="create-an-azure-application-insights-instance"></a>Een azure application insights-exemplaar maken

Voordat u Azure Application Insights gebruiken, moet u eerst een instantie van de service maken.

1. Open de **Azure-portal** en navigeer naar **Application Insights**.  
    ![App-inzichten maken](media/api-management-howto-app-insights/apim-app-insights-instance-1.png)  
2. Klik op **+ Toevoegen**.  
    ![App-inzichten maken](media/api-management-howto-app-insights/apim-app-insights-instance-2.png)  
3. Vul het formulier in. Selecteer **Algemeen** als **toepassingstype**.
4. Klik **op Maken**.

## <a name="create-a-connection-between-azure-application-insights-and-azure-api-management-service-instance"></a>Een verbinding maken tussen Azure Application Insights en Azure API Management-serviceinstantie

1. Navigeer naar de **instantie azure API Management-service** in de **Azure-portal.**
2. Selecteer **Toepassingsinzichten** in het menu aan de linkerkant.
3. Klik op **+ Toevoegen**.  
    ![App Insights logger](media/api-management-howto-app-insights/apim-app-insights-logger-1.png)  
4. Selecteer het eerder gemaakte **exemplaar Application Insights** en geef een korte beschrijving.
5. Klik **op Maken**.
6. U hebt zojuist een Azure Application Insights-logger gemaakt met een instrumentatiesleutel. Het moet nu in de lijst verschijnen.  
    ![App Insights logger](media/api-management-howto-app-insights/apim-app-insights-logger-2.png)  

> [!NOTE]
> Achter de scène wordt een [Logger-entiteit](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger/createorupdate) gemaakt in uw API-beheerexemplaar, met de instrumentatiesleutel van het exemplaar Application Insights.

## <a name="enable-application-insights-logging-for-your-api"></a>Logboekregistratie van Toepassingsinzichten inschakelen voor uw API

1. Navigeer naar de **instantie azure API Management-service** in de **Azure-portal.**
2. Selecteer **API's** in het menu aan de linkerkant.
3. Klik op uw API, in dit geval **Demo Conference API.**
4. Ga vanaf de bovenste balk naar het tabblad **Instellingen.**
5. Blader omlaag naar de sectie **Diagnostische logboeken.**  
    ![App Insights logger](media/api-management-howto-app-insights/apim-app-insights-api-1.png)  
6. Schakel het selectievakje **Inschakelen in.**
7. Selecteer uw bijgevoegde logger in de **vervolgkeuzelijst Bestemming.**
8. Invoer **100** als **Sampling (%)** en vink het selectievakje **Altijd logboekfouten** aan.
9. Klik op **Opslaan**.

> [!WARNING]
> Als u de standaardwaarde **0** in het veld **Eerste bytes van de hoofdtekst** overschrijft, kan de prestaties van uw API's aanzienlijk worden verlaagd.

> [!NOTE]
> Achter de schermen wordt op API-niveau een [diagnostische](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/diagnostic/createorupdate) entiteit met de naam 'applicationinsights' gemaakt.

| Naam van instelling                        | Waardetype                        | Beschrijving                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Inschakelen                              | booleaans                           | Hiermee geeft u op of logboekregistratie van deze API is ingeschakeld.                                                                                                                                                                                                                                                                                                |
| Doel                         | Azure Application Insights-logger | Hiermee geeft u azure application insights-logger op die moet worden gebruikt                                                                                                                                                                                                                                                                                           |
| Bemonstering (%)                        | decimal                           | Waarden van 0 tot 100 (procent). <br/> Hiermee geeft u op welk percentage aanvragen wordt geregistreerd bij Azure Application Insights. 0% sampling betekent nul gelogde aanvragen, terwijl 100% sampling betekent dat alle aanvragen worden geregistreerd. <br/> Deze instelling wordt gebruikt voor het verminderen van prestatieimplicaties van logboekregistratieaanvragen voor Azure Application Insights (zie het gedeelte hieronder). |
| Altijd fouten registreren                   | booleaans                           | Als deze instelling is geselecteerd, worden alle fouten geregistreerd bij Azure Application Insights, ongeacht de **instelling Sampling.**                                                                                                                                                                                                                  |
| Basisopties: kopteksten              | list                              | Hiermee geeft u de kopteksten op die worden vastgelegd in Azure Application Insights voor aanvragen en antwoorden.  Standaard: er worden geen kopteksten geregistreerd.                                                                                                                                                                                                             |
| Basisopties: eerste bytes van de body  | geheel getal                           | Hiermee geeft u op hoeveel eerste bytes van de hoofdtekst zijn aangemeld bij Azure Application Insights voor aanvragen en antwoorden.  Standaard: de hoofdtekst wordt niet geregistreerd.                                                                                                                                                                                                    |
| Geavanceerde opties: Verbosity         |                                   | Hiermee geeft u het verbositeitsniveau op. Alleen aangepaste sporen met een hoger ernstniveau worden geregistreerd. Standaard: informatie.                                                                                                                                                                                                                               |
| Geavanceerde opties: Frontend-aanvraag  |                                   | Hiermee geeft u op of en hoe *frontend-aanvragen* worden aangemeld bij Azure Application Insights. *Frontend-aanvraag* is een aanvraag die wordt ingediend bij de Azure API Management-service.                                                                                                                                                                        |
| Geavanceerde opties: Frontend-antwoord |                                   | Hiermee geeft u op of en hoe *frontend-antwoorden* worden vastgelegd op Azure Application Insights. *Frontend-respons* is een antwoord dat uitgaande is van de Azure API Management-service.                                                                                                                                                                   |
| Geavanceerde opties: Backend-aanvraag   |                                   | Hiermee geeft u op of en hoe *backend-aanvragen* worden aangemeld bij Azure Application Insights. *Backend-aanvraag* is een aanvraag die wordt uitgaande van de Azure API Management-service.                                                                                                                                                                        |
| Geavanceerde opties: backend-reactie  |                                   | Hiermee geeft u op of en hoe *backend-antwoorden* worden vastgelegd op Azure Application Insights. *Backend-respons* is een antwoord dat wordt binnengekomen op de Azure API Management-service.                                                                                                                                                                       |

> [!NOTE]
> U loggers op verschillende niveaus opgeven - enkele API-logger of een logger voor alle API's.
>  
> Als u beide opgeeft:
> + als ze verschillende loggers, dan zullen ze allebei worden gebruikt (multiplexing logs),
> + als ze dezelfde loggers, maar hebben verschillende instellingen, dan is de een voor enkele API (meer gedetailleerd niveau) zal overschrijven de een voor alle API's.

## <a name="what-data-is-added-to-azure-application-insights"></a>Welke gegevens worden toegevoegd aan Azure Application Insights

Azure Application Insights ontvangt:

+ Telemetrie-item *aanvragen* voor elk binnenkomend verzoek *(frontend request*, *frontend response),*
+ Telemetrie-item *afhankelijkheid,* voor elk verzoek dat wordt doorgestuurd naar een backendservice *(backend-aanvraag,* *backend-antwoord*),
+ *Exception* Uitzonderingstelemetrie-item, voor elke mislukte aanvraag.

Een mislukte aanvraag is een aanvraag, die:

+ mislukt vanwege een gesloten clientverbinding, of
+ een sectie op fout van het *API-beleid* heeft geactiveerd, of
+ heeft een antwoord HTTP-statuscode die overeenkomt met 4xx of 5xx.

## <a name="performance-implications-and-log-sampling"></a>Prestatieimplicaties en logboekbemonstering

> [!WARNING]
> Het registreren van alle gebeurtenissen kan ernstige gevolgen hebben voor de prestaties, afhankelijk van het aantal binnenkomende aanvragen.

Op basis van interne belastingstests zorgde het inschakelen van deze functie voor een vermindering van de doorvoer van 40%-50% wanneer de aanvraagsnelheid meer dan 1.000 aanvragen per seconde overschreed. Azure Application Insights is ontworpen om statistische analyse te gebruiken voor het beoordelen van toepassingsprestaties. Het is niet bedoeld als een auditsysteem en is niet geschikt voor het registreren van elk individueel verzoek om apuisten met een hoog volume.

U het aantal aanvragen manipuleren dat wordt geregistreerd door de **instelling Sampling** aan te passen (zie de bovenstaande stappen). Waarde 100% betekent dat alle aanvragen worden geregistreerd, terwijl 0% geen logboekregistratie weerspiegelt. **Sampling** helpt om het volume van telemetrie te verminderen, effectief te voorkomen van aanzienlijke verslechtering van de prestaties, terwijl nog steeds de voordelen van logging.

Het overslaan van het overslaan van headers en het lichaam van verzoeken en reacties zal ook een positief effect hebben op het verlichten van prestatieproblemen.

## <a name="video"></a>Video

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Volgende stappen

+ Meer informatie over [Azure Application Insights](https://docs.microsoft.com/azure/application-insights/).
+ Overweeg [om u te registreren met Azure Event Hubs](api-management-howto-log-event-hubs.md).
