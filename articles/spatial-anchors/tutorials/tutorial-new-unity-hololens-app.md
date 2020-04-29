---
title: 'Zelf studie: een nieuwe app voor het definiëren van een HoloLens-eenheid maken'
description: In deze zelf studie leert u hoe u een nieuwe app voor het maken van een HoloLens-eenheid maakt met behulp van Azure spatiale ankers.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 07/05/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: e1abb759c80e770f1e650c232b6b2e21232b7e6f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75457717"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>Zelf studie: stapsgewijze instructies voor het maken van een nieuwe app voor het definiëren van een HoloLens-eenheid met behulp van Azure spatiale ankers

In deze zelf studie leert u hoe u een nieuwe app voor het maken van een HoloLens-eenheid maakt met behulp van Azure spatiale ankers.

## <a name="prerequisites"></a>Vereisten

Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien:

1. Een Windows-machine met <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017 +</a> geïnstalleerd met de **universeel Windows-platform Development** -werk belasting en het onderdeel **Windows 10 SDK (10.0.18362.0 of hoger)** en <a href="https://git-scm.com/download/win" target="_blank">git voor Windows</a>.
2. De [C++/WinRT Visual Studio Extension (VSIX)](https://aka.ms/cppwinrt/vsix) voor Visual Studio moet worden geïnstalleerd vanuit de [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
3. Een HoloLens-apparaat waarvoor de [ontwikkelaarsmodus](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) is ingeschakeld. Dit artikel vereist een HoloLens-apparaat met de [Update voor Windows van 10 oktober 2018](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (ook wel bekend als RS5). Als u wilt bijwerken naar de nieuwste release op HoloLens, opent u de app **Instellingen**, gaat u naar **Bijwerken en beveiliging** en selecteert u vervolgens de knop **Controleren op updates**.

## <a name="getting-started"></a>Aan de slag

We gaan eerst ons project en de eenheids scène instellen:
1. Begin eenheid.
2. Selecteer **Nieuw**.
4. Zorg ervoor dat **3D** is geselecteerd.
5. Geef uw project een naam en voer een opslag **locatie**in.
6. Klik op **project maken**.
7. Sla de lege standaard scène op in een nieuw bestand met behulp van: **bestand** > **Opslaan als**.
8. Geef het nieuwe **hoofd** van de scène een naam en klik op de knop **Opslaan** .

**De project instellingen instellen**

We gaan nu enkele eenheids project instellingen instellen die ons helpen bij het richten op de Windows Holographic SDK voor ontwikkeling.

U kunt eerst kwaliteits instellingen voor onze toepassing instellen.
1. Selecteer de**kwaliteit** **project instellingen** >  **bewerken** > 
2. Klik in de kolom onder het logo van **Windows Store** op de pijl op de **standaard** rij en selecteer **zeer laag**. U weet dat de instelling correct wordt toegepast wanneer het vakje in de kolom **Windows Store** en **zeer kleine** rijen groen is.

We moeten unit laten weten dat de app die we willen exporteren, een detail weergave moet maken in plaats van een 2D-weer gave. We maken een detail weergave door ondersteuning van Virtual Reality in te scha kelen op Unity die is gericht op de Windows 10-SDK.

1. Ga naar de**project instellingen** >  **bewerken** > **Player**.
2. Selecteer in het **deel venster Inspector** voor instellingen van de **speler**het pictogram **Windows Store** .
3. Vouw de **XR-instellingen** groep uit.
4. Schakel in de sectie **rendering** het selectie vakje **ondersteund Virtual Reality** in om een nieuwe lijst **van de Virtual Reality-SDK** toe te voegen.
5. Controleer of **Windows Mixed Reality** wordt weer gegeven in de lijst. Als dat niet het geval **+** is, selecteert u de knop aan de onderkant van de lijst en kiest u **Windows Mixed Reality**.

> [!NOTE]
> Als u het pictogram van de Windows Store niet ziet, controleert u of u de back-end van de Windows Store .NET-scripting hebt geselecteerd voordat u de installatie uitvoert. Als dat niet het geval is, moet u de eenheid mogelijk opnieuw installeren met de juiste Windows-installatie.

**Configuratie van back-end voor scripts controleren**
1. Ga naar **Edit** > **project settings** > **Player** (mogelijk hebt u nog steeds vanuit de vorige stap een **speler** geopend).
2. Selecteer in het **deel venster Inspector** voor instellingen van de **speler**het pictogram **Windows Store** .
3. Zorg er in de sectie configuratie van **andere instellingen** voor dat **Scripting back-end** is ingesteld op **IL2CPP**.

**Mogelijkheden instellen**
1. Ga naar **Edit** > **project settings** > **Player** (mogelijk hebt u nog steeds vanuit de vorige stap een **speler** geopend).
2. Selecteer in het **deel venster Inspector** voor instellingen van de **speler**het pictogram **Windows Store** .
3. Controleer in de sectie configuratie van **publicatie-instellingen** **InternetClientServer** en **SpatialPerception**.

**De virtuele hoofd camera instellen**
1. Selecteer **hoofd camera**in het **deel venster hiërarchie**.
2. Stel in de **Inspector**de positie van de trans formatie in op **0, 0, 0**.
3. Zoek de eigenschap **Clear Flags** en wijzig de vervolg keuzelijst van **Skybox** in **effen Color**.
4. Klik op het veld **achtergrond** om een kleuren kiezer te openen.
5. Stel **R, G, B en A** in op **0**.
6. Selecteer **onderdeel toevoegen** en zoeken en toevoegen van de Strijdere **spatiale toewijzing**.

**Ons script maken**
1. Maak in het deel venster **project** een nieuwe map, **scripts**, onder de map **assets** .
2. Klik met de rechter muisknop op de map en selecteer vervolgens **Create >**, **C# script**. Titel it- **AzureSpatialAnchorsScript**.
3. Ga naar **GameObject** -> **leeg maken**.
4. Selecteer de functie en wijzig de **naam van** **GameObject** in **MixedRealityCloud**. Selecteer **onderdeel toevoegen** en zoeken naar en voeg het **AzureSpatialAnchorsScript**toe.

**De Sphere prefab maken**
1. Ga naar **GameObject** -> **3D-object** -> **bol**.
2. Stel in de **Inspector**de schaal in op **0,25, 0,25, 0,25**.
3. Zoek het object **Sphere** in het deel venster **hiërarchie** . Klik erop en sleep deze naar de map **assets** in het deel venster **project** .
4. Klik met de rechter muisknop en **Verwijder** de oorspronkelijke bol die u hebt gemaakt in het deel venster **hiërarchie** .

U hebt nu een Sphere prefab in uw **project** venster.

## <a name="trying-it-out"></a>Probeer het uit
Als u wilt testen of alles werkt, bouwt u uw app op **eenheid** en implementeert u deze vanuit **Visual Studio**. Volg hoofd stuk 6 van de [ **Mr-basis beginselen 100: aan de slag met eenheids** cursus](https://docs.microsoft.com/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio) . U ziet nu het scherm unit-start en vervolgens een duidelijke weer gave.

## <a name="place-an-object-in-the-real-world"></a>Plaats een object in de praktijk
Laten we een object maken & deze met uw app te plaatsen. Open de Visual Studio-oplossing die we hebben gemaakt toen [onze app werd geïmplementeerd](#trying-it-out).

Voeg eerst de volgende invoer toe aan uw `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

Voeg vervolgens de volgende leden variabelen toe aan uw `AzureSpatialAnchorsScript` klasse:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-42,48-52,60-79)]

Voordat we verdergaan, moeten we het Sphere prefab instellen dat we hebben gemaakt op basis van de spherePrefab-lidvariabele. Ga terug naar **Unity**.
1. Selecteer in **eenheid**het object **MixedRealityCloud** in het deel venster **hiërarchie** .
2. Klik op het **Sphere** -prefab dat u hebt opgeslagen in het deel venster **project** . Sleep de **bol** waarop u hebt geklikt in het gebied **prefab** onder **Azure spatiale-ankers script (script)** in het deel venster **Inspector** .

Nu moet de **bol** zijn ingesteld als de prefab in uw script. Bouw van **Unity** en open vervolgens de resulterende **Visual Studio** -oplossing opnieuw, zoals u zojuist hebt [geprobeerd](#trying-it-out).

Open `AzureSpatialAnchorsScript.cs` opnieuw in **Visual Studio**. Voeg de volgende code toe aan `Start()` de-methode. Met deze code wordt een `GestureRecognizer`verbinding gemaakt, die detecteert of er een lucht kraan en `HandleTap`-aanroep wordt gedetecteerd.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=81-90,93&highlight=4-10)]

We moeten nu de volgende `HandleTap()` methode toevoegen. `Update()` Er wordt een Ray-cast uitgevoerd en er wordt een raak punt opgehaald waarop een bol moet worden geplaatst.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=267-277,299-300,304-312)]

We moeten de bol nu maken. De bol is in eerste instantie wit, maar deze waarde wordt later aangepast. Voeg de volgende `CreateAndSaveSphere()` methode toe:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-325,390)]

Voer uw app uit vanuit **Visual Studio** om deze opnieuw te valideren. Tik op het scherm om het te maken & plaats uw witte bol op het gewenste Opper vlak.

## <a name="set-up-the-dispatcher-pattern"></a>Het dispatcher-patroon instellen

Wanneer u werkt met Unity, moeten alle unit-Api's, bijvoorbeeld Api's die u gebruikt voor het uitvoeren van UI-updates, plaatsvinden op de hoofd thread. In de code gaan we echter retour aanroepen op andere threads ophalen. We willen de gebruikers interface bijwerken in deze retour aanroepen, dus we hebben een manier nodig om van een side thread naar de hoofd thread te gaan. Als u code wilt uitvoeren op de hoofd thread vanuit een side thread, gebruiken we het dispatcher-patroon.

We gaan een lidvariabele toevoegen, dispatchQueue, een wachtrij met acties. We gaan acties naar de wachtrij pushen en vervolgens de acties in de hoofd thread vervolgens uit de wachtrij verwijderen en uitvoeren.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=38-51&highlight=6-9)]

Vervolgens voegen we een manier toe om een actie aan de wachtrij toe te voegen. Direct `QueueOnUpdate()` toevoegen na `Update()` :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=107-117)]

We gaan nu de lus update () gebruiken om te controleren of er een actie in de wachtrij is geplaatst. Als dit het geval is, wordt de actie in de wachtrij geplaatst en wordt uitgevoerd.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=95-105&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>De Azure spatiale-ankers SDK ophalen

## <a name="via-unity-package"></a>[Via Unity package](#tab/UnityPackage)

De Azure spatiale-ankers SDK worden nu gedownload. Ga naar de [pagina met github-releases van Azure spatiale ankers](https://github.com/Azure/azure-spatial-anchors-samples/releases). Onder assets, down load de **AzureSpatialAnchors. unitypackage**. Ga in Unity naar **assets**en klik op **pakket** > importeren**aangepast pakket...**. Navigeer naar het pakket en selecteer **openen**.

Schakel in het venster nieuwe **eenheid import unit** de optie **plugins** uit en klik in de rechter benedenhoek op **importeren** .

We moeten nu Nuget-pakketten herstellen om de Azure spatiale ankers SDK te verkrijgen. Bouw van **Unity** en open en bouw de resulterende **Visual Studio** -oplossing opnieuw, zoals beschreven in het [uitproberen](#trying-it-out).

## <a name="via-nugetforunity"></a>[Via NuGetForUnity](#tab/NuGetForUnity)

Eerst moet u NuGetForUnity installeren. Ga naar de [pagina met NuGetForUnity github-releases](https://github.com/GlitchEnzo/NuGetForUnity/releases). Onder assets, down load de laatste **NuGetForUnity. unitypackage**. Ga in Unity naar **assets**en klik op **pakket** > importeren**aangepast pakket...**. Navigeer naar het pakket en selecteer **openen**. Unit-eenheid zal nu NugetForUnity installeren. Als u geen nieuwe **NuGet** -vervolg keuzelijst in unit ziet, moet u mogelijk met de rechter muisknop op **project** > **assets**klikken. Selecteer vervolgens **alles opnieuw importeren**.

Nadat u NuGetForUnity hebt geïnstalleerd, selecteert u **NuGet** > **NuGet-pakketten beheren**. Zoek vervolgens naar micro soft. Azure. SpatialAnchors. Unity en selecteer **installeren**.

We moeten nu bouwen om de daad werkelijke Azure spatiale-ankers SDK te verkrijgen, omdat het NuGet-pakket dat we zojuist hebben gedownload, alleen nog hulp scripts bevat. Bouw van **Unity** en open en bouw de resulterende **Visual Studio** -oplossing opnieuw, zoals beschreven in het [uitproberen](#trying-it-out).

---

Voeg in uw **Visual Studio** -oplossing de volgende import bewerking toe `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs`aan uw:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=18-21&highlight=1)]

Voeg vervolgens de volgende lidvariabelen toe aan uw `AzureSpatialAnchorsScript` klasse:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=48-63&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>Een lokaal ruimtelijke Azure-anker koppelen aan het lokale anker

Laten we de CloudSpatialAnchorSession van het ruimtelijke-anker van Azure instellen. We beginnen met het toevoegen van de `InitializeSession()` volgende methode in `AzureSpatialAnchorsScript` uw klasse. Zodra de app is aangeroepen, zorgt deze ervoor dat er een Azure spatiale-anker sessie wordt gemaakt en op de juiste wijze wordt geïnitialiseerd tijdens het opstarten van uw toepassing.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=174-202,205-209)]

We moeten nu code schrijven voor het afhandelen van gemachtigden-aanroepen. We voegen er meer aan toe wanneer we verdergaan.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=211-226)]

Nu gaan we uw `initializeSession()` methode aan uw `Start()` methode koppelen.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=81-93&highlight=12)]

Voeg tot slot de volgende code toe aan `CreateAndSaveSphere()` de methode. Er wordt een lokaal ruimtelijk anker van Azure aangesloten op de bol die we in de praktijk plaatsen.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-338,390&highlight=14-25)]

Voordat u verder gaat, moet u een Azure spatiale-ankers account-id en-sleutel maken, als u deze nog niet hebt. Volg de volgende sectie om ze te verkrijgen.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Uw lokale anker uploaden naar de Cloud

Wanneer u uw Azure spatiale ankers account-id en-sleutel hebt, gaat `Account Id` u `SpatialAnchorsAccountId` naar en `Account Key` plakt `SpatialAnchorsAccountKey`u deze in en in.

Ten slotte gaan we alles aansluiten. Voeg de `SpawnNewAnchoredObject()` volgende code toe in de-methode. De `CreateAnchorAsync()` methode wordt aangeroepen zodra uw Sphere is gemaakt. Zodra de methode is geretourneerd, voert de volgende code een laatste update uit op uw Sphere, waardoor de kleur wordt gewijzigd in blauw.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-391&highlight=26-77)]

Voer uw app uit vanuit **Visual Studio** . Beweeg uw kop en tik vervolgens op de buurt om uw bol te plaatsen. Zodra er voldoende frames zijn, verandert de bol in geel en wordt het uploaden van de Cloud gestart. Zodra het uploaden is voltooid, wordt uw bol blauw. U kunt eventueel ook het uitvoer venster in **Visual Studio** gebruiken om de logboek berichten te bewaken die uw app verzendt. U kunt de aanbevolen voortgang van het maken en de anker-id die de Cloud retourneert, bekijken zodra de upload is voltooid.

> [!NOTE]
> Als u ' DllNotFoundException: kan DLL-bestand ' AzureSpatialAnchors ' niet laden: de opgegeven module is niet gevonden. ', moet u de oplossing **schoonmaken** en opnieuw **bouwen** .

## <a name="locate-your-cloud-spatial-anchor"></a>Het ruimtelijke fixeer punt in de Cloud zoeken

Er wordt een anker naar de Cloud geüpload, zodat u het opnieuw kunt proberen. Laten we de volgende code toevoegen aan uw `HandleTap()` -methode. Deze code gaat als volgt:

* Aanroep `ResetSession()`, waardoor de `CloudSpatialAnchorSession` bestaande blauwe bol op het scherm wordt gestopt en verwijderd.
* Initialiseer `CloudSpatialAnchorSession` opnieuw. We doen dit zodat het anker dat we gaan vinden afkomstig is uit de cloud in plaats van het lokale anker dat we hebben gemaakt.
* Maak een **Watcher** waarmee wordt gezocht naar het anker dat is geüpload naar de ruimtelijke ankers van Azure.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=267-305&highlight=13-31,35-36)]

We voegen nu onze `ResetSession()` en `CleanupObjects()` -methoden toe. U kunt deze hieronder plaatsen`QueueOnUpdate()`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=119-172)]

We moeten nu de code die wordt aangeroepen wanneer het anker voor het uitvoeren van een query wordt gevonden. Voeg in `InitializeSession()`van de volgende retour aanroepen toe:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=200-206&highlight=4-5)]


Met kunt u nu code toevoegen waarmee & een groene bol maakt wanneer de CloudSpatialAnchor zich bevindt. U kunt ook het hele scenario nog een keer herhalen: Maak een ander lokaal anker, upload het en zoek het opnieuw.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=228-265)]

Dat is alles. Voer de app een laatste keer uit in **Visual Studio** om het hele scenario end-to-end uit te proberen. Ga op het apparaat door en plaats uw witte bol. Blijf vervolgens uw kop verplaatsen om omgevings gegevens vast te leggen totdat de bol geel draait. Uw lokale anker wordt geüpload en uw Sphere wordt blauw. Tik ten slotte nogmaals op uw scherm, zodat uw lokale anker wordt verwijderd en zoek vervolgens naar de Cloud equivalent. Blijf door gaan met het verplaatsen van uw apparaat totdat het ruimtelijke bewaarde in de Cloud zich bevindt. Een groene bol moet op de juiste locatie worden weer gegeven en u kunt afspoelen & het hele scenario opnieuw te herhalen.

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]