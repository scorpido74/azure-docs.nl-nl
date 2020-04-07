---
title: Problemen oplossen
description: Informatie over probleemoplossing voor Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: 7ee219ae5ace0f0da398cc542f410d3c895c8bd4
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679983"
---
# <a name="troubleshoot"></a>Problemen oplossen

Op deze pagina worden veelvoorkomende problemen weergegeven die azure remote rendering verstoren en manieren om deze op te lossen.

## <a name="client-cant-connect-to-server"></a>Client kan geen verbinding maken met server

Zorg ervoor dat uw firewalls (op het apparaat, in routers, enz.) de volgende poorten niet blokkeren:

* **50051 (TCP)** - vereist voor de eerste verbinding (HTTP-handdruk)
* **8266 (TCP+UDP)** - vereist voor gegevensoverdracht
* **5000 (TCP)**, **5433 (TCP)**, **8443 (TCP)** - vereist voor [ArrInspector](tools/arr-inspector.md)

## <a name="error-disconnected-videoformatnotavailable"></a>Fout 'Losgekoppeld: videoformatniet beschikbaar'

Controleer of uw GPU hardwarevideodecodering ondersteunt. Zie [Development PC](../overview/system-requirements.md#development-pc).

Als u werkt aan een laptop met twee GPU's, is het mogelijk dat de GPU waarop u werkt standaard geen hardwarevideodecoderingsfunctionaliteit biedt. Als dat het zo is, probeert u uw app te dwingen de andere GPU te gebruiken. Dit is vaak mogelijk in de GPU-driverinstellingen.

## <a name="h265-codec-not-available"></a>H265-codec niet beschikbaar

Er zijn twee redenen waarom de server kan weigeren verbinding te maken met een **codec die niet beschikbaar** is.

**De H265-codec is niet geïnstalleerd:**

Zorg er eerst voor dat u de **HEVC-video-extensies** installeert zoals vermeld in het gedeelte [Software](../overview/system-requirements.md#software) van de systeemvereisten.

Als u nog steeds problemen ondervindt, moet u ervoor zorgen dat uw grafische kaart H265 ondersteunt en dat u het nieuwste grafische stuurprogramma hebt geïnstalleerd. Zie het gedeelte [Development PC](../overview/system-requirements.md#development-pc) van de systeemvereisten voor leveranciersspecifieke informatie.

**De codec is geïnstalleerd, maar kan niet worden gebruikt:**

De reden voor dit probleem is een onjuiste beveiligingsinstelling voor de DLL's. Dit probleem manifesteert zich niet wanneer u video's probeert te bekijken die zijn gecodeerd met H265. Het opnieuw installeren van de codec lost het probleem ook niet op. Voer in plaats daarvan de volgende stappen uit:

1. Een **PowerShell openen met beheerdersrechten** en uitvoeren

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    Die opdracht moet `InstallLocation` de uitvoer van de codec, zoiets als:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Die map openen in Windows Verkenner
1. Er moet een **x86** en een **x64** submap. Klik met de rechtermuisknop op een van de mappen en kies **Eigenschappen**
    1. Selecteer het tabblad **Beveiliging** en klik op de knop **Geavanceerde** instellingen
    1. Klik **op Wijzigen** voor de **eigenaar**
    1. **Beheerders** in het tekstveld typen
    1. Klik **op Namen controleren** en **OK**
1. De bovenstaande stappen voor de andere map herhalen
1. Herhaal ook de bovenstaande stappen op elk DLL-bestand in beide mappen. Er moeten in totaal vier DLL's zijn.

Als u wilt controleren of de instellingen nu correct zijn, doet u dit voor elk van de vier DLL's:

1. Selecteer **Eigenschappen > beveiliging > bewerken**
1. Ga door de lijst van alle **groepen / gebruikers** en zorg ervoor dat elk heeft de Read & **Execute** juiste set (het vinkje in de kolom **toestaan** moet worden aangevinkt)

## <a name="low-video-quality"></a>Lage videokwaliteit

De videokwaliteit kan worden aangetast door de netwerkkwaliteit of de ontbrekende H265-videocodec.

* Bekijk de stappen om netwerkproblemen te [identificeren.](#unstable-holograms)
* Bekijk de [systeemvereisten](../overview/system-requirements.md#development-pc) voor het installeren van het nieuwste grafische stuurprogramma.

## <a name="black-screen-after-successful-model-loading"></a>Zwart scherm na succesvol laden van het model

Als u verbonden bent met de rendering runtime en een model met succes hebt geladen, maar pas daarna een zwart scherm ziet, dan kan dit een paar verschillende oorzaken hebben.

We raden u aan de volgende dingen te testen voordat u een meer diepgaande analyse doet:

* Is de H265 codec geïnstalleerd? Hoewel er een terugval naar de H264 codec zou moeten zijn, hebben we gevallen gezien waarin deze terugval niet goed werkte. Bekijk de [systeemvereisten](../overview/system-requirements.md#development-pc) voor het installeren van het nieuwste grafische stuurprogramma.
* Sluit bij het gebruik van een Unity-project Unity, verwijdert de tijdelijke *bibliotheek-* en *obj-mappen* in de projectmap en laadt/bouwt het project opnieuw. In sommige gevallen in de cache opgeslagen gegevens veroorzaakt het monster niet goed functioneren zonder voor de hand liggende reden.

Als deze twee stappen niet hebben geholpen, is het nodig om uit te vinden of videoframes worden ontvangen door de client of niet. Dit kan programmatisch worden opgevraagd, zoals uitgelegd in het hoofdstuk [prestatiequery's aan de serverzijde.](../overview/features/performance-queries.md) Het `FrameStatistics struct` heeft een lid dat aangeeft hoeveel videoframes zijn ontvangen. Als dit aantal groter is dan 0 en na verloop van tijd toeneemt, ontvangt de client werkelijke videoframes van de server. Daarom moet het een probleem zijn aan de clientkant.

### <a name="common-client-side-issues"></a>Veelvoorkomende problemen aan de klantzijde

**Het model is niet binnen de weergave frustum:**

In veel gevallen wordt het model correct weergegeven, maar zich buiten het camerafrustum bevinden. Een veel voorkomende reden is dat het model is geëxporteerd met een ver off-center pivot, zodat het wordt geknipt door het verre knipvlak van de camera. Het helpt om het selectiekader van het model programmatisch op te vragen en het vak met Unity als regelvak te visualiseren of de waarden ervan af te drukken op het foutopsporingslogboek.

**De Unity render pipeline bevat niet de renderhaken:**

Azure Remote Rendering haakt in de Unity render-pijplijn om de framesamenstelling met de video uit te voeren en de herprojectie uit te voeren. Als u wilt controleren of deze haken bestaan, opent u het menu *Venster > Analyse > Framefoutbugger*. Schakel deze in en zorg ervoor `HolographicRemotingCallbackPass` dat er twee vermeldingen voor de in de pijplijn:

![Unity frame debugger](./media/troubleshoot-unity-pipeline.png)

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>Eenheidscode met de API voor externe rendering wordt niet gecompileerd

Schakel het *buildtype* van de Unity-oplossing over op **Foutopsporing.** Bij het testen van ARR `UNITY_EDITOR` in de Unity editor is de define alleen beschikbaar in 'Debug' builds. Houd er rekening mee dat dit niets te maken heeft met het buildtype dat wordt gebruikt voor [geïmplementeerde toepassingen,](../quickstarts/deploy-to-hololens.md)waarbij u de voorkeur geeft aan 'Release'-builds.

## <a name="unstable-holograms"></a>Onstabiele hologrammen

In het geval dat gerenderde objecten lijken te bewegen samen met hoofdbewegingen, u problemen ondervinden met *Late Stage Reprojection* (LSR). Raadpleeg het gedeelte late [fase reprojectie](../overview/features/late-stage-reprojection.md) voor richtlijnen over hoe een dergelijke situatie te benaderen.

Een andere reden voor onstabiele hologrammen (wiebelen, kromtrekken, jittering, of springen hologrammen) kan een slechte netwerkconnectiviteit, met name onvoldoende netwerkbandbreedte, of te hoge latentie. Een goede indicator voor de kwaliteit van uw `ARRServiceStats.VideoFramesReused`netwerkverbinding is de waarde van de [prestatiestatistieken.](../overview/features/performance-queries.md) Hergebruikte frames geven situaties aan waarin een oud videoframe aan de clientzijde opnieuw moest worden gebruikt omdat er geen nieuw videoframe beschikbaar was , bijvoorbeeld vanwege pakketverlies of vanwege variaties in netwerklatentie. Als `ARRServiceStats.VideoFramesReused` dit vaak groter is dan nul, duidt dit op een netwerkprobleem.

Een andere waarde `ARRServiceStats.LatencyPoseToReceiveAvg`om naar te kijken is. Het moet consequent onder de 100 ms. Als u hogere waarden ziet, geeft dit aan dat u bent verbonden met een datacenter dat te ver weg is.

Zie de [richtlijnen voor netwerkconnectiviteit voor](../reference/network-requirements.md#guidelines-for-network-connectivity)een lijst met mogelijke oplossingen.

## <a name="next-steps"></a>Volgende stappen

* [Systeemvereisten](../overview/system-requirements.md)
* [Netwerkvereisten](../reference/network-requirements.md)
