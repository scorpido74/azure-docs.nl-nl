---
title: Verificatie en autorisatie
description: Conceptueel naslagwerk en overzicht van de functie Verificatie / Autorisatie voor Azure App Service, specifiek voor mobiele apps.
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: 4a9ef62178b9a58fa8703413a09114a617d1d239
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459460"
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Verificatie en autorisatie in Azure App Service voor mobiele apps

In dit artikel wordt beschreven hoe verificatie en autorisatie werken bij het ontwikkelen van native mobiele apps met een back-end van app-service. App Service biedt geïntegreerde verificatie en autorisatie, zodat uw mobiele apps gebruikers kunnen aanmelden zonder code in App Service te wijzigen. Het biedt een eenvoudige manier om uw toepassing te beschermen en te werken met gegevens per gebruiker. 

Dit artikel richt zich op de ontwikkeling van mobiele apps. Om snel aan de slag te gaan met App Service-verificatie en autorisatie voor uw mobiele app, raadpleegt u een van de volgende zelfstudies [Verificatie toevoegen aan uw iOS-app][iOS] (of [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms]of [Cordova]). 

Zie [Verificatie en autorisatie in Azure App Service](../app-service/overview-authentication-authorization.md)voor informatie over hoe verificatie en autorisatie werken in App Service.

## <a name="authentication-with-provider-sdk"></a>Verificatie met provider SDK

Nadat alles is geconfigureerd in App Service, u mobiele clients wijzigen om in te loggen met App Service. Er zijn twee benaderingen hier:

* Gebruik een SDK die een bepaalde identiteitsprovider publiceert om de identiteit vast te stellen en vervolgens toegang te krijgen tot App Service.
* Gebruik één regel code zodat de Mobile Apps-client SDK gebruikers kan aanmelden.

> [!TIP]
> De meeste toepassingen moeten een SDK van de provider gebruiken om een consistentere ervaring te krijgen wanneer gebruikers zich aanmelden, ondersteuning voor tokenvernieuwing te gebruiken en andere voordelen te krijgen die de provider aangeeft.
> 
> 

Wanneer u een SDK van de provider gebruikt, kunnen gebruikers zich aanmelden bij een ervaring die strakker integreert met het besturingssysteem waarop de app wordt uitgevoerd. Deze methode geeft u ook een providertoken en wat gebruikersinformatie over de client, waardoor het veel gemakkelijker is om grafiek-API's te gebruiken en de gebruikerservaring aan te passen. Af en toe op blogs en forums, wordt het aangeduid als de "client flow" of "client-directed flow" omdat code op de client inlogt bij gebruikers, en de clientcode toegang heeft tot een providertoken.

Nadat een providertoken is verkregen, moet het naar de app-service worden verzonden voor validatie. Nadat App Service het token heeft gevalideerd, maakt App Service een nieuw App Service-token dat wordt geretourneerd naar de client. De Mobile Apps client SDK heeft helpermethoden om deze uitwisseling te beheren en koppelt het token automatisch aan alle aanvragen aan de back-end van de toepassing. Ontwikkelaars kunnen ook een verwijzing naar het providertoken bijhouden.

Zie [Verificatiestroom app-service](../app-service/overview-authentication-authorization.md#authentication-flow)voor meer informatie over de verificatiestroom van de verificatie. 

## <a name="authentication-without-provider-sdk"></a>Verificatie zonder provider SDK

Als u geen SDK van de provider wilt instellen, u toestaan dat de functie Mobiele apps van Azure App Service voor u wordt aangemeld. De Mobile Apps client SDK opent een webweergave voor de provider van uw keuze en meldt zich aan bij de gebruiker. Af en toe op blogs en forums, het heet de "server flow" of "server-directed flow" omdat de server beheert het proces dat zich aanmeldt bij gebruikers, en de client SDK ontvangt nooit de provider token.

Code om deze stroom te starten is opgenomen in de verificatiezelfstudie voor elk platform. Aan het einde van de stroom heeft de sdk van de client een App Service-token en wordt het token automatisch gekoppeld aan alle aanvragen aan de backend van de toepassing.

Zie [Verificatiestroom app-service](../app-service/overview-authentication-authorization.md#authentication-flow)voor meer informatie over de verificatiestroom van de verificatie. 
## <a name="more-resources"></a>Meer bronnen

In de volgende zelfstudies ziet u hoe u verificatie toevoegt aan uw mobiele clients met behulp van de [servergestuurde stroom:](../app-service/overview-authentication-authorization.md#authentication-flow)

* [Verificatie toevoegen aan uw iOS-app][iOS]
* [Verificatie toevoegen aan uw Android-app][Android]
* [Verificatie toevoegen aan uw Windows-app][Windows]
* [Verificatie toevoegen aan uw Xamarin.iOS-app][Xamarin.iOS]
* [Verificatie toevoegen aan uw Xamarin.Android-app][Xamarin.Android]
* [Verificatie toevoegen aan uw App Xamarin.Forms][Xamarin.Forms]
* [Verificatie toevoegen aan uw Cordova-app][Cordova]

Gebruik de volgende bronnen als u de [clientgestuurde stroom](../app-service/overview-authentication-authorization.md#authentication-flow) voor Azure Active Directory wilt gebruiken:

* [De Active Directory-verificatiebibliotheek voor iOS gebruiken][ADAL-iOS]
* [De Active Directory-verificatiebibliotheek voor Android gebruiken][ADAL-Android]
* [De Active Directory-verificatiebibliotheek voor Windows en Xamarin gebruiken][ADAL-dotnet]

Gebruik de volgende bronnen als u de door de [client gerichte stroom](../app-service/overview-authentication-authorization.md#authentication-flow) voor Facebook wilt gebruiken:

* [De Facebook SDK voor iOS gebruiken](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Gebruik de volgende bronnen als u de door de [client gerichte stroom](../app-service/overview-authentication-authorization.md#authentication-flow) voor Twitter wilt gebruiken:

* [Twitter Fabric gebruiken voor iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Gebruik de volgende bronnen als u de door de [client gerichte stroom](../app-service/overview-authentication-authorization.md#authentication-flow) voor Google wilt gebruiken:

* [De Google Sign-In SDK voor iOS gebruiken](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service/configure-authentication-provider-aad.md
[Facebook]: ../app-service/configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: ../app-service/configure-authentication-provider-microsoft.md
[Twitter]: ../app-service/configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
