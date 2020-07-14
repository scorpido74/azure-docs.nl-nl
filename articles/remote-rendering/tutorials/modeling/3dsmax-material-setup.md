---
title: Fysiek gebaseerde renderingmaterialen instellen in 3DSMax
description: In deze zelfstudie wordt uitgelegd hoe u fysiek gebaseerde renderingmaterialen instelt in 3DSMax en deze exporteert in een FBX-indeling.
author: muxanickms
ms.author: misams
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: df4be8963c93199f9fad23ab3f709f691e1da768
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85857519"
---
# <a name="tutorial-set-up-physically-based-rendering-materials-in-3d-studio-max"></a>Zelfstudie: Fysiek gebaseerde renderingmaterialen instellen in 3D Studio Max

## <a name="overview"></a>Overzicht
In deze zelfstudie leert u het volgende:

>[!div class="checklist"]
>
> * Materialen met geavanceerde belichting toewijzen aan objecten in de scène.
> * Objecten en materialen ruimtelijk weergeven.
> * Een scène exporteren naar een FBX-indeling en belangrijke opties selecteren.

Het maken van [fysiek gebaseerde rendering-materialen (PBR)](../../overview/features/pbr-materials.md) in 3D Studio Max (3DSMax) is een relatief eenvoudige taak. Het is in veel opzichten vergelijkbaar met het instellen van PBR in andere apps voor het maken van inhoud, zoals Maya. Deze zelfstudie is een handleiding voor de installatie van een eenvoudige PBR-shader en FBX-export voor Azure Remote Rendering-projecten.

De voorbeeldscène in deze zelfstudie bevat een aantal polygone box-objecten. Er worden verschillende materialen aan toegewezen, zoals hout, metaal, geverfd metaal, plastic en rubber. In brede zin bevat elk materiaal alle of de meeste van de volgende patronen:

* **Albedo**, de kleurkaart van de materialen, ook wel **Diffuus** of **BaseColor** genoemd.
* **Metaligheid**, bepaalt of een materiaal van metaal is en welke delen van metaal zijn. 
* **Ruwheid**, bepaalt hoe ruw of glad een oppervlak is.
Dit is ook van invloed op de scherpte of wazigheid van de reflecties en glanspunten op een oppervlak.
* **Normaal**, wat details toevoegt aan een oppervlak zonder dat er noodzakelijkerwijs meer polygonen worden toegevoegd. Voorbeelden van details kunnen putjes en deukjes in een metalen oppervlak of de nerf in hout zijn.
* **Omgevingsocclusie**, wordt gebruikt om zachte schaduwen en contactschaduwen aan een model toe te voegen. Het is een kaart in grijstinten die aangeeft welke gebieden van een model volledige belichting (wit) of volledige schaduw (zwart) krijgen.

## <a name="prepare-the-scene"></a>De scène voorbereiden
In **3D Studio Max** gaat het instellen van PBR-materiaal als volgt.

Zoals te zien is in de voorbeeldscène, hebben we een aantal kubusobjecten gemaakt die elk een ander type materiaal vertegenwoordigen:

>[!TIP]
>Voordat we beginnen met het maken van assets voor ARR, is het de moeite waard te vermelden dat er wordt gemeten in **meters**.  
>Daarom is het raadzaam om de **systeemeenheden** voor uw scène in te stellen op **meters**. Ook is het bij het exporteren raadzaam om eenheden in te stellen op meters in de FBX-exportinstellingen.

In de volgende afbeelding ziet u de stappen voor het instellen van de systeemeenheden op meters in 3D Studio Max. Ga in het hoofdmenu naar **Aanpassen** > **Eenheden instellen** > **Systeemeenheden instellen** en klik in de vervolgkeuzelijst **Systeemeenhedenschaal** op **Meters**. 
![systeemeenheden](media/3dsmax/system-units.jpg)

Als de systeemeenheden zijn ingesteld op meters, kunnen we onze modellen gaan maken. In onze voorbeeldscène maken we diverse vakobjecten die elk een ander materiaaltype vertegenwoordigen, bijvoorbeeld metaal, rubber, plastic enzovoort. 

