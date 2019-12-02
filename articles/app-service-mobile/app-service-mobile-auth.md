---
title: Verificatie en autorisatie
description: Conceptuele Naslag informatie en overzicht van de functie voor verificatie/autorisatie voor Azure App Service, met name voor mobiele apps.
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: 2c3bc1f9563a5657d7574020fc908f23a711391c
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668517"
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Verificatie en autorisatie in Azure App Service voor mobiele apps

> [!NOTE]
> Visual Studio App Center ondersteunt end-to-end-services en geïntegreerde services die een centrale rol spelen bij het ontwikkelen van mobiele apps. Ontwikkelaars kunnen services **bouwen**, **testen** en **distribueren** om een CI/CD-pijplijn (continue integratie en continue levering) in te stellen. Zodra de app is geïmplementeerd, kunnen ontwikkelaars de status en het gebruik van hun app controleren met behulp van de **analyseservice** en de **diagnoseservice** en communiceren met gebruikers met behulp van de **pushservice**. Ontwikkelaars kunnen ook gebruikmaken van **Auth** voor het verifiëren van gebruikers en van **Data** Service voor het persistent maken en synchroniseren van app-gegevens in de cloud.
>
> Als u cloudservices wilt integreren in uw mobiele toepassing, meldt u zich aan bij [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc).

In dit artikel wordt beschreven hoe verificatie en autorisatie werkt bij het ontwikkelen van systeem eigen mobiele apps met een App Service back-end. App Service biedt geïntegreerde verificatie en autorisatie, zodat uw mobiele apps gebruikers kunnen ondertekenen zonder code in App Service te wijzigen. Het biedt een eenvoudige manier om uw toepassing te beveiligen en te werken met gegevens per gebruiker. 

Dit artikel is gericht op de ontwikkeling van mobiele apps. Als u snel aan de slag wilt gaan met App Service verificatie en autorisatie voor uw mobiele app, raadpleegt u een van de volgende zelf studies [verificatie toevoegen aan uw IOS-app][iOS] (of [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms]of [Cordova]). 

Zie [verificatie en autorisatie in azure app service](../app-service/overview-authentication-authorization.md)voor meer informatie over de werking van verificatie en autorisatie in app service.

## <a name="authentication-with-provider-sdk"></a>Verificatie met provider-SDK

Nadat alles in App Service is geconfigureerd, kunt u mobiele clients wijzigen om zich aan te melden met App Service. Er zijn twee benaderingen:

* Gebruik een SDK die een bepaalde id-provider publiceert om identiteit in te richten en vervolgens toegang te krijgen tot App Service.
* Gebruik één regel code zodat de Mobile Apps client-SDK gebruikers kan aanmelden.

> [!TIP]
> De meeste toepassingen moeten een provider-SDK gebruiken om een consistenter ervaring te krijgen wanneer gebruikers zich aanmelden, ondersteuning voor het vernieuwen van tokens gebruiken en andere voor delen ontvangen die de provider heeft opgegeven.
> 
> 

Wanneer u een provider-SDK gebruikt, kunnen gebruikers zich aanmelden bij een ervaring die nauw keuriger kan worden geïntegreerd met het besturings systeem waarop de app wordt uitgevoerd. Deze methode geeft u ook een provider token en bepaalde gebruikers gegevens op de client, waardoor het veel eenvoudiger is om grafiek-Api's te gebruiken en de gebruikers ervaring aan te passen. Af en toe op blogs en forums wordt de ' client-stroom ' of ' client-gerichte stroom ' genoemd, omdat code op de client zich aanmeldt bij gebruikers, en de client code toegang heeft tot een provider token.

Nadat een provider token is verkregen, moet het worden verzonden naar App Service voor validatie. Nadat App Service het token hebt gevalideerd, maakt App Service een nieuw App Service token dat wordt geretourneerd naar de client. De Mobile Apps client-SDK bevat hulp methoden voor het beheren van deze uitwisseling en het token automatisch aan alle aanvragen voor de back-end van de toepassing te koppelen. Ontwikkel aars kunnen ook een verwijzing naar het provider token opslaan.

Zie [app service-verificatie stroom](../app-service/overview-authentication-authorization.md#authentication-flow)voor meer informatie over de verificatie stroom. 

## <a name="authentication-without-provider-sdk"></a>Verificatie zonder provider-SDK

Als u geen provider-SDK wilt instellen, kunt u de Mobile Apps-functie van Azure App Service toestaan om u aan te melden. De Mobile Apps client-SDK opent een webweergave voor de provider van de gebruiker die u kiest en zich aanmeldt. In afwille van blogs en forums wordt het de ' server stroom ' of ' server-gerichte stroom ' genoemd, omdat de server het proces beheert dat zich aanmeldt bij de gebruikers en de client-SDK nooit het provider token ontvangt.

De code voor het starten van deze stroom is opgenomen in de verificatie-zelf studie voor elk platform. Aan het einde van de stroom heeft de client-SDK een App Service token en wordt het token automatisch gekoppeld aan alle aanvragen voor de back-end van de toepassing.

Zie [app service-verificatie stroom](../app-service/overview-authentication-authorization.md#authentication-flow)voor meer informatie over de verificatie stroom. 
## <a name="more-resources"></a>Meer informatiebronnen

In de volgende zelf studies ziet u hoe u verificatie kunt toevoegen aan uw mobiele clients met behulp van de [Server-gerichte stroom](../app-service/overview-authentication-authorization.md#authentication-flow):

* [Verificatie toevoegen aan uw iOS-app][iOS]
* [Verificatie toevoegen aan uw Android-app][Android]
* [Verificatie toevoegen aan uw Windows-app][Windows]
* [Verificatie toevoegen aan uw Xamarin. iOS-app][Xamarin.iOS]
* [Verificatie toevoegen aan uw Xamarin. Android-app][Xamarin.Android]
* [Verificatie toevoegen aan uw Xamarin. Forms-app][Xamarin.Forms]
* [Verificatie toevoegen aan uw Cordova-app][Cordova]

Gebruik de volgende bronnen als u de op de [client gerichte stroom](../app-service/overview-authentication-authorization.md#authentication-flow) voor Azure Active Directory wilt gebruiken:

* [De Active Directory Authentication Library voor iOS gebruiken][ADAL-iOS]
* [De Active Directory Authentication Library voor Android gebruiken][ADAL-Android]
* [De Active Directory Authentication Library voor Windows en Xamarin gebruiken][ADAL-dotnet]

Gebruik de volgende resources als u de op de [client gerichte stroom](../app-service/overview-authentication-authorization.md#authentication-flow) voor Facebook wilt gebruiken:

* [De Facebook-SDK voor iOS gebruiken](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Gebruik de volgende resources als u de op de [client gerichte stroom](../app-service/overview-authentication-authorization.md#authentication-flow) voor Twitter wilt gebruiken:

* [Twitter-infra structuur voor iOS gebruiken](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Gebruik de volgende bronnen als u de op de [client gerichte stroom](../app-service/overview-authentication-authorization.md#authentication-flow) voor Google wilt gebruiken:

* [De Google-aanmeldings-SDK voor iOS gebruiken](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

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
