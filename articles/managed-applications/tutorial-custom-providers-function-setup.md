---
title: Azure Functions instellen voor aangepaste Azure-providers
description: In deze zelf studie wordt uitgelegd hoe u een Azure function-app maakt en deze instelt op werken met aangepaste Azure-providers
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 6b5ab6948d382a9925c9ced91e04f360ecf51a0e
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173029"
---
# <a name="set-up-azure-functions-for-azure-custom-providers"></a>Azure Functions instellen voor aangepaste Azure-providers

Een aangepaste provider is een contract tussen Azure en een eind punt. Met aangepaste providers kunt u werk stromen wijzigen in Azure. In deze zelf studie leert u hoe u een Azure function-app instelt voor gebruik als een eind punt voor een aangepaste provider.

## <a name="create-the-azure-function-app"></a>De Azure-functie-app maken

> [!NOTE]
> In deze zelf studie maakt u een eenvoudig service-eind punt dat gebruikmaakt van een Azure-functie-app. Een aangepaste provider kan echter elk openbaar toegankelijk eind punt gebruiken. Alternatieven zijn Azure Logic Apps, Azure API Management en de Web Apps functie van Azure App Service.

Als u deze zelf studie wilt starten, moet u eerst de zelf studie [uw eerste Azure-functie-app maken in de Azure Portal](../azure-functions/functions-create-first-azure-function.md). In deze zelf studie maakt u een .NET core-webhook-functie die kan worden gewijzigd in de Azure Portal. Het is ook de basis van de huidige zelf studie.

## <a name="install-azure-table-storage-bindings"></a>Azure Table Storage-bindingen installeren

De Azure Table Storage-bindingen installeren:

1. Ga naar het tabblad **integreren** voor de http trigger.
1. Selecteer **+ nieuwe invoer**.
1. Selecteer **Azure Table Storage**.
1. Installeer de extensie micro soft. Azure. webjobs. Extensions. Storage als deze nog niet is geïnstalleerd.
1. Typ **tableStorage**in het vak **naam para meter van tabel** .
1. Voer in het vak **tabel naam** **myCustomResources**in.
1. Selecteer **Opslaan** om de bijgewerkte invoer parameter op te slaan.

![Overzicht van de aangepaste provider waarin tabel bindingen worden weer gegeven](./media/create-custom-providers/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>RESTERENDE HTTP-methoden bijwerken

De Azure-functie zo instellen dat de vervolg aanvraag methoden van de aangepaste provider worden toegevoegd:

1. Ga naar het tabblad **integreren** voor de http trigger.
1. Selecteer onder **geselecteerde HTTP-methoden** **ophalen**, **plaatsen**, **verwijderen**en **plaatsen**.

![Overzicht van de aangepaste provider met HTTP-methoden](./media/create-custom-providers/azure-functions-http-methods.png)

## <a name="add-azure-resource-manager-nuget-packages"></a>Azure Resource Manager NuGet-pakketten toevoegen

> [!NOTE]
> Als uw C# project bestand ontbreekt in de projectmap, kunt u het hand matig toevoegen. Het wordt ook weer gegeven nadat de uitbrei ding micro soft. Azure. webjobs. Extensions. Storage is geïnstalleerd op de functie-app.

Werk vervolgens het C# project bestand bij om nuttige NuGet-bibliotheken op te kunnen bevatten. Deze bibliotheken maken het gemakkelijker om binnenkomende aanvragen van aangepaste providers te parseren. Volg de stappen om [uitbrei dingen toe te voegen vanuit de portal](../azure-functions/install-update-binding-extensions-manual.md) en werk het C# project bestand bij om de volgende pakket verwijzingen op te nemen:

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

Het volgende XML-element is een C# voorbeeld project bestand:

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>netstandard2.0</TargetFramework>
    <WarningsAsErrors />
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
    <PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
    <PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
  </ItemGroup>
</Project>
```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie stelt u een Azure function-app in om te werken als een Azure aangepaste provider-eind punt.

Zie [zelf studie voor meer informatie over het maken van een gerustend eind punt van een aangepaste provider: Een REST-eind punt](./tutorial-custom-providers-function-authoring.md)van een aangepaste provider ontwerpen.

