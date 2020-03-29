---
title: Volgende stappen voor het maken van servicefabric-projecten
description: Meer informatie over het toepassingsproject dat u zojuist hebt gemaakt in Visual Studio.  Meer informatie over het bouwen van services met behulp van zelfstudies en meer informatie over het ontwikkelen van services voor Service Fabric.
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: e8fc105657bda6114851f4819be4658926ad621c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349401"
---
# <a name="your-service-fabric-application-and-next-steps"></a>Uw Service Fabric-toepassing en volgende stappen
Uw Azure Service Fabric-toepassing is gemaakt. In dit artikel worden enkele zelfstudies beschreven om uit te proberen, de make-up van uw project, wat meer informatie waarin u mogelijk geïnteresseerd bent en mogelijke volgende stappen.

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Aan de slag met tutorials, walk-throughs en voorbeelden
Klaar om te beginnen?  

Werk door de tutorial van de .NET-toepassing. Meer informatie over het [bouwen van een app](service-fabric-tutorial-create-dotnet-app.md) met een ASP.NET Core front-end en een stateful back-end, implementeren van de [toepassing](service-fabric-tutorial-deploy-app-to-party-cluster.md) op een cluster, configureren van CI [/ CD,](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)en [het opzetten van monitoring en diagnostiek](service-fabric-tutorial-monitoring-aspnet.md).

Of probeer een van de volgende walk-throughs en maak je eerste...
- [C# Betrouwbare services service op Windows](service-fabric-reliable-services-quick-start.md) 
- [C# Betrouwbare actoren service op Windows](service-fabric-reliable-actors-get-started.md) 
- [Uitvoerbare service voor gasten op Windows](quickstart-guest-app.md) 
- [Windows-containertoepassing](service-fabric-get-started-containers.md) 

Misschien bent u ook geïnteresseerd in het uitproberen van onze [voorbeeldtoepassingen.](https://aka.ms/servicefabricsamples)

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>Vragen of feedback?  Wilt u een probleem melden?
Lees [veelgestelde vragen](service-fabric-common-questions.md) door en vind antwoorden over wat Service Fabric kan doen en hoe het moet worden gebruikt.

[Ondersteuningsopties](service-fabric-support.md) bevatten forums op StackOverflow en MSDN voor het stellen van vragen, evenals opties voor het melden van problemen, het krijgen van ondersteuning en het indienen van productfeedback.

## <a name="the-application-project"></a>Het toepassingsproject
Elke nieuwe toepassing bevat een toepassingsproject. Er kunnen een of twee extra projecten zijn, afhankelijk van het gekozen type service.

Het aanvraagproject bestaat uit:

* Een reeks verwijzingen naar de services die deel uitmaken van uw toepassing.
* Drie publiceren profielen (1-Knooppunt Lokaal, lokaal met 5 knooppunten en cloud) die u gebruiken om voorkeuren te behouden voor het werken met verschillende omgevingen, zoals voorkeuren met betrekking tot een clustereindpunt en of u standaard upgrade-implementaties wilt uitvoeren.
* Drie toepassingsparameterbestanden (hetzelfde als hierboven) die u gebruiken om omgevingsspecifieke toepassingsconfiguraties te onderhouden, zoals het aantal partities dat u voor een service moet maken. Meer informatie over het [configureren van uw toepassing voor meerdere omgevingen.](service-fabric-manage-multiple-environment-app-configuration.md)
* Een implementatiescript dat u gebruiken om uw toepassing te implementeren vanaf de opdrachtregel of als onderdeel van een geautomatiseerde pijplijn voor continue integratie en implementatie. Meer informatie over [het implementeren van toepassingen met PowerShell](service-fabric-deploy-remove-applications.md).
* Het toepassingsmanifest, dat de toepassing beschrijft. U vindt het manifest onder de map ApplicationPackageRoot. Meer informatie over [toepassings- en servicemanifesten](service-fabric-application-model.md).



## <a name="learn-more-about-the-programming-models"></a>Meer informatie over de programmeermodellen
Service Fabric biedt meerdere manieren om uw services te schrijven en te beheren.  Hier is overzicht en conceptuele informatie over [stateless en stateful Reliable Services](service-fabric-reliable-services-introduction.md), [Betrouwbare Actoren,](service-fabric-reliable-actors-introduction.md) [containers,](service-fabric-containers-overview.md) [gast executables](service-fabric-guest-executables-introduction.md), en [stateless en stateful ASP.NET Core services](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="learn-about-service-communication"></a>Meer informatie over servicecommunicatie
Een Service Fabric-toepassing bestaat uit verschillende services, waarbij elke service een gespecialiseerde taak uitvoert. Deze services kunnen met elkaar communiceren en er kunnen clienttoepassingen buiten het cluster zijn die verbinding maken met en communiceren met services. Meer informatie over het [instellen van communicatie met en tussen uw services](service-fabric-connect-and-communicate-with-services.md) in Service Fabric. 

## <a name="learn-about-configuring-application-security"></a>Meer informatie over het configureren van toepassingsbeveiliging
U toepassingen beveiligen die in het cluster worden uitgevoerd onder verschillende gebruikersaccounts. Service Fabric helpt ook de resources te beveiligen die worden gebruikt door toepassingen op het moment van implementatie onder de gebruikersaccounts, bijvoorbeeld bestanden, mappen en certificaten. Dit maakt het uitvoeren van toepassingen, zelfs in een gedeelde gehoste omgeving, veiliger van elkaar.  Meer informatie over het [configureren van beveiligingsbeleid voor uw toepassing](service-fabric-application-runas-security.md).

Uw toepassing kan gevoelige informatie bevatten, zoals tekenreeksen voor opslagverbindingen, wachtwoorden of andere waarden die niet in platte tekst mogen worden verwerkt. Meer informatie over het [beheren van geheimen in uw toepassing.](service-fabric-application-secret-management.md)

## <a name="learn-about-the-application-lifecycle"></a>Meer informatie over de levenscyclus van toepassingen
Net als bij andere platforms gaat een Service Fabric-toepassing meestal door de volgende fasen: ontwerp, ontwikkeling, testen, implementatie, upgraden, onderhoud en verwijdering. [In dit artikel](service-fabric-application-lifecycle.md) vindt u een overzicht van de API's en hoe deze worden gebruikt door de verschillende rollen gedurende de fasen van de levenscyclus van de Service Fabric-toepassing.

## <a name="next-steps"></a>Volgende stappen
- [Een Windows-cluster maken in Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- Visualiseer uw cluster, inclusief geïmplementeerde toepassingen en fysieke lay-out, met [Service Fabric Explorer.](service-fabric-visualizing-your-cluster.md)
- [Uw services versieen en upgraden](service-fabric-application-upgrade-tutorial.md)


