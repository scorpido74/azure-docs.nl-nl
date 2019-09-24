---
title: Azure API Management integreren met Azure-toepassing inzichten | Microsoft Docs
description: Meer informatie over het registreren en weer geven van gebeurtenissen vanuit Azure API Management in Azure-toepassing Insights.
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
ms.openlocfilehash: ae467e3def65d446a8c331c4f15033b4c01886ae
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219489"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Azure API Management integreren met Azure-toepassing inzichten

Azure API Management biedt eenvoudige integratie met Azure-toepassing Insights: een uitbreid bare service voor webontwikkelaars die apps op meerdere platforms bouwen en beheren. In deze hand leiding wordt elke stap van deze integratie uitgelegd en worden strategieën beschreven voor het verminderen van de prestaties van uw API Management service-exemplaar.

## <a name="prerequisites"></a>Vereisten

Als u deze hand leiding wilt volgen, moet u een Azure API Management-exemplaar hebben. Als u er nog geen hebt, voltooit u eerst de [zelf studie](get-started-create-service-instance.md) .

## <a name="create-an-azure-application-insights-instance"></a>Een Azure-toepassing Insights-exemplaar maken

Voordat u Azure-toepassing Insights kunt gebruiken, moet u eerst een exemplaar van de service maken.

1. Open de **Azure Portal** en navigeer naar **Application Insights**.  
    ![App Insights maken](media/api-management-howto-app-insights/apim-app-insights-instance-1.png)  
2. Klik op **+ Toevoegen**.  
    ![App Insights maken](media/api-management-howto-app-insights/apim-app-insights-instance-2.png)  
3. Vul het formulier in. Selecteer **Algemeen** als het **toepassings type**.
4. Klik op **Create**.

## <a name="create-a-connection-between-azure-application-insights-and-azure-api-management-service-instance"></a>Een verbinding maken tussen Azure-toepassing Insights-en Azure API Management service-exemplaar

1. Navigeer naar uw **Azure API Management service-exemplaar** in de **Azure Portal**.
2. Selecteer **Application Insights** in het menu aan de linkerkant.
3. Klik op **+ Toevoegen**.  
    ![Logboek voor app Insights](media/api-management-howto-app-insights/apim-app-insights-logger-1.png)  
4. Selecteer het eerder gemaakte **Application Insights** -exemplaar en geef een korte beschrijving op.
5. Klik op **Create**.
6. U hebt zojuist een Azure-toepassing Insights-logboek registratie gemaakt met een instrumentatie sleutel. Deze wordt nu weer gegeven in de lijst.  
    ![Logboek voor app Insights](media/api-management-howto-app-insights/apim-app-insights-logger-2.png)  

> [!NOTE]
> Achter de scène wordt een [logger](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger/createorupdate) -entiteit gemaakt in uw API Management-exemplaar met de instrumentatie sleutel van de Application Insights instantie.

## <a name="enable-application-insights-logging-for-your-api"></a>Application Insights logboek registratie inschakelen voor uw API

1. Navigeer naar uw **Azure API Management service-exemplaar** in de **Azure Portal**.
2. Selecteer **API's** in het menu aan de linkerkant.
3. Klik op uw API, in dit geval **demo**van de API van de vergadering.
4. Ga naar het tabblad **instellingen** op de bovenste balk.
5. Schuif omlaag naar de sectie **Diagnostische logboeken** .  
    ![Logboek voor app Insights](media/api-management-howto-app-insights/apim-app-insights-api-1.png)  
6. Schakel het **selectie vakje in** .
7. Selecteer de bijgevoegde logboek registratie in de vervolg keuzelijst **bestemming** .
8. Invoer **100** als **steek proef (%)** en tik op het selectie vakje **altijd logboek fouten** .
9. Klik op **Opslaan**.

> [!WARNING]
> Het overschrijven van de standaard waarde **0** in de **eerste bytes van het veld hoofd tekst** kan de prestaties van uw api's aanzienlijk verminderen.

> [!NOTE]
> Achter de scène wordt een [Diagnostische](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/diagnostic/createorupdate) entiteit met de naam ' applicationinsights ' gemaakt op het API-niveau.