>[!TIP]
>Het is raadzaam om bij het maken van assets deze assets een geschikte naam te geven. Zo kunt u ze later gemakkelijker vinden als de scène veel objecten bevat

![rename-objects](media/3dsmax/rename-objects.jpg)

## <a name="assign-materials"></a>Materialen toewijzen

Als er objecten zijn gemaakt in onze scène, in dit geval een aantal kubussen, kunnen we starten met het instellen van PBR:

* Klik in de hoofdwerkbalk op het pictogram van de **materiaaleditor**, zoals wordt weergegeven in de volgende afbeelding. U kunt ook op **M** op het toetsenbord drukken om de editor te openen. De materiaaleditor heeft twee modi die kunnen worden geselecteerd in de vervolgkeuzelijst **Modi**: de modus voor de **compacte materiaaleditor** en de modus voor **slate-materiaal**. Omdat deze scène betrekkelijk eenvoudig is, gebruiken we de **compacte modus**.

* In de materiaaleditor ziet u een aantal bollen: deze bollen zijn onze materialen. Aan elk object (vak) in de scène wordt één materiaal toegewezen. Voor het toewijzen selecteert u om te beginnen een van de objecten in de hoofd-viewport. Zodra deze selectie is gemaakt, klikt u op de eerste bol in het venster van de materiaaleditor. Zodra deze bol is toegewezen aan een object, wordt het geselecteerde materiaal gemarkeerd, zoals in de volgende afbeelding wordt weergegeven.

* Klik op de knop **Materiaal aan selectie toewijzen** zoals wordt weergegeven. Het geselecteerde materiaal is nu toegewezen aan het geselecteerde object.
![assign-material](media/3dsmax/assign-material.jpg)

In de materiaaleditor kunt u typen materialen selecteren (de keus is enorm), afhankelijk van uw gebruikscase. Normaal gesproken wordt het type materiaal standaard ingesteld op **Standaard**. Dit materiaal is een basismateriaal dat niet geschikt is voor het instellen van PBR. Daarom moeten we het materiaaltype wijzigen in een PBR-materiaal. Het favoriete **3DSMax**-materiaal voor de Azure Remote Rendering-projecten is het **fysieke materiaal**.

* Klik in de materiaaleditor op het tabblad **Standaard** en selecteer in de materiaal-/kaartbrowser die wordt geopend **Fysiek materiaal**. Met deze actie wordt het toegewezen **standaard**materiaal geconverteerd naar een **fysiek PBR-materiaal**.
![physical-material](media/3dsmax/physical-material.jpg)

* In de materiaaleditor ziet u nu de eigenschappen van het fysieke materiaal (zie hieronder) en kunnen we patronen aan de asset gaan toewijzen.
![textures-list](media/3dsmax/textures-list.jpg)

Zoals u kunt zien in de bovenstaande afbeelding, zijn er vele kaarten en patronen die aan het materiaal kunnen worden toegevoegd. In dit geval gaan we echter slechts vijf patronen in het materiaal gebruiken.

>[!TIP]
>Het is een goed idee om uw materialen een handige naam te geven, zoals wordt weergegeven in de bovenstaande afbeelding.

We kunnen nu beginnen met het toewijzen van patronen aan ons materiaal. Hoe u patronen genereert, kan variëren afhankelijk van uw voorkeuren of zelfs afhankelijk zijn van het gebruik. Zo kunt u bijvoorbeeld tegelpatronen gebruiken die kunnen worden toegepast op elke asset, of mogelijk moeten specifieke onderdelen van een project/asset worden voorzien van een eigen, aangepaste set patronen. U kunt gebruikmaken van algemene tegelpatronen die u online verkrijgt, of u kunt ze zelf maken in apps zoals **Photoshop**, **Quixel Suite**, **Substance Suite**, enzovoort. 

