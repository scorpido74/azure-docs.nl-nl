---
title: A.Net Core-app maken en publiceren naar een extern Linux-cluster
description: .Net Core-apps maken en publiceren die zich richten op een extern Linux-cluster vanuit Visual Studio
author: peterpogorski
ms.topic: troubleshooting
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: c30eedb6782e4172d677f16e27441f28c78cdd89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614346"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>Visual Studio gebruiken om .Net Core-toepassingen te maken en te publiceren die zijn gericht op een extern Linux Service Fabric-cluster
Met Visual Studio tooling u Service Fabric .Net Core-toepassingen ontwikkelen en publiceren die zich richten op een Linux Service Fabric-cluster. De SDK-versie moet 3.4 of hoger zijn om een .Net Core-toepassing te implementeren die is gericht op Linux Service Fabric-clusters van Visual Studio.

> [!Note]
> Visual Studio ondersteunt geen foutopsporingsservicefabric-toepassingen die zich richten op Linux.
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>Een Service Fabric-toepassing maken die is gericht op .Net Core
1. Visual Studio starten als **beheerder**.
2. Maak een project met **File->Nieuw->Project**.
3. Kies in het dialoogvenster **Nieuw project** de optie **Cloud-> Service Fabric-toepassing**.
![maken-toepassing]
4. Geef de toepassing een naam en klik op **Ok**.
5. Selecteer op de pagina **Nieuwe servicefabricservice** het type service dat u wilt maken onder de **sectie .Net Core**.
![create-service]

## <a name="deploy-to-a-remote-linux-cluster"></a>Implementeren in een extern Linux-cluster
1. Klik in de oplossingsverkenner met de rechtermuisknop op de toepassing en selecteer **Bouwen**.
![build-applicatie]
2. Zodra het bouwproces voor de toepassing is voltooid, klikt u met de rechtermuisknop op de service en selecteert u het **csproj-bestand**bewerken.
![bewerken-csproj]
3. Bewerk de eigenschap UpdateServiceFabricManifestEnabled van True naar **False** als de service een **actorprojecttype**is. Als uw toepassing geen actorservice heeft, gaat u naar stap 4.
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> Als u UpdateServiceFabricManifestInstellen d.m.v. false instellen, worden updates van servicemanifest.xml tijdens een build uitgeschakeld. Wijzigingen zoals toevoegen, verwijderen of de naam van de service wijzigen, worden niet weergegeven in ServiceManifest.xml. Als er wijzigingen worden aangebracht, moet u de ServiceManifest handmatig bijwerken of UpdateServiceManifestManifestD instellen op true en de service bouwen die de ServiceManifest.xml bijwerkt en deze vervolgens terugzet naar false.
>

4. Werk de RuntimeIndetifier bij van win7-x64 naar het doelplatform in het serviceproject.
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. Werk in het ServiceManifest het entrypoint-programma bij om .exe te verwijderen. 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. Klik in Solution Explorer met de rechtermuisknop op de toepassing en selecteer **Publiceren**. Het dialoogvenster **Publiceren** wordt weergegeven.
7. Selecteer **in Verbindingseindpunt**het eindpunt voor het externe Linux-cluster van Service Fabric dat u wilt targeten.
![publicatie-aanvraag]

<!--Image references-->
[maken-toepassing]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[create-service]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[build-applicatie]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[bewerken-csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[publicatie-aanvraag]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [aan de slag met Service Fabric met .Net Core](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
