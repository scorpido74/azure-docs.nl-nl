---
title: 'Zelfstudie: Een nieuwe HoloLens Unity-app maken'
description: In deze zelfstudie leert u hoe u een nieuwe HoloLens Unity-app maakt met Azure Spatial Anchors.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 07/05/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: e1abb759c80e770f1e650c232b6b2e21232b7e6f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75457717"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>Zelfstudie: stapsgewijze instructies voor het maken van een nieuwe HoloLens Unity-app met Azure Spatial Anchors

In deze zelfstudie ziet u hoe u een nieuwe HoloLens Unity-app maakt met Azure Spatial Anchors.

## <a name="prerequisites"></a>Vereisten

Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien:

1. Een Windows-machine met <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017+</a> geïnstalleerd met de **universal Windows Platform development** workload en de Windows **10 SDK (10.0.18362.0 of nieuwer)** component, en <a href="https://git-scm.com/download/win" target="_blank">Git voor Windows</a>.
2. De [C++/WinRT Visual Studio Extension (VSIX)](https://aka.ms/cppwinrt/vsix) voor Visual Studio moet worden geïnstalleerd vanuit de [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
3. Een HoloLens-apparaat waarvoor de [ontwikkelaarsmodus](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) is ingeschakeld. Dit artikel vereist een HoloLens-apparaat met de [Update voor Windows van 10 oktober 2018](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (ook wel bekend als RS5). Als u wilt bijwerken naar de nieuwste release op HoloLens, opent u de app **Instellingen**, gaat u naar **Bijwerken en beveiliging** en selecteert u vervolgens de knop **Controleren op updates**.

## <a name="getting-started"></a>Aan de slag

We zullen eerst ons project en Unity scene opzetten:
1. Start Unity.
2. Selecteer **Nieuw**.
4. Zorg ervoor dat **3D** is geselecteerd.
5. Geef uw project een naam en voer een **opslaglocatie in**.
6. Klik **op Project maken**.
7. Sla de lege standaardscène op in een nieuw bestand met: **Bestand** > **opslaan als**.
8. Geef de nieuwe scène **Hoofd een** naam en druk op **opslaan.**

**De projectinstellingen instellen**

We stellen nu een aantal Unity-projectinstellingen in die ons helpen de Windows Holographic SDK te targeten voor ontwikkeling.

Ten eerste, laat instellen kwaliteitsinstellingen voor onze applicatie.
1. De kwaliteit**van projectinstellingen** >  **bewerken** > **selecteren**
2. Klik in de kolom onder het **Windows Store-logo** op de pijl in de **rij Standaard** en selecteer Zeer **laag**. U weet dat de instelling correct wordt toegepast wanneer het vak in de **kolom Windows Store** en Very **Low-rij** groen is.

We moeten Unity laten weten dat de app die we proberen te exporteren een meeslepende weergave moet creëren in plaats van een 2D-weergave. We creëren een meeslepende weergave door Virtual Reality-ondersteuning in te schakelen op Unity die zich richt op de Windows 10 SDK.

1. Ga naar > **Projectinstellingen** > **bewerken .** **Edit**
2. Selecteer in het **deelvenster Controle** voor **spelers**het pictogram van de **Windows Store.**
3. De groep **XR-instellingen** uitvouwen.
4. Schakel in de sectie **Rendering** het selectievakje **Ondersteund virtual reality-ondersteuning** in om de lijst van een nieuwe Virtual Reality **SDK** toe te voegen.
5. Controleer of **Windows Mixed Reality** in de lijst wordt weergegeven. Zo niet, **+** selecteer dan de knop onder aan de lijst en kies **Windows Mixed Reality**.

> [!NOTE]
> Als u het Windows Store-pictogram niet ziet, controleert u of u de Backend voor Windows Store .NET Scripting hebt geselecteerd voordat u wordt geïnstalleerd. Als dit niet het zo is, moet u Unity mogelijk opnieuw installeren met de juiste Windows-installatie.

**Backend-configuratie scripting verifiëren**
1. Ga naar > **Projectinstellingen** > player **bewerken**(mogelijk is **Player** nog steeds geopend vanaf de vorige stap).**Player**
2. Selecteer in het **deelvenster Controle** voor **spelers**het pictogram van de **Windows Store.**
3. Controleer in de sectie **Configuratie overige instellingen** of **scripting backend** is ingesteld op **IL2CPP**.

**Mogelijkheden instellen**
1. Ga naar > **Projectinstellingen** > player **bewerken**(mogelijk is **Player** nog steeds geopend vanaf de vorige stap).**Player**
2. Selecteer in het **deelvenster Controle** voor **spelers**het pictogram van de **Windows Store.**
3. Controleer in de sectie Configuratie **van publicatie-instellingen** **internetclientserver** en **ruimtelijke perceptie**.

**De hoofdcamera instellen**
1. Selecteer **Hoofdcamera**in het **deelvenster Hiërarchie**.
2. Stel **in**de inspecteur de transformatiepositie in op **0,0,0**.
3. Zoek de eigenschap **Vlaggen wissen** en wijzig de vervolgkeuzelijst van **Skybox** naar **Effen kleur.**
4. Klik op het veld **Achtergrond** om een kleurkiezer te openen.
5. Stel **R, G, B en A** op **0**in.
6. Selecteer **Component toevoegen** en zoek naar en voeg de **collider voor ruimtelijke toewijzing**toe .

**Ons script maken**
1. Maak in het deelvenster **Project** een nieuwe map, **Scripts,** onder de map **Assets.**
2. Klik met de rechtermuisknop op de map en selecteer **vervolgens >** maken, **C# Script**. Titel **azurespatialanchorsScript**.
3. Ga naar **GameObject** -> **Leeg maken**.
4. Selecteer deze en wijzig in De **inspecteur** de naam van **GameObject** naar **MixedRealityCloud**. Selecteer **Component toevoegen** en zoeken naar en voeg het **AzureSpatialAnchorsScript**toe .

**De sfeer prefab maken**
1. Ga naar **GameObject** -> **3D-objectbol** -> **Sphere**.
2. Stel in de **inspecteur**de schaal in op **0,25, 0,25, 0,25**.
3. Zoek het object **Sphere** in het deelvenster **Hiërarchie.** Klik erop en sleep deze naar de map **Assets** in het deelvenster **Project.**
4. Klik met de rechtermuisknop en verwijder de oorspronkelijke bol **die** u hebt gemaakt in het deelvenster **Hiërarchie.**

U moet nu een sfeerprefab in uw **projectvenster** hebben.

## <a name="trying-it-out"></a>Het uitproberen
Om uit te testen of alles werkt, bouwt u uw app in **Unity** en implementeert u deze vanuit **Visual Studio.** Volg hoofdstuk 6 van de [ **MR Basics 100: Aan de slag met Unity** cursus](https://docs.microsoft.com/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio) om dit te doen. U ziet het startscherm van Unity en vervolgens een duidelijke weergave.

## <a name="place-an-object-in-the-real-world"></a>Plaats een object in de echte wereld
Laten we een object & plaatsen met uw app. Open de Visual Studio-oplossing die we hebben gemaakt toen we [onze app hebben geïmplementeerd.](#trying-it-out)

Voeg eerst de volgende `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs`invoer toe aan uw:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

Voeg vervolgens de volgende ledenvariabelen `AzureSpatialAnchorsScript` toe aan uw klas:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-42,48-52,60-79)]

Voordat we verder gaan, moeten we de sfeer prefab die we hebben gemaakt op onze spherePrefab lid variabele. Ga terug naar **Unity.**
1. Selecteer **in Unity**het object **MixedRealityCloud** in het deelvenster **Hiërarchie.**
2. Klik op de **Sphere** prefab die u hebt opgeslagen in het deelvenster **Project.** Sleep de **sfeer waarop** u hebt geklikt in het gebied **Sphere Prefab** onder Azure **Spatial Anchors Script (Script)** in het deelvenster **Controle.**

U moet nu de **Sphere** instellen als de prefab op uw script. Bouw op van **Unity** en open vervolgens de resulterende **Visual Studio-oplossing** opnieuw, zoals je net deed in het uitproberen van [het uit](#trying-it-out).

In **Visual Studio** `AzureSpatialAnchorsScript.cs` , weer open. Voeg de volgende `Start()` code toe aan uw methode. Deze code zal `GestureRecognizer`aansluiten, die zal detecteren wanneer `HandleTap`er een luchtkraan en bellen .

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=81-90,93&highlight=4-10)]

We moeten nu de `HandleTap()` volgende `Update()`methode hieronder toe te voegen . Het zal een straal gieten doen en een geraakt punt krijgen waarop een bol te plaatsen.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=267-277,299-300,304-312)]

