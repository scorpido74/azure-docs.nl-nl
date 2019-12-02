---
title: Versie beheer voor client en Server-SDK
description: Lijst met client-Sdk's en-compatibiliteit met Server SDK-versies voor Mobile Services en Azure Mobile Apps.
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: 4d0b301dee363c2338cb13a9fc09ee17549467eb
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668845"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Client-en Server versie beheer in Mobile Apps en Mobile Services
> [!NOTE]
> Visual Studio App Center ondersteunt end-to-end-services en geïntegreerde services die een centrale rol spelen bij het ontwikkelen van mobiele apps. Ontwikkelaars kunnen services **bouwen**, **testen** en **distribueren** om een CI/CD-pijplijn (continue integratie en continue levering) in te stellen. Zodra de app is geïmplementeerd, kunnen ontwikkelaars de status en het gebruik van hun app controleren met behulp van de **analyseservice** en de **diagnoseservice** en communiceren met gebruikers met behulp van de **pushservice**. Ontwikkelaars kunnen ook gebruikmaken van **Auth** voor het verifiëren van gebruikers en van **Data** Service voor het persistent maken en synchroniseren van app-gegevens in de cloud.
>
> Als u cloudservices wilt integreren in uw mobiele toepassing, meldt u zich aan bij [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc).

De nieuwste versie van Azure Mobile Services is de **Mobile apps** -functie van Azure app service.

De Mobile Apps-client-en server-Sdk's zijn oorspronkelijk gebaseerd op die in Mobile Services, maar ze zijn *niet* compatibel met elkaar.
Dat wil zeggen dat u een *Mobile apps* client-SDK met een *Mobile apps* Server-SDK moet gebruiken en ook voor *Mobile Services*. Dit contract wordt afgedwongen via een speciale header-waarde die wordt gebruikt door de Sdk's van de client en de server, `ZUMO-API-VERSION`.

Opmerking: wanneer dit document naar een *Mobile Services* back-end verwijst, hoeft het niet per se te worden gehost op Mobile Services. Het is nu mogelijk om een mobiele service te migreren om te worden uitgevoerd op App Service zonder code wijzigingen, maar de service zal nog steeds gebruikmaken van *Mobile Services* SDK-versies.

## <a name="header-specification"></a>Header specificatie
De sleutel `ZUMO-API-VERSION` kan worden opgegeven in de HTTP-header of in de query reeks. De waarde is een versie teken reeks in de vorm **x. y. z**.

Bijvoorbeeld:

https://service.azurewebsites.net/tables/TodoItem ophalen

HEADERS: ZUMO-API-VERSION: 2.0.0

POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Controle van versie uitschakelen
U kunt de versie controle afmelden door de waarde **True** in te stellen voor de app-instelling **MS_SkipVersionCheck**. Geef dit op in het web. config-bestand of in de sectie toepassings instellingen van de Azure Portal.

> [!NOTE]
> Er zijn een aantal gedrags wijzigingen tussen Mobile Services en Mobile Apps, met name op het gebied van offline synchronisatie, verificatie en push meldingen. U moet de controle van de versie alleen afmelden na het volt ooien van testen om ervoor te zorgen dat deze gedrags wijzigingen de functionaliteit van uw app niet onderbreken.

## <a name="2.0.0"></a>Azure Mobile Apps-client en-server
### <a name="MobileAppsClients"></a>Client-Sdk's voor mobiele *apps*
De versie controle werd geïntroduceerd vanaf de volgende versies van de client SDK voor **Azure Mobile apps**:

| Client platform | Version | Waarde van versie-header |
| --- | --- | --- |
| Beheerde client (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

### <a name="mobile-apps-server-sdks"></a>Sdk's van Mobile *apps* server
Versie controle is opgenomen in de volgende server SDK-versies:

| Server platform | SDK | Geaccepteerde versie header |
| --- | --- | --- |
| .NET |[Micro soft. Azure. Mobile. server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[Azure-Mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Gedrag van Mobile Apps back-ends
| ZUMO-API-VERSIE | Waarde van MS_SkipVersionCheck | Antwoord |
| --- | --- | --- |
| x. y. z of Null |Waar |200-OK |
| Null |ONWAAR/niet opgegeven |400 - Foute aanvraag |
| 1. x. y |ONWAAR/niet opgegeven |400 - Foute aanvraag |
| 2.0.0-2. x. y |ONWAAR/niet opgegeven |200-OK |
| 3.0.0-3. x. y |ONWAAR/niet opgegeven |400 - Foute aanvraag |

[Mobile Services clients]: #MobileServicesClients
[Mobile Apps clients]: #MobileAppsClients
[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
