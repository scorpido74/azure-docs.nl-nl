---
title: Voorbeeldtypen van klasse typen op Azure Lab Services | Microsoft Documenten
description: Biedt een aantal typen klassen waarvoor u labs instellen met Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 27619a69a1f7fbded8ce6430afc2b8e9a8b4a00c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79296724"
---
# <a name="class-types-overview---azure-lab-services"></a>Overzicht van klassetypen - Azure Lab Services

Met Azure Lab Services u snel klassikale laboratoriumomgevingen instellen in de cloud. Artikelen in deze sectie bieden richtlijnen voor het instellen van verschillende typen klaslokalen met Azure Lab Services.

## <a name="deep-learning-in-natural-language-processing"></a>Deep learning in natuurlijke taalverwerking

U een lab opzetten dat is gericht op deep learning in natural language processing (NLP) met Azure Lab Services. Natural language processing (NLP) is een vorm van kunstmatige intelligentie (AI) waarmee computers met vertaal-, spraakherkennings- en andere mogelijkheden voor taalbegrip kunnen worden gebruikt. Studenten die een NLP-klas volgen, krijgen een Linux virtuele machine (VM) om te leren hoe ze neurale netwerkalgoritmen kunnen toepassen om deep learning-modellen te ontwikkelen die worden gebruikt voor het analyseren van geschreven menselijke taal.

Zie Een lab instellen dat zich richt [op deep learning in natuurlijke taalverwerking met Azure Lab Services](class-type-deep-learning-natural-processing.md)voor gedetailleerde informatie over het instellen van dit type lab.

## <a name="shell-scripting-on-linux"></a>Shell-scripts in Linux

U het opzetten van een lab om shell scripting te leren op Linux. Scripting is een handig onderdeel van systeembeheer waarmee beheerders repetitieve taken kunnen vermijden. In dit voorbeeldscenario omvat de klasse traditionele bashscripts en verbeterde scripts. Verbeterde scripts zijn scripts die bash-commando's en Ruby combineren. Deze aanpak stelt Ruby in staat om gegevens door te geven en commando's te bashen om met de shell te communiceren.

Studenten die deze scripting lessen krijgen een Linux virtuele machine om de basisprincipes van Linux te leren, en ook vertrouwd te raken met de bash shell scripting. De Linux virtuele machine wordt geleverd met remote desktop toegang ingeschakeld en met [gedit](https://help.gnome.org/users/gedit/stable/) en [Visual Studio Code](https://code.visualstudio.com/) tekst editors geïnstalleerd.

Zie [Shell scripting op Linux](class-type-shell-scripting-linux.md)voor gedetailleerde informatie over het opzetten van dit type lab.

## <a name="ethical-hacking"></a>Ethisch hacken

U het opzetten van een lab voor een klasse die zich richt op forensische kant van ethische hacking. Penetratie testen, een praktijk die wordt gebruikt door de ethische hacking gemeenschap, treedt op wanneer iemand probeert om toegang te krijgen tot het systeem of netwerk om kwetsbaarheden die een kwaadwillende aanvaller kan exploiteren aan te tonen.

In een ethische hacking klasse, studenten kunnen leren moderne technieken voor de verdediging tegen kwetsbaarheden. Elke student krijgt een Windows Server host virtuele machine die twee geneste virtuele machines heeft - een virtuele machine met [Metasploitable3](https://github.com/rapid7/metasploitable3) beeld en een andere machine met [Kali Linux-afbeelding.](https://www.kali.org/) De Metasploitable virtuele machine wordt gebruikt voor exploitatiedoeleinden.  De Kali Linux virtuele machine biedt toegang tot de tools die nodig zijn om forensische taken uit te voeren.

Voor gedetailleerde informatie over het opzetten van dit type lab, zie Het opzetten van [een lab om ethische hacking klasse te leren.](class-type-ethical-hacking.md)

## <a name="database-management"></a>Databasebeheer
Databases concepten zijn een van de inleidende cursussen onderwezen in de meeste van de Informatica afdelingen op de universiteit. U een lab instellen voor een basisbeheerklasse voor databases in Azure Lab Services. U bijvoorbeeld een sjabloon voor virtuele machines instellen in een lab met een [MySQL Database](https://www.mysql.com/) Server of een [SQL Server 2019-server.](https://www.microsoft.com/sql-server/sql-server-2019)

Zie [Een lab opzetten om databasebeheer voor relationele databases te onderwijzen voor](class-type-database-management.md)gedetailleerde informatie over het instellen van dit type lab.

## <a name="python-and-jupyter-notebooks"></a>Python- en Jupyter-notitieblokken
U een sjabloonmachine instellen in Azure Lab Services met de tools die nodig zijn om studenten te leren hoe ze [Jupyter-notitieblokken](http://jupyter-notebook.readthedocs.io)moeten gebruiken. Jupyter Notebooks is een open-source project waarmee u [Python](https://www.python.org/) eenvoudig rijke tekst en uitvoerbare Python-broncode combineren op één canvas dat een notitieblok wordt genoemd. Het uitvoeren van een notebook resulteert in een lineaire registratie van ingangen en uitgangen.  Deze uitvoer kan tekst, tabellen met informatie, spreidingsplots en meer bevatten.

Zie Een lab opzetten om data science [te onderwijzen met Python- en Jupyter-laptops voor](class-type-jupyter-notebook.md)gedetailleerde informatie over het instellen van dit type lab.

## <a name="mobile-app-development-with-android-studio"></a>Ontwikkeling met Android Studio via de mobiele app
U een lab instellen in Azure Lab Services om een inleidende klasse voor het ontwikkelen van mobiele toepassingen te onderwijzen. Deze klasse richt zich op Android mobiele applicaties die kunnen worden gepubliceerd naar de [Google Play Store](https://play.google.com/store/apps).  Studenten leren hoe ze [Android Studio](https://developer.android.com/studio) kunnen gebruiken om applicaties te bouwen.  [Visual Studio Emulator voor Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) wordt gebruikt om de toepassing lokaal te testen.

Zie [Een lab opzetten om mobiele applicatieontwikkeling met Android Studio te leren](class-type-mobile-dev-android-studio.md)voor gedetailleerde informatie over het opzetten van dit type lab.


## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen:

- [Een lab opzetten dat zich richt op deep learning in natuurlijke taalverwerking met Azure Lab Services](class-type-deep-learning-natural-processing.md)
- [Shell-scripts in Linux](class-type-shell-scripting-linux.md)
- [Ethisch hacken](class-type-ethical-hacking.md)