---
title: Systeemvereisten
description: Een lijst met de systeem vereisten voor de externe rendering van Azure
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: article
ms.openlocfilehash: 9754636063e29592595ee57d09164ae1134341a1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84300603"
---
# <a name="system-requirements"></a>Systeemvereisten

> [!IMPORTANT]
> **Azure Remote Rendering** is momenteel beschikbaar als openbare preview-versie.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Dit hoofd stuk bevat de minimale systeem vereisten voor het werken met *Azure remote rendering* (arr).

## <a name="development-pc"></a>Ontwikkel computer

* Windows 10 versie 1903 of hoger.
* Up-to-date grafische Stuur Programma's.
* Optioneel: H265 hardware-video decoder als u de lokale preview van extern gerenderde inhoud wilt gebruiken (bijvoorbeeld eenheids Unit).

> [!IMPORTANT]
> Windows Update levert niet altijd de meest recente GPU-Stuur Programma's. Raadpleeg de website van uw GPU-fabrikant voor de meest recente Stuur Programma's:
>
> * [AMD-Stuur Programma's](https://www.amd.com/en/support)
> * [Intel-Stuur Programma's](https://www.intel.com/content/www/us/en/support/detect.html)
> * [NVIDIA-Stuur Programma's](https://www.nvidia.com/Download/index.aspx)

In de volgende tabel ziet u welke Gpu's H265 hardware-video-decodering ondersteunt.

| GPU-fabrikant | Ondersteunde modellen |
|-----------|:-----------|
| GRAFISCHE | Controleer de **ondersteunings matrix van NVDEC** [onder aan deze pagina](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix). Uw GPU heeft ja nodig in de kolom **H. 265 4:2:0 8-bits** . |
| POWERNOW | Gpu's met ten minste versie 6 van de [gecombineerde video decoder](https://en.wikipedia.org/wiki/Unified_Video_Decoder#UVD_6)van AMD. |
| Intel | Skylake en nieuwere Cpu's |

Hoewel de juiste H265-codec kan worden geïnstalleerd, kunnen de beveiligings eigenschappen van de codec-Dll's ertoe leiden dat er codec-initialisatie fouten optreden. In de [hand leiding](../resources/troubleshoot.md#h265-codec-not-available) voor het oplossen van problemen worden stappen beschreven voor het oplossen van dit probleem. Het DLL-probleem kan zich alleen voordoen wanneer u de service in een bureaublad toepassing gebruikt, bijvoorbeeld eenheid.

## <a name="devices"></a>Apparaten

De externe rendering van Azure biedt momenteel alleen ondersteuning voor **HoloLens 2** en Windows Desktop als doel apparaat. Zie de sectie [platform beperkingen](../reference/limits.md#platform-limitations) .

Het is belang rijk dat u de nieuwste HEVC-codec gebruikt, omdat nieuwere versies aanzienlijke verbeteringen in de latentie hebben. Controleren welke versie op het apparaat is geïnstalleerd:

1. Start de **Microsoft Store**.
1. Klik op de knop **'... '** in de rechter bovenhoek.
1. Selecteer **down loads en updates**.
1. Zoek in de lijst naar **HEVC-video-uitbrei dingen van de fabrikant van het apparaat**.
1. Zorg ervoor dat de vermelde codec ten minste versie **1.0.21821.0**.
1. Klik op de knop **updates ophalen** en wacht totdat deze is geïnstalleerd.

## <a name="network"></a>Netwerk

Een stabiele netwerk verbinding met lage latentie is essentieel voor een goede gebruikers ervaring.

Zie speciaal hoofd stuk voor [netwerk vereisten](../reference/network-requirements.md).

Raadpleeg de [hand leiding](../resources/troubleshoot.md#unstable-holograms)voor het oplossen van problemen met netwerk problemen.

## <a name="software"></a>Software

De volgende software moet zijn geïnstalleerd:

* De nieuwste versie van **Visual Studio 2019** [(down load)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Visual Studio Tools voor Mixed Reality](https://docs.microsoft.com/windows/mixed-reality/install-the-tools). Met name de volgende *workload*-installaties zijn verplicht:
  * **Desktopontwikkeling met C++**
  * **Universal Windows Platform (UWP)-ontwikkeling**
* **Windows SDK 10.0.18362.0** [(down load)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* **Git** [(down load)](https://git-scm.com/downloads)
* Optioneel: als u de video stroom van de server op een desktop computer wilt weer geven, hebt u de **HEVC-video-extensie** [(Microsoft Store link)](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7)nodig.

## <a name="unity"></a>Unity

Voor ontwikkeling met Unit, installeert u

* Unity 2019.3.1 [(download)](https://unity3d.com/get-unity/download)
* Installeer de volgende modules in Unity:
  * **UWP**: ondersteuning voor UWP-builds (Universeel Windows-platform)
  * **IL2CPP**: ondersteuning voor Windows-builds (IL2CPP)

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: Een model weergeven met Unity](../quickstarts/render-model.md)