We moeten nu de bol creëren. De bol zal in eerste instantie wit zijn, maar deze waarde zal later worden aangepast. Voeg de `CreateAndSaveSphere()` volgende methode toe:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-325,390)]

Voer uw app uit **Visual Studio** om deze nogmaals te valideren. Tik deze keer op het scherm om & plaats je witte bol over het oppervlak van je keuze.

## <a name="set-up-the-dispatcher-pattern"></a>Het verzenderpatroon instellen

Bij het werken met Unity moeten alle Unity API's, bijvoorbeeld API's die u gebruikt om UI-updates uit te voeren, op de hoofdthread plaatsvinden. In de code die we zullen schrijven echter, krijgen we callbacks op andere threads. We willen ui bijwerken in deze callbacks, dus we hebben een manier nodig om van een zijdraad naar de hoofdthread te gaan. Om code uit te voeren op de hoofdthread van een zijdraad, gebruiken we het verzenderpatroon.

Laten we een lidvariabele toevoegen, dispatchQueue, een wachtrij met acties. We duwen Acties in de wachtrij en zetten de wachtrij en voeren de acties uit op de hoofdthread.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=38-51&highlight=6-9)]

Laten we vervolgens een manier toevoegen om een actie toe te voegen aan de wachtrij. Direct `QueueOnUpdate()` na `Update()` :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=107-117)]

