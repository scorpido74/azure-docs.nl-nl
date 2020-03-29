---
title: Status rapporteren en controleren met Azure Service Fabric
description: Meer informatie over het verzenden van statusrapporten vanuit uw servicecode en hoe u de status van uw service controleren met behulp van de hulpprogramma's voor statusbewaking die Azure Service Fabric biedt.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 2b7a9c44a84e3ce15eaec22c8f57bb48f79dae05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464637"
---
# <a name="report-and-check-service-health"></a>Servicestatus rapporteren en controleren
Wanneer uw services problemen ondervinden, is uw vermogen om te reageren op en incidenten en uitval op te lossen afhankelijk van uw vermogen om de problemen snel op te sporen. Als u problemen en fouten rapporteert aan de Azure Service Fabric-statusbeheerder vanuit uw servicecode, u standaardhulpprogramma's voor statusbewaking gebruiken die Service Fabric biedt om de status van de status te controleren.

Er zijn drie manieren waarop u de status van de service melden:

* Gebruik [Partitie-](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition) of [CodePackageActivationContext-objecten.](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext)  
  U `Partition` de `CodePackageActivationContext` objecten en objecten gebruiken om de status te rapporteren van elementen die deel uitmaken van de huidige context. Code die wordt uitgevoerd als onderdeel van een replica, kan bijvoorbeeld alleen status rapporteren op die replica, de partitie waartoe deze behoort en de toepassing waarvan deze deel uitmaakt.
* Gebruik `FabricClient`.   
  U kunt `FabricClient` de status van de servicecode rapporteren als het cluster niet [veilig](service-fabric-cluster-security.md) is of als de service wordt uitgevoerd met beheerdersrechten. De meeste scenario's in de echte wereld gebruiken geen onbeveiligde clusters of bieden geen beheerdersrechten. Met `FabricClient`u de status rapporteren over elke entiteit die deel uitmaakt van het cluster. Idealiter moet servicecode echter alleen rapporten verzenden die gerelateerd zijn aan de eigen gezondheid.
* Gebruik de REST-API's op de cluster-, toepassings-, geïmplementeerde toepassing,service-, servicepakket-, partitie-, replica- of knooppuntniveaus. Dit kan worden gebruikt om de gezondheid te melden vanuit een container.

In dit artikel vindt u een voorbeeld dat de status van de servicecode rapporteert. Het voorbeeld laat ook zien hoe de tools van Service Fabric kunnen worden gebruikt om de status van de status te controleren. Dit artikel is bedoeld als een snelle introductie tot de mogelijkheden voor gezondheidsmonitoring van Service Fabric. Voor meer gedetailleerde informatie, u de reeks diepgaande artikelen over gezondheid lezen die beginnen met de link aan het einde van dit artikel.

## <a name="prerequisites"></a>Vereisten
U moet het volgende hebben geïnstalleerd:

* Visual Studio 2015 of Visual Studio 2019
* Service Fabric SDK

## <a name="to-create-a-local-secure-dev-cluster"></a>Een lokaal beveiligd dev-cluster maken
* Open PowerShell met beheerdersbevoegdheden en voer de volgende opdrachten uit:

