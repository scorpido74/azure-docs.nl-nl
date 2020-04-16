---
title: Systeemvereisten
description: Hier worden de systeemvereisten voor Azure Remote Rendering weergegeven
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: article
ms.openlocfilehash: 8573a88d5371bbde07a541c789f52e6c44f1e279
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411133"
---
# <a name="system-requirements"></a>Systeemvereisten

> [!IMPORTANT]
> **Azure Remote Rendering** is momenteel in openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

In dit hoofdstuk worden de minimale systeemvereisten weergegeven om te werken met *Azure Remote Rendering* (ARR).

## <a name="development-pc"></a>Ontwikkelings-pc

* Windows 10 versie 1903 of hoger.
* Up-to-date grafische drivers.
* Optioneel: H265 hardware videodecoder, als u lokale preview van op afstand gerenderde inhoud wilt gebruiken (bijvoorbeeld in Unity).

> [!IMPORTANT]
> Windows-update levert niet altijd de allernieuwste GPU-stuurprogramma's, controleer de website van de GPU-fabrikant op de nieuwste stuurprogramma's:
>
> * [AMD-stuurprogramma's](https://www.amd.com/en/support)
> * [Intel-stuurprogramma's](https://www.intel.com/content/www/us/en/support/detect.html)
> * [NVIDIA-stuurprogramma's](https://www.nvidia.com/Download/index.aspx)

In de onderstaande tabel ziet u welke GPU's H265-hardwarevideodecodering ondersteunen.

| GPU-fabrikant | Ondersteunde modellen |
|-----------|:-----------|
| Nvidia | Bekijk de **NVDEC Support Matrix** [onderaan deze pagina.](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix) Uw GPU heeft een JA nodig in de **kolom H.265 4:2:0 8-bit.** |
| Amd | GPU's met ten minste versie 6 van AMD's [Unified Video Decoder](https://en.wikipedia.org/wiki/Unified_Video_Decoder#UVD_6). |
| Intel | Skylake en nieuwere CPU's |

Hoewel de juiste H265-codec kan worden geïnstalleerd, kunnen beveiligingseigenschappen op de codec-dll's codec-initialisatiefouten veroorzaken. In [de handleiding voor probleemoplossing](../resources/troubleshoot.md#h265-codec-not-available) worden stappen beschreven om dit probleem op te lossen. Het DLL-probleem kan alleen optreden wanneer de service wordt gebruikt in een desktoptoepassing, bijvoorbeeld in Unity.

## <a name="devices"></a>Apparaten

Azure Remote Rendering ondersteunt momenteel alleen **HoloLens 2** en Windows-bureaublad als doelapparaat. Zie de sectie [platformbeperkingen.](../reference/limits.md#platform-limitations)

Het is belangrijk om de nieuwste HEVC-codec te gebruiken, omdat nieuwere versies aanzienlijke verbeteringen in latentie hebben. Ga als u controleren welke versie op uw apparaat is geïnstalleerd:

1. Start de **Microsoft Store**.
1. Klik rechtsboven op de knop **"..."**
1. Selecteer **Downloads en updates**.
1. Zoek in de lijst naar **HEVC-video-extensies van apparaatfabrikant**.
1. Controleer of de vermelde codec ten minste versie **1.0.21821.0**heeft .
1. Klik **op** de knop Updates ontvangen en wacht tot deze is geïnstalleerd.

## <a name="network"></a>Netwerk

Een stabiele netwerkverbinding met lage latentie is essentieel voor een goede gebruikerservaring.

Zie speciaal hoofdstuk voor [netwerkvereisten](../reference/network-requirements.md).

Raadpleeg de handleiding voor [probleemoplossing](../resources/troubleshoot.md#unstable-holograms)voor het oplossen van netwerkproblemen voor het oplossen van problemen.

## <a name="software"></a>Software

De volgende software moet worden geïnstalleerd:

* De nieuwste versie van **Visual Studio 2019** [(download)](https://visualstudio.microsoft.com/vs/older-downloads/)
* **Windows SDK 10.0.18362.0** [(downloaden)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* **GIT** [(download)](https://git-scm.com/downloads)
* Optioneel: als u de videostream vanaf de server op een desktop-pc wilt bekijken, hebt u de **HEVC-video-extensies** [(Microsoft Store-koppeling)](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7)nodig.

## <a name="unity"></a>Unity

Voor ontwikkeling met Unity, installeren

* Unity 2019.3.1 [(download)](https://unity3d.com/get-unity/download)
* Installeer deze modules in Unity:
  * **UWP** - Ondersteuning voor universal Windows Platform Build
  * **IL2CPP** - Windows Build Support (IL2CPP)

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: een model weergeven met Unity](../quickstarts/render-model.md)
