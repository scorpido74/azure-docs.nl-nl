---
title: Azure Kinect DK opnieuw instellen
description: Hierin wordt beschreven hoe u een Azure Kinect DK-apparaat hersteld naar de fabrieksinstallatiekopie
author: Teresa-Motiv
ms.author: v-tea
ms.reviewer: jarrettr
ms.prod: kinect-dk
ms.date: 02/11/2020
ms.topic: how-to
keywords: kinect, opnieuw instellen
ms.custom:
- CI 113492
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: f851579712a9549c88f1ca652aea4f31457da132
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201895"
---
# <a name="reset-azure-kinect-dk"></a>Azure Kinect DK opnieuw instellen

U kunt in een situatie terechtkomen waarin u Azure Kinect DK moet herstellen naar de fabrieksinstallatiekopie (bijvoorbeeld als een firmware-update niet juist is geïnstalleerd).

1. Haal Azure Kinect DK van het stroomnet. U kunt dit doen door de USB-kabel en de stroomkabel te verwijderen.
  ![Een diagram waarin de locatie wordt aangegeven van de schroef die over de herstelknop zit.](media/reset-azure-kinect-dk-diagram.png)
1. Als u de herstelknop zoekt, verwijdert u de statiefschroef in de koppelingsvergrendeling.
1. Sluit de stroomkabel opnieuw aan.
1. Steek de punt van een rechtgebogen paperclip in het gat waaruit u de schroef hebt verwijderd, in de koppelingsvergrendeling
1. Gebruik de paperclip om de knop voor opnieuw instellen voorzichtig ingedrukt te houden.
1. Terwijl u deze knop ingedrukt houdt, sluit u de USB-kabel weer aan.
1. Na ongeveer 3 seconden verandert het lichtje van de aan/uit-indicator in geelbruin. Wanneer het licht is gewijzigd, laat u de knop voor opnieuw instellen los.  
   
   Nadat u de knop opnieuw instellen hebt losgelaten, knipperen de lichtjes van de aan/uit-indicator wit en geelbruin terwijl het apparaat opnieuw wordt ingesteld. 
1. Wacht totdat het lampje van de aan/uit-indicator effen wit is.
1. Plaats de statiefschroef terug in de koppelingsvergrendeling, over de knop voor opnieuw instellen heen.
1. Gebruik de Azure Kinect-viewer om te controleren of de firmware opnieuw is ingesteld. Hiervoor start u de [Azure Kinect-viewer](azure-kinect-viewer.md), en selecteert u vervolgens **Versiegegevens voor apparaatfirmware** om te zien welke firmwareversie is geïnstalleerd op Azure Kinect DK.

Zorg er altijd voor dat de meest recente firmware op het apparaat is geïnstalleerd. Gebruik het hulpprogramma voor Azure Kinect-firmware om de meest recente firmwareversie te verkrijgen. Raadpleeg [Firmwareversie van app controleren](azure-kinect-firmware-tool.md#check-device-firmware-version) voor meer informatie over het controleren van de firmwarestatus.

## <a name="related-topics"></a>Verwante onderwerpen

- [Over Azure Kinect DK](about-azure-kinect-dk.md)
- [Azure Kinect DK installeren](set-up-azure-kinect-dk.md)
- [Azure Kinect DK-hardwarespecificaties: Besturingsomgeving](hardware-specification.md#operating-environment)
- [Hulpprogramma voor Azure Kinect-firmware](azure-kinect-firmware-tool.md)
- [Azure Kinect-viewer](azure-kinect-viewer.md)
- [Synchronisatie tussen meerdere Azure Kinect DK-apparaten](multi-camera-sync.md)