Laten we nu de lus Update() gebruiken om te controleren of er een actie in de wachtrij staat. Als dat zo is, zullen we de actie in de wachtrij en voer het uit.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=95-105&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>Download de Azure Spatial Anchors SDK

## <a name="via-unity-package"></a>[Via Unity Pakket](#tab/UnityPackage)

We downloaden nu de Azure Spatial Anchors SDK. Ga naar de [pagina Azure Spatial Anchors GitHub releases](https://github.com/Azure/azure-spatial-anchors-samples/releases). Download onder Assets het **pakket AzureSpatialAnchors.unity**. Klik in Unity naar **Assets,** klik op Pakket aangepast **pakket** > **importeren...**. Navigeer naar het pakket en selecteer **Openen**.

Schakel in het nieuwe venster **Unity Package importeren** dat verschijnt de optie **Plug-in seinen** uit en klik rechtsonder op **Importeren.**

We moeten nu Nuget-pakketten herstellen om Azure Spatial Anchors SDK te krijgen. Bouw op van **Unity** en open en bouw de resulterende **Visual Studio-oplossing** opnieuw, zoals beschreven in Het [uitproberen ervan.](#trying-it-out)

## <a name="via-nugetforunity"></a>[Via NuGetForUnity](#tab/NuGetForUnity)

Eerst moeten we NuGetForUnity installeren. Ga naar de [NuGetForUnity GitHub releases pagina](https://github.com/GlitchEnzo/NuGetForUnity/releases). Download onder Assets het nieuwste **NuGetForUnity.unitypackage**. Klik in Unity naar **Assets,** klik op Pakket aangepast **pakket** > **importeren...**. Navigeer naar het pakket en selecteer **Openen**. Unity installeert nu NugetForUnity. Als u geen nieuwe **nuget-vervolgkeuzelijst** in Unity ziet, moet u mogelijk met de rechtermuisknop klikken onder > **Projectenassets**. **Projects** Selecteer vervolgens **Alles opnieuw importeren**.

Zodra je NuGetForUnity hebt geïnstalleerd, selecteer je **NuGet** > **Manage NuGet-pakketten.** Zoek vervolgens naar Microsoft.Azure.SpatialAnchors.Unity en selecteer **Installeren**.

We moeten nu bouwen om de werkelijke Azure Spatial Anchors SDK te krijgen, omdat het NuGet-pakket dat we zojuist hebben gedownload, alleen helperscripts bevat. Bouw op van **Unity** en open en bouw de resulterende **Visual Studio-oplossing** opnieuw, zoals beschreven in Het [uitproberen ervan.](#trying-it-out)

---

Voeg in uw **Visual Studio-oplossing** `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs`de volgende import toe aan uw:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=18-21&highlight=1)]

Voeg vervolgens de volgende lidvariabelen `AzureSpatialAnchorsScript` toe aan uw klas:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=48-63&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>Een lokaal Azure-ruimtelijk anker koppelen aan het lokale anker

Laten we de CloudSpatialAnchorSession van Azure Spatial Anchor instellen. We beginnen met het `InitializeSession()` toevoegen van `AzureSpatialAnchorsScript` de volgende methode in je klas. Eenmaal gebeld, zorgt het ervoor dat een Azure Spatial Anchors-sessie wordt gemaakt en goed wordt geïnitialiseerd tijdens het opstarten van uw app.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=174-202,205-209)]

