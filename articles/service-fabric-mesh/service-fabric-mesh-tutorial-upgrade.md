---
title: Zelfstudie- Een Azure Service Fabric-mesh-toepassing upgraden
description: Deze zelfstudie is deel vier van een serie en laat u zien hoe u een Azure Service Fabric Mesh-toepassing rechtstreeks vanuit Visual Studio upgraden.
author: dkkapur
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 7cdb8868f760ef0f35ab90c06b411110f871738c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75351720"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Zelfstudie: meer informatie over het upgraden van een Service Fabric-toepassing met Visual Studio

Deze zelfstudie is deel vier van een serie en laat u zien hoe u een Azure Service Fabric Mesh-toepassing rechtstreeks vanuit Visual Studio upgraden. De upgrade bevat zowel een code-update als een config-update. U ziet dat de stappen voor het upgraden en publiceren vanuit Visual Studio hetzelfde zijn.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een Service Fabric Mesh-service upgraden met Visual Studio

In deze zelfstudiereeks leert u het volgende:
> [!div class="checklist"]
> * [Een Service Fabric Mesh-app maken in Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Fouten opsporen in een Service Fabric Mesh-app die wordt uitgevoerd in de lokale ontwikkelcluster](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Een Service Fabric Mesh-app implementeren](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Een Service Fabric Mesh-app bijwerken
> * [Service Fabric Mesh-bronnen opschonen](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* Als u de to-do-app nog niet hebt geïmplementeerd, volgt u de instructies in [Zelfstudie: een Service Fabric Mesh-webtoepassing implementeren](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Een Service Fabric Mesh-service upgraden met Visual Studio

In dit artikel ziet u hoe u een microservice binnen een toepassing upgraden. In dit voorbeeld wijzigen we `WebFrontEnd` de service om een taakcategorie weer te geven en de hoeveelheid CPU die deze heeft gegeven te verhogen. Dan upgraden we de geïmplementeerde service.

## <a name="modify-the-config"></a>De config wijzigen

Wanneer u een Service Fabric Mesh-app maakt, voegt Visual studio een **parameters.yaml-bestand** toe voor elke implementatieomgeving (cloud en lokaal). In deze bestanden u parameters en hun waarden definiëren waarnaar kan worden verwezen vanuit uw Mesh *.yaml-bestanden zoals service.yaml of network.yaml.  Visual Studio biedt een aantal variabelen voor u, zoals hoeveel CPU de service kan gebruiken.

We werken de `WebFrontEnd_cpu` parameter bij om `1.5` de cpu-bronnen bij te werken naar in afwachting dat de **WebFrontEnd-service** meer wordt gebruikt.

1. Open in het **todolistapp-project** onder **Environments** > **Cloud**het bestand **parameters.yaml.** Wijzig `WebFrontEnd_cpu`de waarde `1.5`in . De parameternaam wordt voorafgegaan door `WebFrontEnd_` de servicenaam als aanbevolen praktijk om deze te onderscheiden van parameters met dezelfde naam die van toepassing zijn op verschillende services.

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. Open het **service.yaml-bestand** van het **WebFrontEnd-project** onder **WebFrontEnd-servicebronnen** > **Service Resources**.

    Houd er `resources:` rekening `cpu:` mee dat `"[parameters('WebFrontEnd_cpu')]"`de in-sectie is ingesteld op . Als het project wordt gebouwd voor de `'WebFrontEnd_cpu` cloud, wordt de waarde voor de **omgevingen** >  `1.5`**cloud** > **parameters.yaml** bestand, en zal worden . Als het project wordt gebouwd om lokaal uit te voeren, wordt de waarde uit het bestand **Omgevingen** > **Lokaal** > **parameters.yaml** gehaald en wordt deze '0,5'.

> [!Tip]
> Standaard wordt het parameterbestand dat een peer van het bestand profile.yaml is gebruikt om de waarden voor dat profiel.yaml-bestand te leveren.
> Omgevingen > Cloud > parameters.yaml bieden bijvoorbeeld de parameterwaarden voor omgevingen > Cloud > profile.yaml.
>
> U dit overschrijven door het volgende toe`parametersFilePath=”relative or full path to the parameters file”` te voegen `parametersFilePath=”C:\MeshParms\CustomParameters.yaml”` aan het bestand profile.yaml:`parametersFilePath=”..\CommonParameters.yaml”`

## <a name="modify-the-model"></a>Het model wijzigen

Als u een codewijziging `Category` wilt `ToDoItem` invoeren, `ToDoItem.cs` voegt u een eigenschap toe aan de klasse in het bestand.

```csharp
public class ToDoItem
{
    public string Category { get; set; }
    ...
}
```

Werk vervolgens `Load()` de methode in hetzelfde bestand bij om de categorie in te stellen op een standaardtekenreeks:

```csharp
public static ToDoItem Load(string description, int index, bool completed)
{
    ToDoItem newItem = new ToDoItem(description)
    {
        Index = index,
        Completed = completed, 
        Category = "none"    // <-- add this line
    };

    return newItem;
}
```

## <a name="modify-the-service"></a>De service wijzigen

Het `WebFrontEnd` project is een ASP.NET Core-toepassing met een webpagina met takenlijstitems. Open `WebFrontEnd` `Index.cshtml` en voeg in het project de volgende twee regels, hieronder aangegeven, toe om de categorie van de taak weer te geven:

```HTML
<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Category</th>           @*add this line*@
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Category</td>           @*add this line*@
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

Bouw en voer de app uit om te controleren of u een nieuwe categoriekolom ziet op de webpagina met de taken.

## <a name="upgrade-the-app-from-visual-studio"></a>De app upgraden vanuit Visual Studio

Of u nu een code-upgrade maakt of een config-upgrade (in dit geval doen we beide), upgrade uw Service Fabric Mesh-app op Azure door met de rechtermuisknop op **de todolistapp** in Visual Studio te klikken en vervolgens **Publiceren te selecteren...**

Vervolgens ziet u het dialoogvenster **Service Fabric-toepassing publiceren**.

Gebruik de vervolgkeuzelijst **Doelprofiel** om het profiel.yaml-bestand te selecteren dat u voor deze implementatie wilt gebruiken. We upgraden de app in de cloud, dus we selecteren de **cloud.yaml** in de vervolgkeuzelijst, die de `WebFrontEnd_cpu` waarde van 1.0 gebruikt die in dat bestand is gedefinieerd.

![Dialoogvenster in Visual Studio over Service Fabric Mesh-publicatie](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Selecteer uw Azure-account en -abonnement. Stel de locatie in **op** de locatie die u hebt gebruikt toen u de takenapp oorspronkelijk publiceerde op Azure. Dit artikel gebruikt **Oost-VS**.

**Stel de resourcegroep** in op de resourcegroep die u hebt gebruikt toen u de takenapp oorspronkelijk in Azure publiceerde.

Stel **Azure Container Registry** in op de naam azure containerregistry die u hebt gemaakt toen u de takenapp oorspronkelijk publiceerde in Azure.

Druk in het publicatiedialoogvenster op de knop **Publiceren** om de to-do-app op Azure te upgraden.

Controleer de voortgang van de upgrade door het deelvenster **Hulpmiddelen voor servicestructuur** te selecteren in het venster **Visual Studio-uitvoer.** 

Nadat de afbeelding is gemaakt en naar het Azure Container Registry is geschoven, wordt er een **statuskoppeling voor** weergegeven in de uitvoer waarop u klikken om de implementatie in de Azure-portal te controleren.

Zodra de upgrade is voltooid, wordt in de uitvoer **van Service Fabric Tools** het IP-adres en de poort van uw toepassing weergegeven in de vorm van een URL.

```json
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Open een webbrowser en navigeer naar de URL om de website in Azure uitgevoerd te zien worden. U ziet nu een webpagina met een categoriekolom.

## <a name="next-steps"></a>Volgende stappen

In dit deel van de zelfstudie hebt u het volgende geleerd:
> [!div class="checklist"]
> * Een Service Fabric Mesh-app upgraden met Visual Studio

Ga door naar de volgende zelfstudie:
> [!div class="nextstepaction"]
> [Service Fabric Mesh-bronnen opschonen](service-fabric-mesh-tutorial-cleanup-resources.md)