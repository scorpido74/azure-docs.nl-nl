---
title: Het azure cloudshell-venster gebruiken | Microsoft Documenten
description: Overzicht van het gebruik van het Azure Cloud Shell-venster.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: damaerte
ms.openlocfilehash: 3435d958049012750e775e2a17f43f463a0cc358
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70860316"
---
# <a name="using-the-azure-cloud-shell-window"></a>Het Azure Cloud Shell-venster gebruiken

In dit document wordt uitgelegd hoe u het venster Cloud Shell gebruikt.

## <a name="swap-between-bash-and-powershell-environments"></a>Wisselen tussen Bash- en PowerShell-omgevingen

Gebruik de omgevingskiezer op de werkbalk Cloud Shell om te wisselen tussen Bash- en PowerShell-omgevingen.  
![Omgeving selecteren](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>CloudShell opnieuw opstarten
Klik op het pictogram Opnieuw starten op de werkbalk Cloud Shell om de machinestatus opnieuw in te stellen.  
![CloudShell opnieuw opstarten](media/using-the-shell-window/restart.png)
> [!WARNING]
> Als u Cloud Shell opnieuw start, wordt de status van de machine opnieuw ingesteld en gaan alle bestanden die niet worden gehandhaafd door uw Azure-bestandsshare verloren.

## <a name="change-the-text-size"></a>De tekstgrootte wijzigen
Klik op het instellingenpictogram linksboven in het venster en plaats de muisaanwijzer op de optie Tekstgrootte en selecteer de gewenste tekstgrootte. Uw selectie blijft bestaan tijdens sessies.
![Tekstgrootte](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>Het lettertype wijzigen
Klik op het instellingenpictogram linksboven in het venster en plaats de muisaanwijzer op de optie Lettertype en selecteer het gewenste lettertype.  Uw selectie blijft bestaan tijdens sessies.
![Lettertype](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>Bestanden uploaden en downloaden
Klik op het pictogram bestanden uploaden/downloaden linksboven in het venster en selecteer vervolgens uploaden of downloaden.  
![Bestanden uploaden/downloaden](media/using-the-shell-window/uploaddownload.png)
* Als u bestanden wilt uploaden, gebruikt u de pop-up om naar het bestand op uw lokale computer te bladeren, selecteert u het gewenste bestand en klikt u op de knop Openen.  Het bestand wordt geüpload naar de `/home/user` directory.
* Voor het downloaden van bestand voert u het volledig gekwalificeerde bestandspad in het pop-upvenster in (d.w.z. in feite een pad onder de `/home/user` map dat standaard wordt weergegeven) en selecteert u de knop 'Downloaden'.  
> [!NOTE] 
> Bestanden en bestandspaden zijn hoofdlettergevoelig in Cloud Shell. Controleer je behuizing in je bestandspad.

## <a name="open-another-cloud-shell-window"></a>Een ander Cloud Shell-venster openen
Cloud Shell maakt meerdere gelijktijdige sessies mogelijk op browsertabbladen doordat elke sessie als een afzonderlijk proces kan bestaan.
Als u een sessie afsluit, moet u elk sessievenster verlaten terwijl elk proces onafhankelijk wordt uitgevoerd, hoewel ze op dezelfde machine worden uitgevoerd.  
Klik op het nieuwe sessiepictogram openen linksboven in het venster. Er wordt een nieuw tabblad geopend met een andere sessie die is gekoppeld aan de bestaande container.
![Nieuwe sessie openen](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Cloud Shell-editor
* Raadpleeg de pagina [Azure Cloud Shell-editor gebruiken.](using-cloud-shell-editor.md)

## <a name="web-preview"></a>Webvoorbeeld
Klik op het pictogram voor webvoorbeeld linksboven in het venster, selecteer 'Configureren' en geef de gewenste poort op die u wilt openen.  Selecteer 'Poort openen' om alleen de poort te openen of 'Openen en bladeren' om de poort te openen en een voorbeeld van de poort in een nieuw tabblad te bekijken.  
![Webvoorbeeld](media/using-the-shell-window/preview.png)  
<br>
![Poort configureren](media/using-the-shell-window/preview-configure.png)  
Klik op het webvoorbeeldpictogram linksboven in het venster en selecteer 'Voorbeeldpoort ..." als u een voorbeeld van een geopende poort in een nieuw tabblad wilt bekijken. Klik op het pictogram voor webvoorbeeld linksboven in het venster en selecteert 'Poort sluiten ..." om de open poort te sluiten.  
![Poort voor voorbeeld/afsluiting](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>Minimaliseer & maximaliseer het Venster Cloud Shell
Klik op het pictogram minimaliseren rechtsboven in het venster om het te verbergen. Klik nogmaals op het pictogram Cloud Shell om de verborgen.
Klik op het pictogram maximaliseren om het venster in te stellen op maximale hoogte. Als u het venster wilt herstellen naar de vorige grootte, klikt u op Herstellen.  
![Het venster minimaliseren of maximaliseren](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>Kopiëren en plakken
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Het formaat van het venster Cloud Shell wijzigen
Klik en sleep de bovenrand van de werkbalk omhoog of omlaag om het formaat van het Venster Cloud Shell te wijzigen.

## <a name="scrolling-text-display"></a>Schuivende tekst weergeven
Scroll met je muis of touchpad om terminaltekst te verplaatsen.

## <a name="exit-command"></a>Afsluiten, opdracht
Uitvoeren `exit` eindigt de actieve sessie. Dit gedrag treedt standaard op na 20 minuten zonder interactie.

## <a name="next-steps"></a>Volgende stappen

[Bash in Cloud Shell Quickstart](quickstart.md) <br>
[PowerShell in Cloud Shell Quickstart](quickstart-powershell.md)
