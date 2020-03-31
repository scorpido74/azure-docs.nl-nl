---
title: Ontwikkelhulpprogramma’s
titleSuffix: Azure Data Science Virtual Machine
description: Meer informatie over de tools en geïntegreerde ontwikkelomgevingen die beschikbaar zijn op de Data Science Virtual Machine.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: c48cf6a7a82e90d3c9d8dc4c35e37dfb944af99f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282678"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Ontwikkeltools op de Virtual Machine van Azure Data Science

De Data Science Virtual Machine (DSVM) bundelt verschillende populaire tools in een zeer productieve geïntegreerde ontwikkelomgeving (IDE). Hier zijn enkele tools die worden geleverd op de DSVM.

## <a name="visual-studio-community-edition"></a>Visual Studio Community Edition

|    |           |
| ------------- | ------------- |
| Wat is het?   | Algemeen doel IDE      |
| Ondersteunde DSVM-versies      | Windows: Visual Studio 2017, Windows 2019 : Visual Studio 2019      |
| Typische toepassingen      | Softwareontwikkeling    |
| Hoe is het geconfigureerd en geïnstalleerd op de DSVM?      | Data Science Workload (Python en R tools), Azure workload (Hadoop, Data Lake), Node.js, SQL Server tools, [Azure Machine Learning voor Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Hoe het te gebruiken en uit te voeren      | Snelkoppeling op`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`het bureaublad ( ). Open Visual Studio grafisch met het bureaubladpictogram of het **menu Start.** Zoek naar programma's (Windows-logotoets+S), gevolgd door **Visual Studio**. Van daaruit u projecten maken in talen zoals C#, Python, R en Node.js.   |
| Gerelateerde tools op de DSVM      |     Visual Studio Code, RStudio, Juno  |

> [!NOTE]
> Mogelijk krijgt u een bericht dat uw evaluatieperiode is verstreken. Voer uw Microsoft-accountgegevens in. Of maak een nieuw gratis account aan om toegang te krijgen tot de Visual Studio Community.

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Wat is het?   | Algemeen doel IDE      |
| Ondersteunde DSVM-versies      | Windows, Linux     |
| Typische toepassingen      | Codeeditor en Git-integratie   |
| Hoe het te gebruiken en uit te voeren      | Snelkoppeling op`C:\Program Files (x86)\Microsoft VS Code\Code.exe`het bureaublad ( )`code`in Windows, desktop snelkoppeling of terminal ( ) in Linux    |
| Gerelateerde tools op de DSVM      |     Visual Studio, RStudio, Juno  |

## <a name="rstudio-desktop"></a>RStudio Desktop

|    |           |
| ------------- | ------------- |
| Wat is het?   | Client IDE voor R-taal   |
| Ondersteunde DSVM-versies      | Windows, Linux      |
| Typische toepassingen      |  R ontwikkeling     |
| Hoe het te gebruiken en uit te voeren      | Snelkoppeling op`C:\Program Files\RStudio\bin\rstudio.exe`het bureaublad (`/usr/bin/rstudio`) op Windows, desktop snelkoppeling ( ) op Linux      |
| Gerelateerde tools op de DSVM      |   Visual Studio, Visual Studio Code, Juno      |

## <a name="rstudio-server"></a>RStudio Server

|    |           |
| ------------- | ------------- |
| Wat is het?   | Client IDE voor R-taal   |
| Wat is het?   | Web-based IDE voor R    |
| Ondersteunde DSVM-versies      | Linux      |
| Typische toepassingen      |  R ontwikkeling     |
| Hoe het te gebruiken en uit te voeren      | Schakel de service in met _systemctl enable rstudio-server_en start de service met _systemctl start rstudio-server._ Meld u dan aan bij\/RStudio Server op http: /your-vm-ip:8787.       |
| Gerelateerde tools op de DSVM      |   Visual Studio, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Wat is het?   | Client IDE voor Julia taal   |
| Ondersteunde DSVM-versies      | Windows, Linux      |
| Typische toepassingen      |  De ontwikkeling van Julia     |
| Hoe het te gebruiken en uit te voeren      | Snelkoppeling op`C:\JuliaPro-0.5.1.1\Juno.bat`het bureaublad (`/opt/JuliaPro-VERSION/Juno`) op Windows, desktop snelkoppeling ( ) op Linux      |
| Gerelateerde tools op de DSVM      |   Visual Studio, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm Pycharm

|    |           |
| ------------- | ------------- |
| Wat is het?   | Client IDE voor Python-taal    |
| Ondersteunde DSVM-versies      | Windows 2019, Linux      |
| Typische toepassingen      |  Python-ontwikkeling     |
| Hoe het te gebruiken en uit te voeren      | Snelkoppeling op`C:\Program Files\tk`het bureaublad ( ) op Windows. Snelkoppeling op`/usr/bin/pycharm`het bureaublad op Linux      |
| Gerelateerde tools op de DSVM      |   Visual Studio, Visual Studio Code, RStudio      |
