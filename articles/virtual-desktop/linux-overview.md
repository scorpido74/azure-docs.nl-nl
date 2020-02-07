---
title: Virtueel bureau blad van Windows-Linux Support-Azure
description: Een beknopt overzicht van Linux-ondersteuning voor virtueel bureau blad van Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
ms.openlocfilehash: 47e38d79e8aa4656b8164c94b4ef439bf431e01d
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049672"
---
# <a name="linux-support"></a>Linux Support

U kunt de Linux SDK voor Windows Virtual Desktop gebruiken om een zelfstandige virtueel-bureaubladclient voor Windows te bouwen. U kunt dit ook gebruiken om ondersteuning van Windows virtueel bureau blad in te scha kelen voor uw client toepassing. In deze korte hand leiding wordt uitgelegd wat de Linux SDK is en hoe u deze kunt gebruiken.

## <a name="what-is-the-linux-sdk"></a>Wat is de Linux SDK?

U kunt de SDK-Api's gebruiken om bron feeds op te halen, verbinding te maken met de sessies van een bureau blad of externe toepassing en veel van de omleidingen te gebruiken die onze clients van de eerste partij ondersteunen.

### <a name="supported-linux-distributions"></a>Ondersteunde Linux-distributies

De SDK is compatibel met de meeste besturings systemen op basis van Ubuntu 18,04 of hoger. Als u een andere Linux-distributie hebt, kunt u met u samen werken om erachter te komen hoe u uw behoeften het beste wordt ondersteund.

### <a name="feature-support"></a>Ondersteuning van functies

De SDK ondersteunt meerdere verbindingen met Desktop-en externe toepassings sessies. De volgende omleidingen worden ondersteund:

| Omleiding       | Ondersteund |
| :---------------- | :-------: |
| Toetsen          | &#10004;  |
| Klikken             | &#10004;  |
| Audio in          | &#10004;  |
| Audio-out         | &#10004;  |
| Klem bord (tekst)  | &#10004;  |
| Klem bord (afbeelding) | &#10004;  |
| Klem bord (bestand)  | &#10004;  |
| Cards         | &#10004;  |
| Station/map      | &#10004;  |

De SDK biedt ook ondersteuning voor meerdere monitor configuraties, op voor waarde dat de monitors die u voor uw sessie selecteert, aaneengesloten zijn.

Dit document wordt bijgewerkt tijdens het toevoegen van ondersteuning voor nieuwe functies en omleidingen. Ga naar onze [UserVoice-pagina](https://go.microsoft.com/fwlink/?linkid=2116523)als u nieuwe functies en andere verbeteringen wilt Voorst Ellen.

## <a name="get-started-with-the-linux-sdk"></a>Aan de slag met de Linux SDK

Voordat u een Linux-client voor Windows virtueel bureau blad kunt ontwikkelen, moet u het volgende doen:

1. Bouw en implementeer een Windows Virtual Desktop-omgeving voor het testen of productie gebruik.
2. Test de beschik bare clients van de eerste partij om vertrouwd te raken met de gebruikers ervaring van het virtuele Windows-bureau blad.

## <a name="next-steps"></a>Volgende stappen

De SDK is momenteel in ontwikkeling. Dit document wordt bijgewerkt met instructies voor toegang tot de SDK wanneer deze beschikbaar is.

Bekijk onze documentatie voor de volgende clients:

- [Windows-bureaubladclient](connect-windows-7-and-10.md)
- [Webclient](connect-web.md)
- [Android-client](connect-android.md)
- [macOS-client](connect-macos.md)
- [iOS-client](connect-ios.md)