Voordat we de patronen gaan toewijzen, moet u nadenken over de assetpatrooncoördinaten (UVW). Hoewel het bij het toepassen van patronen op een model best practice is om ervoor te zorgen dat het model is uitgepakt (patronen worden niet goed weergegeven zonder op de juiste manier te zijn uitgepakt), is het in dit geval belangrijk als we van plan zijn om een **omgevingsocclusiekaart** te gebruiken voor het model. Waar **Stingray Shader** beschikbaar is in **Maya**, is **Fysiek materiaal** beschikbaar in **3DSMax**, maar dit programma beschikt niet over een speciaal patroonslot voor **omgevingsocclusie**. Daarom wordt de AO-kaart toegepast op een ander slot en zorgen we ervoor dat deze afzonderlijk van de andere patronen kan worden gebruikt (zoals tegelpatronen). Hiervoor wijzen we deze aan een eigen UVW-kaartkanaal toe. 

We beginnen met het toewijzen van een **UVW-aanpassingsfunctie voor uitpakken** aan ons model, zoals hieronder wordt weergegeven:

* Klik in de eigenschappeneditor voor geselecteerde objecten op de lijst van de aanpassingsfunctie. Schuif dan in de vervolgkeuzelijst die wordt geopend omlaag en selecteer UVW uitpakken. Met deze actie wordt een UVW-aanpassingsfunctie voor uitpakken toegepast op onze asset.
![unwrap-modifier](media/3dsmax/unwrap-modifier.jpg)

* Het kaartkanaal is ingesteld op één. In kaartkanaal één wordt meestal het uitpakken uitgevoerd. In ons geval is het object uitgepakt zonder overlappende patrooncoördinaten (UV).
![unwrapped-uvw](media/3dsmax/unwrapped-uvw.jpg)

De volgende stap is het maken van een tweede UV-kaartkanaal.

* Sluit de UV-editor als deze is geopend en wijzig in het kanaalgedeelte van het menu **UV's bewerken** het kanaalnummer in twee. Kaartkanaal 2 is het verwachte kanaal voor omgevingsocclusiekaarten. 

* In het dialoogvenster **Waarschuwing over kanaalwijziging** dat wordt geopend, krijgt u de mogelijkheid om de bestaande UV's in kanaal 1 te **verplaatsen** naar het nieuwe kanaal 2 of om de bestaande UV's te **verlaten**, waardoor er automatisch een nieuwe **UV-uitpaktaak** wordt gemaakt. Selecteer **Verlaten** alleen als u van plan bent om een nieuwe **UV-uitpaktaak** te maken voor de omgevingsocclusiekaart die verschilt van de UV's in kaartkanaal 1 (bijvoorbeeld als u tegelpatronen wilt gebruiken in kanaal 1). In dit geval gaan we de UV's **verplaatsen** van kanaal 1 naar kanaal 2, omdat het nieuwe UV-kanaal niet hoeft te worden bewerkt.

>[!NOTE]
>Zelfs als u de UV-uitpaktaak hebt gekopieerd (**verplaatst**) van kaartkanaal 1 naar kaartkanaal 2, kunt u nog wijzigingen aanbrengen aan de nieuwe kanaal-UV's zonder dat dit van invloed is op het oorspronkelijke kaartkanaal.

![channel-change](media/3dsmax/channel-change.jpg)

Wanneer het nieuwe kaartkanaal is gemaakt, kunnen we teruggaan naar het fysieke materiaal in de materiaaleditor en aan de slag gaan met het toevoegen van onze patronen. Eerst voegen we de omgevingsocclusie (**AO**) toe, omdat er nog een stap moet worden uitgevoerd om ervoor te zorgen dat deze goed werkt. Zodra de AO-kaart is gekoppeld aan ons materiaal, moeten we de kaart instrueren om kanaal 2 te gebruiken.

* Zoals eerder vermeld, is er geen toegewezen slot voor AO-kaarten in het **fysieke materiaal van 3DSMax**. In plaats daarvan wordt de AO-kaart toegepast op het slot **Diffuse ruwheid**.

* Klik in de lijst **Algemene kaarten** van het fysieke materiaal op **Diffuse ruwheid** bij het slot **Geen kaart** en laad uw AO-kaart.

