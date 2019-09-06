---
title: Ontwikkelprogramma's
titleSuffix: Azure Data Science Virtual Machine
description: Meer informatie over de hulpprogram ma's en geïntegreerde ontwikkel omgevingen die beschikbaar zijn op de Data Science Virtual Machine.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/11/2017
ms.openlocfilehash: 6ff4d92cb3730716c532332bf426132fcbb8e122
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70191946"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Ontwikkel hulpprogramma's op de Azure Data Science Virtual Machine

De Data Science Virtual Machine (DSVM) bundelt verschillende populaire hulpprogram ma's in een zeer productieve Integrated Development Environment (IDE). Hier zijn enkele hulpprogramma's die beschikbaar zijn op de DSVM.

## <a name="visual-studio-2019"></a>Visual Studio 2019  

|    |           |
| ------------- | ------------- |
| Wat is het?   | IDE voor algemeen gebruik      |
| Ondersteunde DSVM-versies      | Windows      |
| Wordt doorgaans gebruikt      | Software ontwikkeling    |
| Hoe wordt het geconfigureerd en geïnstalleerd op de DSVM?      | Data Science-werkbelasting (Python / R tools), Azure-workload (Hadoop, Data Lake), Node.js, SQL Server-hulpprogramma's, [Azure Machine Learning voor Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Hoe gebruiken en uitvoeren      | Snelkoppeling bureau blad`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`()    |
| Gerelateerde hulpprogram ma's op de DSVM      |     Visual Studio Code, RStudio, Juno  |

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Wat is het?   | IDE voor algemeen gebruik      |
| Ondersteunde DSVM-versies      | Windows, Linux     |
| Wordt doorgaans gebruikt      | Code-editor en Git-integratie   |
| Hoe gebruiken en uitvoeren      | Bureaublad snelkoppeling (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) in Windows, bureaublad snelkoppeling of Terminal (`code`) in Linux    |
| Gerelateerde hulpprogram ma's op de DSVM      |     Visual Studio 2019, RStudio, Juno  |

## <a name="rstudio--desktop"></a>RStudio Desktop 

|    |           |
| ------------- | ------------- |
| Wat is het?   | Client-IDE voor R-taal   |
| Ondersteunde DSVM-versies      | Windows, Linux      |
| Wordt doorgaans gebruikt      |  R-ontwikkeling     |
| Hoe gebruiken en uitvoeren      | Bureau blad (`C:\Program Files\RStudio\bin\rstudio.exe`) in Windows, snelkoppeling op Bureau`/usr/bin/rstudio`blad () op Linux      |
| Gerelateerde hulpprogram ma's op de DSVM      |   Visual Studio 2019, Visual Studio code, Juno      |

## <a name="rstudio--server"></a>RStudio Server 

|    |           |
| ------------- | ------------- |
| Wat is het?   | Client-IDE voor R-taal   |
| Wat is het?   | Webgebaseerde IDE voor R    |
| Ondersteunde DSVM-versies      | Linux      |
| Wordt doorgaans gebruikt      |  R-ontwikkeling     |
| Hoe gebruiken en uitvoeren      | Schakel de service in met _systemctl rstudio-server inschakelen_en start de service vervolgens met _systemctl start rstudio-server_. Meld u vervolgens aan bij de RStudio-server\/op http:/Your-VM-IP: 8787.       |
| Gerelateerde hulpprogram ma's op de DSVM      |   Visual Studio 2019, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Wat is het?   | IDE-client voor Julia-taal   |
| Ondersteunde DSVM-versies      | Windows, Linux      |
| Wordt doorgaans gebruikt      |  Julia-ontwikkeling     |
| Hoe gebruiken en uitvoeren      | Bureau blad (`C:\JuliaPro-0.5.1.1\Juno.bat`) in Windows, snelkoppeling op Bureau`/opt/JuliaPro-VERSION/Juno`blad () op Linux      |
| Gerelateerde hulpprogram ma's op de DSVM      |   Visual Studio 2019, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| Wat is het?   | IDE-client voor Python-taal    |
| Ondersteunde DSVM-versies      | Linux      |
| Wordt doorgaans gebruikt      |  Python-ontwikkeling     |
| Hoe gebruiken en uitvoeren      | Bureaublad snelkoppeling (`/usr/bin/pycharm`) op Linux      |
| Gerelateerde hulpprogram ma's op de DSVM      |   Visual Studio 2019, Visual Studio Code, RStudio      |



## <a name="power-bi-desktop"></a>Power BI Desktop 

|    |           |
| ------------- | ------------- |
| Wat is het?   | Interactieve gegevens visualisatie en BI-hulp programma    |
| Ondersteunde DSVM-versies      | Windows  |
| Wordt doorgaans gebruikt      |  Gegevens visualisatie en het maken van Dash boards   |
| Hoe gebruiken en uitvoeren      | Snelkoppeling bureau blad`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`()      |
| Gerelateerde hulpprogram ma's op de DSVM      |   Visual Studio 2019, Visual Studio code, Juno      |

