---
title: Aanbevolen procedures azure-app-configuratie | Microsoft Documenten
description: Meer informatie over het optimaal gebruik van Azure App-configuratie
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: df56f53b64a35737700529b80c004efeb31eaabc
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348663"
---
# <a name="azure-app-configuration-best-practices"></a>Aanbevolen procedures voor azure-app-configuratie

In dit artikel worden veelvoorkomende patronen en aanbevolen procedures besproken wanneer u Azure App-configuratie gebruikt.

## <a name="key-groupings"></a>Belangrijkste groeperingen

App-configuratie biedt twee opties voor het ordenen van sleutels:

* Belangrijkste voorvoegsels
* Labels

U één of beide opties gebruiken om uw sleutels te groeperen.

*Belangrijke voorvoegsels* zijn de begindelen van sleutels. U logischerwijs een set sleutels groeperen met hetzelfde voorvoegsel in hun naam. Voorvoegsels kunnen meerdere componenten bevatten die zijn `/`verbonden door een scheidingsteken, zoals , vergelijkbaar met een URL-pad, om een naamruimte te vormen. Dergelijke hiërarchieën zijn handig wanneer u sleutels opslaat voor veel toepassingen, componentservices en omgevingen in één App-configuratiearchief.

Een belangrijk ding om in gedachten te houden is dat sleutels zijn wat uw toepassing code verwijzingen naar de waarden van de bijbehorende instellingen op te halen. Sleutels mogen niet veranderen, anders moet u uw code elke keer dat dat gebeurt wijzigen.

*Labels* zijn een kenmerk op toetsen. Ze worden gebruikt om varianten van een sleutel te maken. U bijvoorbeeld labels toewijzen aan meerdere versies van een sleutel. Een versie kan een iteratie, een omgeving of een andere contextuele informatie zijn. Uw toepassing kan een geheel andere set sleutelwaarden aanvragen door een ander label op te geven. Als gevolg hiervan blijven alle belangrijke referenties ongewijzigd in uw code.

## <a name="key-value-compositions"></a>Sleutelsamenstellingen

App-configuratie behandelt alle sleutels die ermee zijn opgeslagen als onafhankelijke entiteiten. App-configuratie probeert geen relatie tussen sleutels af te leiden of belangrijke waarden te erven op basis van hun hiërarchie. U echter meerdere sets sleutels samenvoegen door labels te gebruiken in combinatie met de juiste configuratiestapeling in uw toepassingscode.

Laten we eens naar een voorbeeld kijken. Stel dat u een instelling met de naam **Asset1**hebt , waarvan de waarde kan variëren op basis van de ontwikkelomgeving. U maakt een sleutel met de naam "Asset1" met een leeg label en een label met de naam "Ontwikkeling". In het eerste label plaatst u de standaardwaarde voor **Asset1**en plaatst u een specifieke waarde voor 'Ontwikkeling' in de laatste.

In uw code haalt u eerst de belangrijkste waarden op zonder labels en haalt u vervolgens een tweede keer dezelfde set belangrijke waarden op met het label 'Ontwikkeling'. Wanneer u de waarden de tweede keer ophaalt, worden de vorige waarden van de sleutels overschreven. Met het .NET Core-configuratiesysteem u meerdere sets configuratiegegevens op elkaar stapelen. Als een sleutel in meer dan één set bestaat, wordt de laatste set met deze sleutel gebruikt. Met een modern programmeerframework, zoals .NET Core, krijgt u deze stapelmogelijkheid gratis als u een native configuratieprovider gebruikt om toegang te krijgen tot app-configuratie. In het volgende codefragment ziet u hoe u stapelen in een .NET Core-toepassing implementeren:

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

[Gebruik labels om verschillende configuraties voor verschillende omgevingen in te schakelen,](./howto-labels-aspnet-core.md) biedt een compleet voorbeeld.

## <a name="app-configuration-bootstrap"></a>Starttrap van app-configuratie