* In de eigenschappen van het AO-patroon ziet u dat het kaartkanaal standaard is ingesteld op **1**. Wijzig deze waarde in **2**. Met deze actie worden de stappen voltooid die nodig zijn om uw omgevingsocclusiekaart toe te voegen.

>[!IMPORTANT]
>Dit is een belangrijke stap, met name als uw UV's in kanaal 2 verschillen van die in kanaal 1, omdat de AO niet correct in kaart wordt gebracht als het onjuiste kanaal is geselecteerd.

![assign-ao-map](media/3dsmax/assign-ao-map.jpg)

We gaan nu de normale kaart toewijzen aan het PBR-materiaal. Deze actie verschilt van de actie in **Maya** omdat normal-map niet rechtstreeks wordt toegepast op de bumpkaartslot (er is geen slot normal-map in het **fysieke materiaal van 3DSMax**). In plaats daarvan wordt de kaart toegevoegd aan een normal-map-modifier die op zijn beurt wordt gekoppeld aan het slot **normals**.

* Klik in het gedeelte **Speciale kaarten** van de eigenschappen van het fysieke materiaal (in de materiaaleditor) op **Bumpkaart** bij het slot **Geen kaart**. 

* Zoek in de materialen-/kaartbrowser naar de optie **Normale bump** en klik hierop. Met deze actie wordt een **normale bump**-aanpassing aan ons materiaal toegevoegd.

* Klik in de **normale bump**-aanpassing op **Normaal** bij **Geen kaart** en zoek en laad dan uw normale kaart (normal-map).

* Controleer of de methode is ingesteld op **Taaklijn** (dit moet standaard het geval zijn) en stel indien nodig de schakelaar in op **groen (Y)** .

![normal-bump](media/3dsmax/normal-bump.jpg)
![load-normal-map](media/3dsmax/load-normal-map.jpg)

Als normal-map goed wordt toegewezen, kunnen we verder met het toewijzen van de resterende patronen om het instellen van het fysieke materiaal te voltooien. Dit proces is eenvoudig; er hoeft geen rekening te worden gehouden met speciale instellingen. In de volgende afbeelding ziet u de volledige set met patronen die aan ons materiaal zijn toegewezen: ![all-textures](media/3dsmax/all-textures.jpg)

Als uw PBR-materialen zijn gemaakt en ingesteld, is het de moeite waard om na te denken over het instantiëren van objecten in uw scène. Het instantiëren van vergelijkbare objecten in uw scène, zoals moeren, bouten en schroeven, sluitringen en alle objecten die hetzelfde zijn, kan aanzienlijke besparingen opleveren wat betreft de bestandsgrootte. Instanties van een masterobject kunnen hun eigen schaal, draaiing en transformaties krijgen, zodat deze naar behoefte in uw scène kunnen worden geplaatst. In **3D Studio Max** is het **instantiëringsproces** eenvoudig.

* Selecteer in de hoofd-viewport het object of de objecten die u wilt exporteren.

* Houd **Shift** ingedrukt en versleep de assets omhoog met het hulpprogramma Transformeren (verplaatsen) 

* In het dialoogvenster **Opties klonen** dat wordt geopend, stelt u **Object** in op **Instantie** en klikt u op **OK**. 
![instance-object](media/3dsmax/instance-object.jpg)

Met deze actie wordt een instantie van uw object gemaakt dat onafhankelijk van het bovenliggende item en andere instanties van dat bovenliggende item kan worden gedraaid of geschaald.

>[!IMPORTANT]
>Wijzigingen die u in de sub-objectmodus aanbrengt in een instantie worden echter verzonden naar alle instanties van uw object. Als u dus werkt met geïnstantieerde objectonderdelen, zoals hoekpunten en polygoonvlakken, moet u zich ervan bewust zijn dat de wijzigingen die u aanbrengt, van invloed zijn op al deze instanties. Houd er rekening mee dat elk geïnstantieerd object op elk gewenst moment kan worden veranderd in een uniek object. 

>[!TIP]
>De best practice voor instantiëren in uw scène is om deze instanties gaandeweg te maken, omdat het vervangen van **kopieën** door geïnstantieerde objecten later bijzonder moeilijk is. 