![Opdrachten die laten zien hoe u een beveiligd ontwikkelaarscluster maakt](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Een toepassing implementeren en de status ervan controleren
1. Open Visual Studio als beheerder.
1. Maak een project met behulp van de sjabloon **Stateful Service.**
   
    ![Een Service Fabric-toepassing maken met Stateful Service](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)
1. Druk op **F5** om de toepassing in de foutopsporingsmodus uit te voeren. De toepassing wordt geïmplementeerd in het lokale cluster.
1. Nadat de toepassing is uitgevoerd, klikt u met de rechtermuisknop op het pictogram Lokaal clusterbeheer in het meldingsgebied en selecteert u **Lokaal cluster beheren** in het snelmenu om Service Fabric Explorer te openen.
   
    ![Open Service Fabric Explorer vanuit het meldingsgebied](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)
1. De toepassingsstatus moet worden weergegeven zoals in deze afbeelding. Op dit moment moet de toepassing gezond zijn zonder fouten.
   
    ![Gezonde toepassing in Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)
1. U de status ook controleren met PowerShell. U kunt ```Get-ServiceFabricApplicationHealth``` de status van een toepassing controleren ```Get-ServiceFabricServiceHealth``` en u de status van een service controleren. Het gezondheidsrapport voor dezelfde toepassing in PowerShell bevindt zich in deze afbeelding.
   
    ![Gezonde toepassing in PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Aangepaste statusgebeurtenissen toevoegen aan uw servicecode
De projectsjablonen servicefabric in Visual Studio bevatten voorbeeldcode. In de volgende stappen ziet u hoe u aangepaste statusgebeurtenissen rapporteren vanuit uw servicecode. Dergelijke rapporten worden automatisch weergegeven in de standaardhulpprogramma's voor statusbewaking die Service Fabric biedt, zoals Service Fabric Explorer, Azure-portalstatusweergave en PowerShell.

1. Open de toepassing die u eerder hebt gemaakt in Visual Studio opnieuw of maak een nieuwe toepassing met behulp van de sjabloon **Stateful Service** Visual Studio.
1. Open het Stateful1.cs-bestand `myDictionary.TryGetValueAsync` en zoek `RunAsync` de aanroep in de methode. U zien dat `result` deze methode een retourneert met de huidige waarde van de teller, omdat de belangrijkste logica in deze toepassing is om een telling actief te houden. Als deze toepassing een echte toepassing was en als het gebrek aan resultaat een fout vertegenwoordigde, zou u die gebeurtenis willen markeren.
1. Als u een statusgebeurtenis wilt melden wanneer het gebrek aan resultaat een fout betekent, voegt u de volgende stappen toe.
   
    a. Voeg `System.Fabric.Health` de naamruimte toe aan het Stateful1.cs bestand.
   
    ```csharp
    using System.Fabric.Health;
    ```
   
    b. De volgende code `myDictionary.TryGetValueAsync` toevoegen na de aanroep
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    We rapporteren replica status omdat het wordt gemeld van een stateful service. De `HealthInformation` parameter slaat informatie op over het gezondheidsprobleem dat wordt gerapporteerd.
   
    Als u een stateless service hebt gemaakt, gebruikt u de volgende code
   
    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```
1. Als uw service wordt uitgevoerd met beheerdersrechten [secure](service-fabric-cluster-security.md)of als het `FabricClient` cluster niet veilig is, u deze ook gebruiken om de status te rapporteren zoals weergegeven in de volgende stappen.  
   
    a. De `FabricClient` instantie maken `var myDictionary` na de aangifte.
   
    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```
   
    b. Voeg de volgende `myDictionary.TryGetValueAsync` code toe na het gesprek.
   
    ```csharp
    if (!result.HasValue)
    {
       var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.Context.PartitionId,
            this.Context.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```
1. Laten we deze fout simuleren en deze zien verschijnen in de hulpprogramma's voor gezondheidsbewaking. Als u de fout wilt simuleren, geeft u commentaar op de eerste regel in de statusrapportagecode die u eerder hebt toegevoegd. Nadat u de eerste regel hebt gereageerd, ziet de code eruit als het volgende voorbeeld.
   
    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
   Deze code wordt elke `RunAsync` keer uitgevoerd in het gezondheidsrapport. Nadat u de wijziging hebt gewijzigd, drukt u op **F5** om de toepassing uit te voeren.
1. Nadat de toepassing is uitgevoerd, opent u Service Fabric Explorer om de status van de toepassing te controleren. Deze keer laat Service Fabric Explorer zien dat de toepassing niet in orde is. De toepassing wordt als ongezond weergegeven omdat de fout die is gerapporteerd van de code die we eerder hebben toegevoegd.
   
    ![Ongezonde toepassing in Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)
1. Als u de primaire replica selecteert in de structuurweergave van Service Fabric Explorer, ziet u dat **de status** ook een fout aangeeft. Service Fabric Explorer geeft ook de details `HealthInformation` van het gezondheidsrapport weer die zijn toegevoegd aan de parameter in de code. U dezelfde statusrapporten bekijken in PowerShell en de Azure-portal.
   
    ![Replicastatus in Service Fabric Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Dit rapport blijft in de statusmanager totdat het wordt vervangen door een ander rapport of totdat deze replica is verwijderd. Omdat we dit `TimeToLive` gezondheidsrapport niet `HealthInformation` in het object hebben ingesteld, verloopt het rapport nooit.

We raden aan om de gezondheid te melden op het meest gedetailleerde niveau, wat in dit geval de replica is. U ook `Partition`gezondheid melden op.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

Om gezondheid `Application`te `DeployedApplication`melden `DeployedServicePackage`over `CodePackageActivationContext`, en , gebruik .

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Volgende stappen
* [Diepe duik op Service Fabric gezondheid](service-fabric-health-introduction.md)
* [REST API voor status van rapportageservice](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service)
* [REST-API voor het rapporteren van toepassingsstatus](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-an-application)

