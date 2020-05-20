---
title: Problemen oplossen
description: Probleemoplossings informatie voor de externe rendering van Azure
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: b518b2b92ba6d2529ffdefce754a3b29b74fb21b
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83674288"
---
# <a name="troubleshoot"></a>Problemen oplossen

Op deze pagina vindt u veelvoorkomende problemen met betrekking tot de externe rendering van Azure en manieren om deze op te lossen.

## <a name="client-cant-connect-to-server"></a>Client kan geen verbinding maken met de server

Zorg ervoor dat de firewalls (op het apparaat, binnen routers, enzovoort) niet de volgende poorten blok keren:

* **50051 (TCP)** : vereist voor de eerste verbinding (http-Handshake)
* **8266 (TCP + UDP)** -vereist voor gegevens overdracht
* **5000 (TCP)**, **5433 (tcp)**, **8443 (TCP)** -vereist voor [ArrInspector](tools/arr-inspector.md)

## <a name="error-disconnected-videoformatnotavailable"></a>Fout ' verbinding verbroken: VideoFormatNotAvailable '

Controleer of uw GPU hardware-video-decodering ondersteunt. Zie [ontwikkel-PC](../overview/system-requirements.md#development-pc).

Als u werkt met een laptop met twee Gpu's, is het mogelijk dat de GPU waarop u uitvoert standaard niet de functionaliteit voor het decoderen van hardware-video biedt. Als dit het geval is, probeert u uw app te dwingen de andere GPU te gebruiken. Dit is vaak mogelijk in de instellingen van het GPU-stuur programma.

## <a name="h265-codec-not-available"></a>De H265-codec is niet beschikbaar

Er zijn twee redenen waarom de server geen verbinding kan maken met een **niet-beschik bare codec** .

**De H265-codec is niet geïnstalleerd:**

Zorg er eerst voor dat u de **HEVC-video-uitbrei dingen** installeert zoals vermeld in de sectie [Software](../overview/system-requirements.md#software) van de systeem vereisten.

Als u nog steeds problemen ondervindt, moet u ervoor zorgen dat uw grafische kaart ondersteuning biedt voor H265 en dat het nieuwste grafische stuur programma is geïnstalleerd. Zie de sectie [ontwikkel-PC](../overview/system-requirements.md#development-pc) van de systeem vereisten voor leverancierspecifieke informatie.

**De codec is geïnstalleerd, maar kan niet worden gebruikt:**

De reden voor dit probleem is een onjuiste beveiligings instelling voor de Dll's. Dit probleem treedt niet op bij het bekijken van Video's die zijn gecodeerd met H265. Als u de codec opnieuw installeert, wordt het probleem niet opgelost. Voer in plaats daarvan de volgende stappen uit:

1. Open een **Power shell met beheerders rechten** en voer uit

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    Met deze opdracht moet de `InstallLocation` van de codec worden uitgevoerd, ongeveer als volgt:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Deze map openen in Windows Verkenner
1. Er moet een **x86** -en een **x64** -submap zijn. Klik met de rechter muisknop op een van de mappen en kies **Eigenschappen**
    1. Selecteer het tabblad **beveiliging** en klik op de knop **Geavanceerde** instellingen.
    1. Klik op **wijzigen** voor de **eigenaar**
    1. Typ **beheerders** in het tekst veld
    1. Klik op **Namen controleren** en vervolgens op **OK**
1. Herhaal de bovenstaande stappen voor de andere map
1. Herhaal ook de bovenstaande stappen voor elk DLL-bestand in beide mappen. Er moeten vier dll-bestanden aanwezig zijn.

Ga als volgt te werk om te controleren of de instellingen nu juist zijn, voor elk van de vier Dll's:

1. **Eigenschappen > beveiliging > bewerken** selecteren
1. Door loop de lijst met alle **groepen/gebruikers** en zorg ervoor dat elke groep de machtiging **lezen & uitvoeren** is ingesteld (het vinkje in de kolom **toestaan** moet worden getickd)

## <a name="low-video-quality"></a>Lage video kwaliteit

De video kwaliteit kan worden aangetast door netwerk kwaliteit of de ontbrekende H265-videocodec.

* Zie de stappen om [netwerk problemen te identificeren](#unstable-holograms).
* Zie de [systeem vereisten](../overview/system-requirements.md#development-pc) voor het installeren van het nieuwste grafische stuur programma.

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>Video die is opgenomen in MRC weerspiegelt niet de kwaliteit van de Live-ervaring

Een video kan worden vastgelegd op Hololens via [Mixed Reality Capture (MRC)](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers). De resulterende video heeft echter een slechterere kwaliteit dan de Live-ervaring om twee redenen:
* De video weergave snelheid wordt beperkt tot 30 Hz, in tegens telling tot 60 Hz.
* De video-installatie kopieën worden niet door de verwerkings stap voor het [vertraagd](../overview/features/late-stage-reprojection.md) verwerken van taken uitgevoerd, dus de video lijkt choppier te zijn.

Beide zijn inherente beperkingen van de opname techniek.

## <a name="black-screen-after-successful-model-loading"></a>Zwart scherm nadat het laden van het model is geslaagd

Als u verbonden bent met de rendering-runtime en een model hebt geladen, maar er een zwart scherm wordt weer gegeven, kan dit een aantal verschillende oorzaken hebben.

Het is raadzaam om de volgende dingen te testen voordat u een uitgebreidere analyse uitvoert:

* Is de H265-codec geïnstalleerd? Hoewel er sprake is van een terugval van de H264-codec, hebben we gevallen gezien waarin deze terugval niet goed werkt. Zie de [systeem vereisten](../overview/system-requirements.md#development-pc) voor het installeren van het nieuwste grafische stuur programma.
* Wanneer u een unit-project gebruikt, sluit eenheid, verwijdert u de map tijdelijke *bibliotheek* en *obj* in de projectmap en laadt u het project opnieuw en bouwt u het opnieuw. In sommige gevallen hebben gegevens in de cache het voor beeld niet goed functioneren om een duidelijke reden.

Als deze twee stappen niet helpen, is het nodig om erachter te komen of video frames zijn ontvangen door de client of niet. Dit kan via een programma worden opgevraagd, zoals wordt uitgelegd in het hoofd stuk [server-side performance query's](../overview/features/performance-queries.md) . De `FrameStatistics struct` bevat een lid dat aangeeft hoeveel video frames zijn ontvangen. Als dit aantal groter is dan 0 en de tijd toeneemt, ontvangt de client daad werkelijke video frames van de server. Daarom moet het een probleem zijn aan de client zijde.

### <a name="common-client-side-issues"></a>Veelvoorkomende problemen aan de client zijde

**Het model overschrijdt de limieten van de geselecteerde virtuele machine, met name het maximum aantal veelhoeken:**

Zie specifieke [beperkingen](../reference/limits.md#overall-number-of-polygons)voor de VM-grootte.

**Het model bevindt zich niet in de weer gave-frustum:**

In veel gevallen wordt het model correct weer gegeven, maar buiten de camera frustum. Een veelvoorkomende reden is dat het model is geëxporteerd met een uit de weg geplaatste draaiing, zodat het wordt geknipt door het ver knip vlak van de camera. Het helpt het model in het kader van een programma op te vragen en het vak met eenheid als een lijn vak te visualiseren of de waarden in het logboek voor fout opsporing af te drukken.

Bovendien genereert het conversie proces een [JSON-uitvoer bestand](../how-tos/conversion/get-information.md) naast het geconverteerde model. Als u fouten wilt opsporen in model positionering, is het een goed idee om de `boundingBox` vermelding in de [sectie outputStatistics](../how-tos/conversion/get-information.md#the-outputstatistics-section)te bekijken:

```JSON
{
    ...
    "outputStatistics": {
        ...
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

Het selectie kader wordt beschreven als een `min` en `max` -positie in 3D-ruimte, uitgedrukt in meters. Een coördinaat van 1000,0 betekent dus 1 kilo meter weg van de oorsprong.

Er kunnen twee problemen zijn met dit selectie kader die leiden tot onzichtbare geometrie:
* **Het vak kan niet uit het midden worden bevinden**, zodat het object volledig is afgekapt vanwege de meeste knip knippen. De `boundingBox` waarden in dit geval zien er als volgt uit: `min = [-2000, -5,-5], max = [-1990, 5,5]` , met behulp van een grote offset op de x-as als hier een voor beeld. Schakel de `recenterToOrigin` optie in de configuratie van de [model conversie](../how-tos/conversion/configure-model-conversion.md)in om dit type probleem op te lossen.
* **Het vak kan worden gecentreerd, maar de volg orde van de grootte is te groot**. Dit betekent dat hoewel de camera wordt gestart in het midden van het model, de geometrie ervan in alle richtingen is afgekapt. Typische `boundingBox` waarden in dit geval zien er als volgt uit: `min = [-1000,-1000,-1000], max = [1000,1000,1000]` . De reden voor dit type probleem is doorgaans een niet-overeenkomende eenheids schaal. Als u wilt compenseren, geeft u een [schaal waarde op tijdens de conversie](../how-tos/conversion/configure-model-conversion.md#geometry-parameters) of markeert u het bron model met de juiste eenheden. Schalen kan ook worden toegepast op het hoofd knooppunt wanneer het model tijdens runtime wordt geladen.

**De unit weergave-pijp lijn bevat geen render-hooks:**

Azure remote rendering hooks in de unit weergave-pijp lijn voor het samen stellen van de kader samenstelling met de video en om de herprojectie uit te voeren. Als u wilt controleren of deze hooks bestaan, opent u het menu *venster > analyse > frame debugger*. Schakel dit in en zorg ervoor dat er twee vermeldingen zijn voor de `HolographicRemotingCallbackPass` in de pijp lijn:

![Unity frame debugger](./media/troubleshoot-unity-pipeline.png)

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>Unit code met behulp van de remote rendering API wordt niet gecompileerd

### <a name="use-debug-when-compiling-for-unity-editor"></a>Fout opsporing gebruiken bij het compileren van Unity editor

Schakel het *type Build* van unit Solution in op **debug**. Bij het testen van ARR in de eenheids editor is de definitie `UNITY_EDITOR` alleen beschikbaar in builds voor fout opsporing. Houd er rekening mee dat dit niet gerelateerd is aan het build-type dat wordt gebruikt voor [geïmplementeerde toepassingen](../quickstarts/deploy-to-hololens.md), waarbij u de voor keur geeft aan release-builds.

### <a name="compile-failures-when-compiling-unity-samples-for-hololens-2"></a>Fouten compileren bij het compileren van unit-samples voor HoloLens 2

Er zijn onlogische fouten gedetecteerd bij het compileren van unit-voor beelden (Quick Start, ShowCaseApp,..) voor HoloLens 2. Visual Studio klagen over het al dan niet kunnen kopiëren van bestanden, maar ze zijn er wel. Als u dit probleem ondervindt:
* Verwijder alle tijdelijke unit-bestanden uit het project en probeer het opnieuw. Dat wil zeggen, sluit eenheid, verwijder de tijdelijke *bibliotheek* en *obj* -mappen in de projectmap en laad/bouw het project opnieuw.
* Zorg ervoor dat de projecten zich in een map op schijf bevinden met redelijk korte pad, omdat de Kopieer stap soms problemen met lange bestands namen ondervindt.
* Als dat niet helpt, kan het zijn dat MS Sense de Kopieer stap verstoort. Als u een uitzonde ring wilt instellen, voert u deze register opdracht uit vanaf de opdracht regel (hiervoor zijn beheerders rechten vereist):
    ```cmd
    reg.exe ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection" /v groupIds /t REG_SZ /d "Unity”
    ```
    
## <a name="unstable-holograms"></a>Onstabiele hologrammen

In het geval dat gerenderde objecten samen met de hoofd bewegingen worden verplaatst, kunnen er problemen optreden met de *vertraagde fase* van het project (lsr). Raadpleeg de sectie over [vertraagde fase](../overview/features/late-stage-reprojection.md) ring van het project voor richt lijnen voor het aanpaken van een dergelijke situatie.

Een andere reden voor onstabiele hologrammen (Wobbling, krom trekken, jitteren of springende Holograms) kan een slechte netwerk verbinding zijn, met name onvoldoende netwerk bandbreedte of te hoge latentie. Een goede indicator voor de kwaliteit van uw netwerk verbinding is de waarde voor [prestatie statistieken](../overview/features/performance-queries.md) `ARRServiceStats.VideoFramesReused` . Hergebruikte frames geven situaties aan waarbij een oud video frame moet worden hergebruikt aan de client zijde omdat er geen nieuw video frame beschikbaar was, bijvoorbeeld vanwege pakket verlies of vanwege afwijkingen in de netwerk latentie. Als `ARRServiceStats.VideoFramesReused` het regel matig groter is dan nul, duidt dit op een netwerk probleem.

U kunt ook een andere waarde zoeken in `ARRServiceStats.LatencyPoseToReceiveAvg` . Deze moet consistent zijn onder 100 MS. Als u hogere waarden ziet, betekent dit dat u verbonden bent met een Data Center dat te ver weg is.

Zie de [richt lijnen voor netwerk connectiviteit](../reference/network-requirements.md#guidelines-for-network-connectivity)voor een lijst met mogelijke oplossingen.

## <a name="next-steps"></a>Volgende stappen

* [Systeemvereisten](../overview/system-requirements.md)
* [Netwerkvereisten](../reference/network-requirements.md)