We moeten nu code schrijven om gedelegeerde oproepen af te handelen. We zullen meer toevoegen aan hen als we verder gaan.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=211-226)]

Nu, laten we `initializeSession()` je methode `Start()` aansluiten op je methode.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=81-93&highlight=12)]

Voeg ten slotte de `CreateAndSaveSphere()` volgende code toe aan uw methode. Het zal een lokaal Azure Spatial Anchor koppelen aan de bol die we in de echte wereld plaatsen.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-338,390&highlight=14-25)]

Voordat u verder gaat, moet u een Azure Spatial Anchors-account-id en -sleutel maken als u deze nog niet hebt. Volg de volgende sectie om ze te verkrijgen.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Upload uw lokale anker in de cloud

Zodra u uw Azure Spatial Anchors-account-id `Account Id` en `SpatialAnchorsAccountId` sleutel `Account Key` `SpatialAnchorsAccountKey`hebt, gaat u de in en de in en de in plakken.

Tot slot, laten we alles aan elkaar haken. Voeg `SpawnNewAnchoredObject()` in uw methode de volgende code toe. Het zal `CreateAnchorAsync()` de methode aanroepen zodra jullie bol is gemaakt. Zodra de methode terugkeert, zal de onderstaande code een laatste update uitvoeren voor uw bol, en de kleur veranderen in blauw.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-391&highlight=26-77)]

Voer uw app nogmaals uit **vanuit Visual Studio.** Beweeg rond je hoofd en dan lucht tik om je bol te plaatsen. Zodra we genoeg frames hebben, wordt de bol geel en wordt het uploaden van de cloud gestart. Zodra de upload is voltooid, wordt je bol blauw. Optioneel u ook het venster Uitvoer in **Visual Studio** gebruiken om de logboekberichten te controleren die uw app verzendt. U de aanbevolen functie voor het maken van voortgang en de anker-id die de cloud retourneert zodra de upload is voltooid, bekijken.

> [!NOTE]
> Als u 'DllNotFoundException: Unable to load DLL 'AzureSpatialAnchors': The specified module could not be found', moet u uw oplossing opnieuw **schoonmaken** en **bouwen.**

## <a name="locate-your-cloud-spatial-anchor"></a>Zoek uw ruimtelijk anker in de cloud

Een van uw anker is geüpload naar de cloud, we zijn klaar om te proberen het opnieuw te lokaliseren. Laten we de volgende code `HandleTap()` toevoegen aan uw methode. Deze code zal:

* Oproep `ResetSession()`, die `CloudSpatialAnchorSession` zal stoppen met de en verwijder onze bestaande blauwe bol van het scherm.
* Opnieuw initialiseren. `CloudSpatialAnchorSession` We doen dit zodat we zeker weten dat het anker dat we gaan vinden uit de cloud komt in plaats van het lokale anker te zijn dat we hebben gemaakt.
* Maak een **watcher** die zoekt naar het anker dat we hebben geüpload naar Azure Spatial Anchors.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=267-305&highlight=13-31,35-36)]

Laten we nu `ResetSession()` onze `CleanupObjects()` en methoden toevoegen. U ze hieronder zetten`QueueOnUpdate()`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=119-172)]

We moeten nu de code aansluiten die wordt aangeroepen wanneer het anker waar we naar zoeken zich bevindt. Binnen `InitializeSession()`in , voeg de volgende callbacks:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=200-206&highlight=4-5)]


Nu u code toevoegen die & een groene bol plaatsen zodra de CloudSpatialAnchor zich bevindt. Het zal ook weer op het scherm tikken, zodat u het hele scenario opnieuw herhalen: maak een ander lokaal anker, upload het en zoek het opnieuw.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=228-265)]

Dat is alles. Voer uw app nog een laatste keer uit vanuit **Visual Studio** om het hele scenario van eind tot eind uit te proberen. Beweeg rond uw apparaat en plaats uw witte bol. Houd vervolgens je hoofd in beweging om omgevingsgegevens vast te leggen totdat de bol geel wordt. Je lokale anker wordt geüpload en je bol wordt blauw. Tik tot slot nogmaals op uw scherm, zodat uw lokale anker wordt verwijderd, en vervolgens vragen we naar de tegenhanger van de cloud. Blijf uw apparaat verplaatsen totdat uw ruimtelijk anker in de cloud zich bevindt. Een groene bol moet op de juiste plaats worden weergegeven en u het hele scenario opnieuw & herhalen.

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]