Een van de laatste dingen die u moet overwegen voordat we verdergaan met het exportproces is hoe u uw scène/asset kunt inpakken voor delen. In het ideale geval is het zo dat als u de asset doorgeeft aan een klant of een teamlid, u hen de assets wilt kunnen laten openen en weergeven met een minimale hoeveelheid rompslomp. Het is dan ook belangrijk dat u de patroonpaden van uw assets afstemt op het scènebestand. Als de patroonpaden voor uw asset verwijzen naar een lokaal station of een absoluut pad/absolute locatie, worden ze niet geladen in de scène als deze wordt geopend op een andere computer, zelfs niet als het **.max**-bestand in dezelfde map staat als de patronen. Als u de patroonpaden relatief maakt in 3D Studio Max, wordt dit probleem redelijk eenvoudig opgelost.

* Ga in de hoofdwerkbalk naar **Bestand** > **Verwijzing** > **Assets bijhouden**. 

* In de browser voor het bijhouden van assets die wordt geopend, ziet u (bijna) alle patronen die u hebt toegepast op uw PBR-materialen die worden vermeld in de kolom **Kaarten/shaders**.

* Hiernaast, in de kolom **Volledig pad**, ziet u het bestandspad voor de locatie van de patronen (waarschijnlijk de locatie op uw lokale computer).

* Ten slotte ziet u een kolom met de naam **Status**. In deze kolom wordt aangegeven of een bepaald patroon is gevonden en toegepast op uw scène, en wordt dit patroon gemarkeerd met een van de volgende termen: **OK**, **Gevonden** of **Bestand ontbreekt**. De eerste twee geven aan dat het bestand is gevonden en geladen, terwijl de laatste betekent dat de tracker een bestand niet heeft gevonden.
![texture-paths](media/3dsmax/texture-paths.jpg)

U zult merken dat niet alle patronen worden weergegeven in de assettracker wanneer u deze voor het eerst opent. U hoeft zich nergens zorgen over te maken, omdat u meestal alle scènepatronen wel kunt vinden als u het proces voor het vinden van paden een of twee keer uitvoert. Het proces voor het zoeken van paden is als volgt: 

* In het asset-trackervenster houdt u **Shift** ingedrukt en +**klikt** u op het bovenste patroon in de lijst **Kaarten/shaders**. Houd Shift ingedrukt en klik op het laatste patroon in de lijst. Met deze actie worden alle patronen in de lijst geselecteerd. De geselecteerde patronen worden nu in het blauw gemarkeerd (zie de bovenstaande afbeelding).

* Klik met de rechtermuisknop op de selectie en selecteer in het pop-upmenu dat wordt geopend **Pad instellen**.

