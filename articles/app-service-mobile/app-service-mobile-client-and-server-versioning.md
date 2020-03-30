---
title: SDK-versie van client en server
description: Lijst met client-SDK's en compatibiliteit met server SDK-versies voor mobiele services en Azure Mobile Apps.
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: a9ba442c00ec2498139ee34a1ff7497c98f17ede
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293486"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Client- en serverversies in mobiele apps en mobiele services

De nieuwste versie van Azure Mobile Services is de functie **Mobiele apps** van Azure App Service.

De Mobile Apps client en server SDKs zijn oorspronkelijk gebaseerd op die in Mobiele Diensten, maar ze zijn *niet* compatibel met elkaar.
Dat wil zeggen, u moet een *Mobile Apps* client SDK gebruiken met een *Mobile Apps* server SDK en op dezelfde manier voor *Mobiele Diensten.* Dit contract wordt afgedwongen door middel van een speciale header `ZUMO-API-VERSION`waarde die wordt gebruikt door de client en server SDKs, .

Opmerking: wanneer dit document verwijst naar een back-end *van Mobiele Services,* hoeft het niet per se te worden gehost op mobiele services. Het is nu mogelijk om een mobiele service te migreren om te draaien op App Service zonder wijzigingen in de code, maar de service zou nog steeds gebruik maken van *Mobile Services* SDK-versies.

## <a name="header-specification"></a>Specificatie van koptekst
De `ZUMO-API-VERSION` sleutel kan worden opgegeven in de HTTP-header of de querytekenreeks. De waarde is een versietekenreeks in het formulier **x.y.z**.

Bijvoorbeeld:

`GET https://service.azurewebsites.net/tables/TodoItem`

HEADERS: ZUMO-API-VERSIE: 2.0.0

`POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0`

## <a name="opting-out-of-version-checking"></a>Afmelden voor versiecontrole
U zich afmelden voor versiecontrole door een waarde in te stellen die **geldt** voor de app-instelling **MS_SkipVersionCheck**. Geef dit op in uw web.config of in de sectie Toepassingsinstellingen van de Azure-portal.

> [!NOTE]
> Er zijn een aantal gedragswijzigingen tussen mobiele services en mobiele apps, met name op het gebied van offline synchronisatie, verificatie en pushmeldingen. U moet zich alleen afmelden voor versiecontrole na volledige tests om ervoor te zorgen dat deze gedragswijzigingen de functionaliteit van uw app niet verbreken.

## <a name="azure-mobile-apps-client-and-server"></a><a name="2.0.0"></a>Azure Mobile Apps-client en -server
### <a name="mobile-apps-client-sdks"></a><a name="MobileAppsClients"></a>SDKs-client Van Mobiele *Apps*
Versiecontrole werd geïntroduceerd vanaf de volgende versies van de client SDK voor **Azure Mobile Apps:**

| Client platform | Versie | Waarde van versiekop |
| --- | --- | --- |
| Beheerde client (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

### <a name="mobile-apps-server-sdks"></a>SDK's voor mobiele *apps-server*
Versiecontrole is inbegrepen in de volgende SDK-versies van de server:

| Serverplatform | SDK | Geaccepteerde versiekoptekst |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Gedrag van backends voor mobiele apps
| ZUMO-API-VERSIE | Waarde van MS_SkipVersionCheck | Antwoord |
| --- | --- | --- |
| x.y.z of Null |True |200 - OK |
| Null |False/Not Specified |400 - Foute aanvraag |
| 1.x.y |False/Not Specified |400 - Foute aanvraag |
| 2.0.0-2.x.y |False/Not Specified |200 - OK |
| 3.0.0-3.x.y |False/Not Specified |400 - Foute aanvraag |

[Mobile Services clients]: #MobileServicesClients
[Mobile Apps clients]: #MobileAppsClients
[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