| Naam van instelling                        | Waardetype                        | Description                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Inschakelen                              | boolean                           | Hiermee wordt aangegeven of logboek registratie van deze API is ingeschakeld.                                                                                                                                                                                                                                                                                                |
| Destination                         | Azure-toepassing Insights-logboek registratie | Hiermee wordt aangegeven Azure-toepassing Insights-logboeken moeten worden gebruikt                                                                                                                                                                                                                                                                                           |
| Sampling (%)                        | decimal                           | Waarden van 0 tot 100 (procent). <br/> Hiermee geeft u op welk percentage van aanvragen wordt geregistreerd bij Azure-toepassing inzichten. met een steek proef van 0% worden er nul aanvragen geregistreerd, terwijl 100% de steek proef alle geregistreerde aanvragen houdt. <br/> Deze instelling wordt gebruikt voor het verminderen van de prestatie implicaties van logboek registratie aanvragen voor het Azure-toepassing van inzichten (Zie de sectie hieronder). |
| Altijd fouten in logboek registreren                   | boolean                           | Als deze instelling is geselecteerd, worden alle fouten geregistreerd in Azure-toepassing inzichten, ongeacht de **bemonsterings** instelling.                                                                                                                                                                                                                  |
| Basis opties: Headers              | list                              | Hiermee geeft u de koppen op die worden vastgelegd Azure-toepassing inzichten op aanvragen en antwoorden.  Standaard: er worden geen headers vastgelegd.                                                                                                                                                                                                             |
| Basis opties: Eerste bytes van de hoofd tekst  | integer                           | Hiermee geeft u op hoeveel eerste bytes van de hoofd tekst worden vastgelegd in Azure-toepassing inzichten op aanvragen en antwoorden.  Standaard: de hoofd tekst is niet geregistreerd.                                                                                                                                                                                                    |
| Geavanceerde opties: Uitbreidings         |                                   | Hiermee geeft u het uitbreidings niveau op. Alleen aangepaste traceringen met een hoger Ernst niveau worden vastgelegd. Prijs Gegevens.                                                                                                                                                                                                                               |
| Geavanceerde opties: Front-end-aanvraag  |                                   | Hiermee wordt aangegeven of en hoe de front- *End-aanvragen* worden geregistreerd bij Azure-toepassing Insights. De front- *End-aanvraag* is een aanvraag die binnenkomend is voor de Azure API Management-service.                                                                                                                                                                        |
| Geavanceerde opties: Frontend-antwoord |                                   | Hiermee wordt aangegeven of en hoe en hoe *frontend-antwoorden* worden vastgelegd in azure-toepassing Insights. *Frontend-antwoord* is een antwoord van de Azure API Management-service.                                                                                                                                                                   |
| Geavanceerde opties: Back-end-aanvraag   |                                   | Hiermee wordt aangegeven of en hoe *back-aanvragen* worden geregistreerd voor Azure-toepassing inzichten. *Back-end-aanvraag* is een uitgaande aanvraag van de Azure API Management-service.                                                                                                                                                                        |
| Geavanceerde opties: Reactie van back-end  |                                   | Hiermee geeft u op of en hoe *back-end-antwoorden* worden vastgelegd voor Azure-toepassing inzichten. *Back-end-respons* is een antwoord dat binnenkomt bij de Azure API Management-service.                                                                                                                                                                       |

> [!NOTE]
> U kunt Logboeken opgeven op verschillende niveaus: enkelvoudige API-logboek registratie of een logger voor alle Api's.
>  
> Als u beide opgeeft:
> + Als ze verschillende logboeken zijn, worden beide gebruikt (multiplex Logboeken),
> + Als ze dezelfde logboeken hebben, maar wel verschillende instellingen hebben, overschrijft het één voor één API (meer gedetailleerd niveau) de voor alle Api's.

## <a name="what-data-is-added-to-azure-application-insights"></a>Welke gegevens worden toegevoegd aan Azure-toepassing Insights

Azure-toepassing Insights ontvangt:

+ Een telemetrie-item *aanvragen* voor elke inkomende aanvraag (front-*End-aanvraag*, *frontend-antwoord*)
+ Telemetrie-item voor *afhankelijkheid* , voor elke aanvraag die wordt doorgestuurd naar een back-end-service (back-end-*aanvraag*, *back-end*)
+ Telemetrie van *uitzonde ring* voor elke mislukte aanvraag.

Een mislukte aanvraag is een aanvraag, die:

+ is mislukt vanwege een gesloten client verbinding of
+ Er is een *fout opgetreden in* de sectie van het API-beleid of
+ heeft een antwoord-HTTP-status code die overeenkomt met 4xx of 5xx.

## <a name="performance-implications-and-log-sampling"></a>Prestatie implicaties en logboek steekproef

> [!WARNING]
> Het vastleggen van alle gebeurtenissen kan ernstige gevolgen hebben voor de prestaties, afhankelijk van het aantal binnenkomende aanvragen.

Op basis van interne belasting tests heeft het inschakelen van deze functie een reductie van 40%-50% veroorzaakt wanneer de aanvraag snelheid de 1.000 aanvragen per seconde overschrijdt. Azure-toepassing Insights is ontworpen om statistische analyses te gebruiken voor het beoordelen van de prestaties van toepassingen. Het is niet bedoeld als controle systeem en is niet geschikt voor het vastleggen van elke afzonderlijke aanvraag voor high-volume-Api's.

U kunt het aantal aanvragen dat wordt geregistreerd, bewerken door de **sampling** -instelling aan te passen (Zie de bovenstaande stappen). Waarde 100% betekent dat alle aanvragen worden geregistreerd, terwijl 0% helemaal geen logboek registratie weerspiegelt. Door de **steek proef** wordt de hoeveelheid telemetrie verminderd, waardoor de prestaties aanzienlijk worden vermeden, terwijl de voor delen van logboek registratie nog steeds worden gereduceerd.

Het overs laan van de logboek registratie van headers en hoofd tekst van aanvragen en antwoorden heeft ook een positieve invloed op het oplossen van prestatie problemen.

## <a name="video"></a>Video

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>Volgende stappen

+ Meer informatie over [Azure-toepassing Insights](https://docs.microsoft.com/azure/application-insights/).
+ Overweeg [logboek registratie met Azure Event hubs](api-management-howto-log-event-hubs.md).