* Selecteer in het vak **Assetpad opgeven** dat wordt geopend het lokale pad naar de weergegeven patronen. Vervang het pad door het volgende `.\` en klik op **OK**. 

* Na een bepaalde periode (die afhankelijk is van het aantal patronen in uw scène en hoe groot uw scène is), moet de asset-tracker alles als volgt omzetten (zie de afbeelding).
![resolve-textures](media/3dsmax/resolve-textures.jpg)

De kolom **Volledig pad** is nu leeg. Dit betekent dat de scène niet langer zoekt naar de relevante patronen op een specifieke (absolute) locatie, maar dat deze ze altijd vindt als het max-bestand of het bijbehorende FBX-bestand zich in dezelfde map bevindt als de patronen. 

>[!NOTE]
>Het kan gebeuren dat u dit proces een paar keer moet herhalen om alle patronen en paden te vinden en om te zetten. Daar is niets te doen. Herhaal deze procedure gewoon totdat alle relevante assets zijn verwerkt. Het kan ook gebeuren dat sommige bestanden niet meer te vinden zijn. In dit geval selecteert u alle assets in de lijst en klikt u op **Ontbrekende paden verwijderen** (zie de afbeelding hierboven)

## <a name="fbx-export"></a>FBX-export

Wanneer het traceren van assets is voltooid, kunnen we doorgaan met de FBX-export. Dit proces is eenvoudig en kan op verschillende manieren worden uitgevoerd. 

>[!TIP]
>Tenzij u uw hele scène wilt exporteren, is het raadzaam alleen de assets te selecteren die nodig zijn. Bij scènes waarvoor veel resources nodig zijn, kan het exporteren lang duren. Het is dus logisch om alleen te exporteren wat u nodig hebt
>
>Als u modifiers hebt gebruikt zoals **Turbosmooth** of **Open SubDiv**, is het raadzaam deze samen te vouwen voordat u exporteert, omdat deze tijdens het exporteren tot problemen kunnen leiden. Sla uw scène altijd op voordat u dit doet. 

* Selecteer in de scène de assets die u wilt exporteren en ga in de hoofdwerkbalk naar **Bestand** > **Exporteren** > **Geselecteerde export**

* Typ of selecteer in het dialoogvenster **Bestand selecteren om te exporteren** de naam van het uitvoerbestand en selecteer in de opties **Opslaan als type** **Autodesk (*.fbx)** . Met deze actie wordt het FBX-exportmenu geopend. 

* Houd er rekening mee dat, als u instanties in uw scène hebt gemaakt, het belangrijk is dat **Instanties behouden** wordt ingeschakeld in de exportinstellingen voor FBX. 
![fbx-export](media/3dsmax/fbx-export.jpg)

Er is eerder gezegd dat er verschillende manieren zijn om een bestand te exporteren. Als het de bedoeling is dat de FBX wordt gedeeld met de patroonbestanden in een map, moeten de instellingen in de onderstaande afbeelding worden toegepast en goed werken. Wanneer u de instellingen hebt geselecteerd, klikt u op **OK**.
![fbx-settings](media/3dsmax/fbx-settings.jpg)

Als u echter liever geen grote mappen met patronen met de FBX wilt delen, kunt u ervoor kiezen om de patronen **in te sluiten** in de FBX. Dit betekent dat de volledige asset (inclusief patronen) wordt toegevoegd aan één FBX. Houd er echter rekening mee dat uw export wordt gecombineerd tot één enkele asset en dat het FBX-bestand als gevolg redelijk groot zal zijn.

>[!IMPORTANT]
>Als het FBX-bestand groter is dan 2,4 GB, moet de minimale versie van de FBX-exportinstellingen (zie hierboven) 2016 of hoger zijn. Nieuwere versies bieden ondersteuning voor 64 bits en ondersteunen dus grotere bestanden.

* In de FBX-exportinstellingen schakelt u **Media insluiten in en daarna klikt u op **OK** om te exporteren met de patronen erbij. 

Als u tijdens het gebruik van het fysieke materiaal naar FBX exporteert, ziet u waarschijnlijk de volgende waarschuwing nadat u op OK hebt geklikt in het exporteerdialoogvenster: ![export-warnings](media/3dsmax/export-warnings.jpg)

Met deze waarschuwing wordt de gebruiker erover geïnformeerd dat het geëxporteerde materiaal mogelijk niet compatibel is met andere softwarepakketten. Omdat het fysieke materiaal compatibel is met Azure Remote Rendering, hoeft u zich geen zorgen te maken. Klik op **OK** om het proces te voltooien en het venster te sluiten.

## <a name="conclusion"></a>Conclusie

Over het algemeen lijken materialen van dit type realistisch, omdat ze zijn gebaseerd op natuurlijk licht. Hierdoor ontstaat een extra onderdompelingseffect dat ervoor zorgt dat de scène werkelijk lijkt te bestaan.

## <a name="next-steps"></a>Volgende stappen

U weet nu hoe u materialen kunt instellen met geavanceerde belichting voor objecten in een scène. U weet ook hoe u de objecten kunt exporteren naar de FBX-indeling die wordt ondersteund door Azure Remote Rendering. De volgende stap bestaat uit het converteren van het FBX-bestand en visualiseren in Azure Remote Rendering.

>[!div class="nextstepaction"]
>[Snelstart: een model converteren voor rendering](../../quickstarts\convert-model.md)