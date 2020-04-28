---
title: OpenAPI-metagegevens in Azure-functies
description: Overzicht van OpenAPI-ondersteuning in Azure-functies
author: alexkarcher-msft
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alkarche
ms.openlocfilehash: cbfd0e36307210851070c22e74acb0a858446ce1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866720"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Ondersteuning voor OpenAPI 2.0-metagegevens in Azure-functies (voorbeeld)
Ondersteuning voor openAPI 2.0 (voorheen Swagger) in Azure-functies is een voorbeeldfunctie die u gebruiken om een OpenAPI 2.0-definitie in een functie-app te schrijven. U dat bestand vervolgens hosten met behulp van de functie-app.

> [!IMPORTANT]
> De OpenAPI-preview-functie is alleen vandaag beschikbaar in de runtime 1.x. Meer informatie over het maken van een 1.x-functie-app [vindt u hier](./functions-versions.md#creating-1x-apps).

[OpenAPI-metagegevens](https://swagger.io/) maken het mogelijk dat een functie die een REST API host, wordt verbruikt door een breed scala aan andere software. Deze software bevat Microsoft-aanbiedingen zoals PowerApps en de [API-apps-functie van Azure App Service,](../app-service/overview.md)externe hulpprogramma's voor ontwikkelaars zoals [Postman](https://www.getpostman.com/docs/importing_swagger)en [nog veel meer pakketten.](https://swagger.io/tools/)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>We raden u aan om te beginnen met de [zelfstudie aan](./functions-api-definition-getting-started.md) de slag en vervolgens terug te keren naar dit document voor meer informatie over specifieke functies.

## <a name="enable-openapi-definition-support"></a><a name="enable"></a>Ondersteuning voor OpenAPI-definitie inschakelen
U alle OpenAPI-instellingen configureren op de **API-definitiepagina** in de **platformfuncties**van uw functie-app.

> [!NOTE]
> Functie-API-definitiefunctie wordt momenteel niet ondersteund voor bètarun.

Als u het genereren van een gehoste OpenAPI-definitie en een quickstartdefinitie wilt inschakelen, stelt u **API-definitiebron** in op **Functie (Voorbeeld).** **Met externe URL** kan uw functie een OpenAPI-definitie gebruiken die elders wordt gehost.

## <a name="generate-a-swagger-skeleton-from-your-functions-metadata"></a><a name="generate-definition"></a>Genereer een Swagger-skelet uit de metadata van je functie
Een sjabloon kan u helpen bij het schrijven van uw eerste OpenAPI-definitie. De functie definitiesjabloon maakt een schaarse OpenAPI-definitie door alle metagegevens in het function.json-bestand te gebruiken voor elk van uw HTTP-triggerfuncties. U moet meer informatie over uw API invullen via de [OpenAPI-specificatie,](https://swagger.io/specification/)zoals aanvraag- en antwoordsjablonen.

Zie de [zelfstudie aan](./functions-api-definition-getting-started.md)de slag voor stapsgewijze instructies.

### <a name="available-templates"></a><a name="templates"></a>Beschikbare sjablonen

|Naam| Beschrijving |
|:-----|:-----|
|Gegenereerde definitie|Een OpenAPI-definitie met de maximale hoeveelheid informatie die kan worden afgeleid uit de bestaande metagegevens van de functie.|

### <a name="included-metadata-in-the-generated-definition"></a><a name="quickstart-details"></a>Opgenomen metagegevens in de gegenereerde definitie

De volgende tabel vertegenwoordigt de Azure-portalinstellingen en de bijbehorende gegevens in function.json terwijl deze is toegewezen aan het gegenereerde Swagger-skelet.

|Swagger.json|Portal-gebruikersinterface|Function.json|
|:----|:-----|:-----|
|[Host](https://swagger.io/specification/#fixed-fields-15)|**Url van app-instellingen** > **functie-app-instellingen** > **Overzicht** > **URL**|*Niet aanwezig*
|[Paden](https://swagger.io/specification/#paths-object-29)|**Geselecteerde** > **HTTP-methoden integreren**|Bindingen: Route
|[Paditem](https://swagger.io/specification/#path-item-object-32)|**Routesjabloon integreren** > **Route template**|Bindingen: Methoden
|[Beveiliging](https://swagger.io/specification/#security-scheme-object-112)|**Sleutels**|*Niet aanwezig*|
|operationID*|**Route + Toegestane werkwoorden**|Route + Toegestane werkwoorden|

\*De bedrijfs-ID is alleen vereist voor de integratie met PowerApps en Flow.
> [!NOTE]
> De x-ms-samenvattingsextensie biedt een weergavenaam in Logic Apps, PowerApps en Flow.
>
> Zie Uw [Swagger-definitie aanpassen voor PowerApps voor](https://docs.microsoft.com/connectors/custom-connectors/openapi-extensions)meer informatie.

## <a name="use-cicd-to-set-an-api-definition"></a><a name="CICD"></a>CI/CD gebruiken om een API-definitie in te stellen

 U moet API-definitiehosting in de portal inschakelen voordat u bronbeheer inschakelt om uw API-definitie te wijzigen vanuit bronbeheer. Volg de volgende instructies:

1. Blader naar **API Definition (preview)** in de instellingen van de functie-app.
   1. **API-definitiebron** instellen op **functie**.
   1. Klik **op API-definitiesjabloon genereren** en vervolgens **opslaan** om een sjabloondefinitie te maken om later te wijzigen.
   1. Let op uw API-definitie URL en sleutel.
1. [Continue integratie/continue implementatie instellen (CI/CD).](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#requirements-for-continuous-deployment)
2. Wijzig swagger.json in bronbeheer op\.\site\wwwroot azurefunctions\swagger\swagger.json.

Nu worden wijzigingen in swagger.json in uw repository gehost door uw functie-app op de API-definitie-URL en de sleutel die u in stap 1.c. hebt opgemerkt.

## <a name="next-steps"></a>Volgende stappen
* [Aan de slag tutorial](functions-api-definition-getting-started.md). Probeer onze walkthrough om een OpenAPI-definitie in actie te zien.
* [GitHub-repository voor Azure-functies](https://github.com/Azure/Azure-Functions/). Bekijk de functions repository om ons feedback te geven over de API definition support preview. Maak een GitHub-probleem voor alles wat u wilt zien bijgewerkt.
* [Verwijzing naar azure-functies-ontwikkelaars](functions-reference.md). Meer informatie over codeerfuncties en het definiëren van triggers en bindingen.