Als u toegang wilt krijgen tot een App Configuration Store, u de verbindingstekenreeks gebruiken, die beschikbaar is in de Azure-portal. Omdat verbindingstekenreeksen referentie-informatie bevatten, worden ze beschouwd als geheimen. Deze geheimen moeten worden opgeslagen in Azure Key Vault en uw code moet worden geverifieerd naar Key Vault om ze op te halen.

Een betere optie is het gebruik van de functie beheerde identiteiten in Azure Active Directory. Met beheerde identiteiten hebt u alleen de URL van het eindpunt app-configuratie nodig om de toegang tot uw app-configuratiearchief op te start zetten. U de URL insluiten in uw toepassingscode (bijvoorbeeld in het *bestand appsettings.json).* Zie [Integreren met door Azure beheerde identiteiten](howto-integrate-azure-managed-service-identity.md) voor meer informatie.

## <a name="app-or-function-access-to-app-configuration"></a>Toegang tot app of functie tot app-configuratie

U toegang bieden tot app-configuratie voor web-apps of -functies met behulp van een van de volgende methoden:

* Voer via de Azure-portal de verbindingstekenreeks in voor uw App-configuratiearchief in de toepassingsinstellingen van App-service.
* Sla de verbindingstekenreeks op in uw App Configuration Store in Key Vault en [verwijs ernaar vanuit App Service.](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references)
* Gebruik door Azure beheerde identiteiten om toegang te krijgen tot het App Configuration Store. Zie [Integreren met door Azure beheerde identiteiten voor](howto-integrate-azure-managed-service-identity.md)meer informatie.
* Pushconfiguratie van app-configuratie naar appservice. App-configuratie biedt een exportfunctie (in Azure-portal en de Azure CLI) die gegevens rechtstreeks naar App Service verzendt. Met deze methode hoeft u de toepassingscode helemaal niet te wijzigen.

## <a name="reduce-requests-made-to-app-configuration"></a>Aanvragen voor app-configuratie verminderen

Overmatige verzoeken aan app-configuratie kunnen leiden tot beperking of overschrijding van kosten. Ga als reactie op het aantal aanvragen:

* Verhoog de time-out voor vernieuwen, vooral als uw configuratiewaarden niet vaak worden gewijzigd. Geef een nieuwe vernieuwingstime-out op met behulp van de [ `SetCacheExpiration` methode](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.azureappconfigurationrefreshoptions.setcacheexpiration).

* Bekijk een enkele *sentinel-sleutel*in plaats van naar afzonderlijke sleutels te kijken. Vernieuw alle configuratie alleen als de sentinel-sleutel verandert. Zie [Dynamische configuratie gebruiken in een ASP.NET Core-app](enable-dynamic-configuration-aspnet-core.md) voor een voorbeeld.

* Gebruik Azure Event Grid om meldingen te ontvangen wanneer de configuratie verandert, in plaats van voortdurend te peilen voor wijzigingen. Zie [Configuratiegebeurtenissen voor Azure-app-configuratie routeren naar een webeindpunt](./howto-app-configuration-event.md) voor meer informatie

## <a name="importing-configuration-data-into-app-configuration"></a>Configuratiegegevens importeren in app-configuratie

App-configuratie biedt de optie om uw configuratie-instellingen bulk te [importeren](https://aka.ms/azconfig-importexport1) uit uw huidige configuratiebestanden met behulp van de Azure-portal of CLI. U ook dezelfde opties gebruiken om waarden uit app-configuratie te exporteren, bijvoorbeeld tussen gerelateerde winkels. Als u een doorlopende synchronisatie met uw GitHub-repo wilt instellen, u onze [GitHub-actie](https://aka.ms/azconfig-gha2) gebruiken, zodat u uw bestaande bronbeheerpraktijken blijven gebruiken terwijl u de voordelen van app-configuratie krijgt.

## <a name="next-steps"></a>Volgende stappen

* [Toetsen en waarden](./concept-key-value.md)
