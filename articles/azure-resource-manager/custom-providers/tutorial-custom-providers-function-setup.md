---
title: Azure Functions instellen
description: Deze zelfstudie gaat over het maken van een Azure-functie-app en deze instellen om te werken met Azure Custom Providers
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: b6984bfdf7ec078d02999bccf77f3592cfbcab27
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75649986"
---
# <a name="set-up-azure-functions-for-azure-custom-providers"></a>Azure-functies instellen voor Azure Custom Providers

Een aangepaste provider is een contract tussen Azure en een eindpunt. Met aangepaste providers u werkstromen in Azure wijzigen. In deze zelfstudie ziet u hoe u een Azure-functie-app instelt om te werken als een aangepast eindpunt van de provider.

## <a name="create-the-azure-function-app"></a>De Azure-functie-app maken

> [!NOTE]
> In deze zelfstudie maakt u een eenvoudig serviceeindpunt dat een Azure-functie-app gebruikt. Een aangepaste provider kan echter elk openbaar toegankelijk eindpunt gebruiken. Alternatieven zijn Azure Logic Apps, Azure API Management en de Web Apps-functie van Azure App Service.

Als u deze zelfstudie wilt starten, moet u eerst de zelfstudie Volgen [Maak uw eerste Azure-functie-app in de Azure-portal.](../../azure-functions/functions-create-first-azure-function.md) Die zelfstudie maakt een .NET-kernwebhook-functie die kan worden gewijzigd in de Azure-portal. Het is ook de basis voor de huidige tutorial.

## <a name="install-azure-table-storage-bindings"></a>Azure Table-opslagbindingen installeren

Ga als het gaat om het installeren van de Azure Table-opslagbindingen:

1. Ga naar het tabblad **Integreren** voor de HttpTrigger.
1. Selecteer **+ Nieuwe invoer**.
1. Selecteer **Azure-tabelopslag**.
1. Installeer de extensie Microsoft.Azure.WebJobs.Extensions.Storage als deze nog niet is geïnstalleerd.
1. Voer **tabelOpslag**in in het vak Naam van de **parametertabel** .
1. Voer in het vak **Tabelnaam** **myCustomResources**in .
1. Selecteer **Opslaan** om de bijgewerkte invoerparameter op te slaan.

![Overzicht van aangepaste providermet tabelbindingen](./media/create-custom-provider/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>RESTful HTTP-methoden bijwerken

Ga als u de Azure-functie zo in stelt dat de aangepaste provider RESTful-aanvraagmethoden zijn opgenomen:

1. Ga naar het tabblad **Integreren** voor de HttpTrigger.
1. Selecteer **onder Geselecteerde HTTP-methoden**de optie **GET,** **POST**, **Delete**en **PUT**.

![Overzicht van aangepaste providermet HTTP-methoden](./media/create-custom-provider/azure-functions-http-methods.png)

## <a name="add-azure-resource-manager-nuget-packages"></a>Azure Resource Manager NuGet-pakketten toevoegen

> [!NOTE]
> Als uw C#-projectbestand ontbreekt in de projectmap, u het handmatig toevoegen. Of het wordt weergegeven nadat de extensie Microsoft.Azure.WebJobs.Extensions.Storage is geïnstalleerd in de functie-app.

Werk vervolgens het C#-projectbestand bij met handige NuGet-bibliotheken. Deze bibliotheken maken het eenvoudiger om binnenkomende aanvragen van aangepaste providers te ontleden. Volg de stappen om extensies toe te [voegen vanuit de portal](../../azure-functions/install-update-binding-extensions-manual.md) en werk het C#-projectbestand bij om de volgende pakketverwijzingen op te nemen:

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

Het volgende XML-element is een voorbeeld C#-projectbestand:

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

In deze zelfstudie stelt u een Azure-functie-app in om te werken als eindpunt van een aangepaste Azure-provider.

Zie Zelfstudie: Een restful aangepast providereindpunt voor meer informatie over het maken van een RESTful custom provider.To learn how to author a RESTful custom provider endpoint, see [Tutorial: Authoring a RESTful custom provider endpoint](./tutorial-custom-providers-function-authoring.md).

