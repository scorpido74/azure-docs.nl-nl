---
title: API-verificatie en -autorisatie - Azure Time Series Insights | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u verificatie en autorisatie configureert voor een aangepaste toepassing die de Azure Time Series Insights API aanroept.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: ff5f7a80e2dcedb1795bae14ee9140c2842303a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76984540"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Verificatie en autorisatie voor Azure Time Series Insights-API

In dit document wordt beschreven hoe u een app registreert in Azure Active Directory met het nieuwe Azure Active Directory-blad. Met apps die zijn geregistreerd in Azure Active Directory, kunnen gebruikers de Azure Time Series Insight API die is gekoppeld aan een Time Series Insights-omgeving verifiëren en mogen gebruiken.

> [!IMPORTANT]
> Azure Time Series Insights ondersteunt de volgende verificatiebibliotheken:
> * De meer recente [Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * De [Azure Active Directory Authentication Library (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="service-principal"></a>Service-principal

In de volgende secties wordt beschreven hoe u een toepassing configureert om toegang te krijgen tot de Time Series Insights API namens een app. De toepassing kan vervolgens referentiegegevens in de Time Series Insights-omgeving opvragen of publiceren met behulp van zijn eigen toepassingsreferenties via Azure Active Directory.

## <a name="summary-and-best-practices"></a>Samenvatting en best practices

De registratiestroom van Azure Active Directory-apps omvat drie belangrijke stappen.

1. [Een toepassing registreren](#azure-active-directory-app-registration) in Azure Active Directory.
1. Geef de toepassing toestemming om toegang te [hebben tot de Time Series Insights-omgeving.](#granting-data-access)
1. Gebruik de **toepassings-id** en **clientgeheim** om een token te verkrijgen in `https://api.timeseries.azure.com/` uw [client-app.](#client-app-initialization) Het token kan vervolgens worden gebruikt om de Time Series Insights API aan te roepen.

Per **stap 3**u met het scheiden van uw toepassing en uw gebruikersreferenties:

* Wijs machtigingen toe aan de app-identiteit die losstaan van uw eigen machtigingen. Deze machtigingen zijn doorgaans beperkt tot wat de app vereist. U de app bijvoorbeeld toestaan om alleen gegevens uit een bepaalde Time Series Insights-omgeving te lezen.
* Isoleer de beveiliging van de app van de verificatiereferenties van de gebruiker met behulp van een **clientgeheim** of beveiligingscertificaat. Als gevolg hiervan zijn de referenties van de toepassing niet afhankelijk van de referenties van een specifieke gebruiker. Als de rol van de gebruiker verandert, vereist de toepassing niet noodzakelijkerwijs nieuwe referenties of verdere configuratie. Als de gebruiker zijn wachtwoord wijzigt, is voor alle toegang tot de toepassing geen nieuwe referenties of sleutels vereist.
* Voer een onbeheerd script uit met behulp **van een clientgeheim** of beveiligingscertificaat in plaats van de referenties van een specifieke gebruiker (waarbij ze aanwezig moeten zijn).
* Gebruik een beveiligingscertificaat in plaats van een wachtwoord om de toegang tot uw Azure Time Series Insights API te beveiligen.

> [!IMPORTANT]
> Volg het principe van **scheiding van zorgen** (hierboven beschreven in dit scenario) bij het configureren van uw Azure Time Series Insights-beveiligingsbeleid.

> [!NOTE]
> * Het artikel richt zich op een toepassing met één tenant waarbij de toepassing is bedoeld om in slechts één organisatie uit te voeren.
> * U gebruikt meestal toepassingen met één tenant voor line-of-business toepassingen die in uw organisatie worden uitgevoerd.

## <a name="detailed-setup"></a>Gedetailleerde installatie

### <a name="azure-active-directory-app-registration"></a>Azure Active Directory-app-registratie

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>Toegang tot gegevens verlenen

1. Selecteer voor de omgeving Time Series Insights **het beleid voor gegevenstoegangs** en selecteer **Toevoegen**.

   [![Nieuw beleid voor gegevenstoegang toevoegen aan de Time Series Insights-omgeving](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. Plak **in** het dialoogvenster Gebruiker selecteren de **toepassingsnaam** of de **toepassings-id** in de sectie Azure Active Directory-app.

   [![Een toepassing zoeken in het dialoogvenster Gebruiker selecteren](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Selecteer de rol. Selecteer **Reader** to query data or **Contributor** to query data and change reference data. Selecteer **OK**.

   [![Kies Lezer of bijdrager in het dialoogvenster Gebruikersrol selecteren](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Sla het beleid op door **OK**te selecteren .

   > [!TIP]
   > Voor geavanceerde opties voor gegevenstoegang leest u [het verlenen van gegevenstoegang](./time-series-insights-data-access.md).

### <a name="client-app-initialization"></a>Initialisatie van client-apps

* Ontwikkelaars kunnen de [Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) of Azure Active Directory Authentication Library [(ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) gebruiken om te verifiëren met Azure Time Series Insights.

* Bijvoorbeeld om te verifiëren met ADAL:

   1. Gebruik de **toepassings-id** en **clientgeheim** (toepassingssleutel) in de sectie Azure Active Directory-app-registratie om het token namens de toepassing te verkrijgen.

   1. In C#kan de volgende code het token namens de toepassing verkrijgen. Lees querygegevens voor een volledig voorbeeld [met C#](time-series-insights-query-data-csharp.md).

        [!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs?range=170-199)]

   1. Het token kan vervolgens `Authorization` worden doorgegeven in de koptekst wanneer de toepassing de Time Series Insights API aanroept.

* Als alternatief kunnen ontwikkelaars ervoor kiezen om zich te verifiëren met BEHULP VAN MSAL. Lees meer over [migreren naar MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration) en bekijk onze [Manage GA-referentiegegevens voor een Azure Time Series Insights-omgeving met behulp van C#-artikel](time-series-insights-manage-reference-data-csharp.md) voor meer informatie. 

## <a name="common-headers-and-parameters"></a>Algemene kopteksten en parameters

In deze sectie worden veelvoorkomende HTTP-aanvraagkoppen en -parameters beschreven die worden gebruikt om query's te maken tegen de GA-inzichten en preview-API's van de tijdreeks. API-specifieke vereisten komen meer in detail aan bod in de [Time Series Insights REST API-referentiedocumentatie.](https://docs.microsoft.com/rest/api/time-series-insights/)

> [!TIP]
> Lees de [Azure REST API-referentie](https://docs.microsoft.com/rest/api/azure/) voor meer informatie over het gebruik van REST-API's, het indienen van HTTP-verzoeken en het verwerken van HTTP-antwoorden.

### <a name="authentication"></a>Authentication

Als u geverifieerde query's wilt uitvoeren tegen de [TIME Series Insights REST API's,](https://docs.microsoft.com/rest/api/time-series-insights/)moet een geldig OAuth 2.0-token aan toonder worden doorgegeven in de header [Autorisatie](/rest/api/apimanagement/2019-01-01/authorizationserver/createorupdate) met behulp van een REST-client naar keuze (Postbode, JavaScript, C#). 

> [!TIP]
> Lees de gehoste [SDK-voorbeeldvisualisatie van](https://tsiclientsample.azurewebsites.net/) Azure Time Series Insights-client om te leren hoe u deze verifiëren met de Time Series Insights-API's die programmatisch worden gebruikt met behulp van de [JavaScript Client SDK,](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) samen met grafieken en grafieken.

### <a name="http-headers"></a>HTTP-kopteksten

Vereiste aanvraagheaders worden hieronder beschreven.

| Vereiste aanvraagkoptekst | Beschrijving |
| --- | --- |
| Autorisatie | Als u wilt verifiëren met Time Series Insights, moet een geldig OAuth 2.0-token aan toonder worden doorgegeven in de **kopautorisatie.** | 

> [!IMPORTANT]
> Het token moet precies `https://api.timeseries.azure.com/` worden uitgegeven aan de resource (ook wel bekend als de "doelgroep" van het token).
> * Uw [Postman](https://www.getpostman.com/) **AuthURL** zal dus:`https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`
> * `https://api.timeseries.azure.com/`is geldig, maar `https://api.timeseries.azure.com` is dat niet.

Optionele aanvraagkoppen worden hieronder beschreven.

| Optionele aanvraagheader | Beschrijving |
| --- | --- |
| Inhoudstype | wordt `application/json` alleen ondersteund. |
| x-ms-client-request-id | Een klantaanvraag-id. De service registreert deze waarde. Hiermee kan de service de werking van alle services traceren. |
| x-ms-client-session-id | Een clientsessie-ID. De service registreert deze waarde. Hiermee kan de service een groep gerelateerde bewerkingen traceren tussen services. |
| x-ms-client-applicatie-naam | Naam van de toepassing die dit verzoek heeft gegenereerd. De service registreert deze waarde. |

Optionele maar aanbevolen antwoordkoppen worden hieronder beschreven.

| Reactiekop | Beschrijving |
| --- | --- |
| Inhoudstype | Alleen `application/json` wordt ondersteund. |
| x-ms-request-id | Server-gegenereerde aanvraag-id. Kan worden gebruikt om contact op te nemen met Microsoft om een verzoek te onderzoeken. |
| x-ms-property-not-found-behavior | GA API optionele antwoordkop. Mogelijke waarden `ThrowError` zijn (standaard) of `UseNull`. |

### <a name="http-parameters"></a>HTTP-parameters

> [!TIP]
> Meer informatie over vereiste en optionele querygegevens vindt u in de [referentiedocumentatie.](https://docs.microsoft.com/rest/api/time-series-insights/)

Vereiste URL-querytekenreeksparameters zijn afhankelijk van de API-versie.

| Release | Mogelijke API-versiewaarden |
| --- |  --- |
| Algemene beschikbaarheid | `api-version=2016-12-12`|
| Preview | `api-version=2018-11-01-preview` |
| Preview | `api-version=2018-08-15-preview` |

Optionele URL-querytekenreeksparameters omvatten het instellen van een time-out voor HTTP-aanvraaguitvoeringstijden.

| Optionele queryparameter | Beschrijving | Versie |
| --- |  --- | --- |
| `timeout=<timeout>` | Time-out aan de serverzijde voor http-aanvraaguitvoering. Alleen van toepassing op de [API's voor omgevingsgebeurtenissen](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) en [informatie-informatie over omgevingsaggregaten.](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api) De time-outwaarde moet bijvoorbeeld `"PT20S"` in de duurnotatie VAN ISO `1-30 s`8601 staan en moet zich in het bereik bevinden . Standaardwaarde `30 s`is . | Algemene beschikbaarheid |
| `storeType=<storeType>` | Voor Preview-omgevingen waarvoor warme opslag is ingeschakeld, `WarmStore` kan `ColdStore`de query worden uitgevoerd op de of . Deze parameter in de query definieert op welke opslag de query moet worden uitgevoerd. Als deze niet is gedefinieerd, wordt de query uitgevoerd in het koelhuis. Als u de warme winkel wilt opvragen, moet **storeType** worden ingesteld op `WarmStore`. Als deze niet is gedefinieerd, wordt de query uitgevoerd tegen het koelhuis. | Preview |

## <a name="next-steps"></a>Volgende stappen

- Lees [Querygegevens met C#](./time-series-insights-query-data-csharp.md)voor voorbeeldcode die de GA Time Series Insights API aanroept.

- Lees [queryvoorbeeldgegevens voor QueryPreview-gegevens met C#](./time-series-insights-update-query-data-csharp.md)voor voorbeeld van API-codevoorbeelden voor timeseries insights.

- Lees voor API-referentiegegevens de [query-API-referentiedocumentatie.](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)

- Meer informatie over het [maken van een serviceprincipal](../active-directory/develop/howto-create-service-principal-portal.md).
