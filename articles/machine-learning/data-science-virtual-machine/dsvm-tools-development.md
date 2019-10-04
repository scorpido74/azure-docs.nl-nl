---
title: Ontwikkelprogramma's
titleSuffix: Azure Data Science Virtual Machine
description: Meer informatie over de hulpprogram ma's en geïntegreerde ontwikkel omgevingen die beschikbaar zijn op de Data Science Virtual Machine.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/3/2019
ms.openlocfilehash: 62eb5f72d4b4395602b2665c0d1b3da4f6bb459b
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950205"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Ontwikkel hulpprogramma's op de Azure Data Science Virtual Machine

De Data Science Virtual Machine (DSVM) bundelt verschillende populaire hulpprogram ma's in een zeer productieve Integrated Development Environment (IDE). Hier zijn enkele hulpprogramma's die beschikbaar zijn op de DSVM.

## <a name="visual-studio-2017"></a>Visual Studio 2017

|    |           |
| ------------- | ------------- |
| Wat is het?   | IDE voor algemeen gebruik      |
| Ondersteunde DSVM-versies      | Windows      |
| Wordt doorgaans gebruikt      | Software ontwikkeling    |
| Hoe wordt het geconfigureerd en geïnstalleerd op de DSVM?      | Data Science-werkbelasting (Python / R tools), Azure-workload (Hadoop, Data Lake), Node.js, SQL Server-hulpprogramma's, [Azure Machine Learning voor Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Hoe gebruiken en uitvoeren      | Snelkoppeling bureau blad (`C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\devenv.exe`)    |
| Gerelateerde hulpprogram ma's op de DSVM      |     Visual Studio Code, RStudio, Juno  |

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Wat is het?   | IDE voor algemeen gebruik      |
| Ondersteunde DSVM-versies      | Windows, Linux     |
| Wordt doorgaans gebruikt      | Code-editor en Git-integratie   |
| Hoe gebruiken en uitvoeren      | Snelkoppeling bureau blad (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) in Windows, bureaublad snelkoppeling of Terminal (`code`) in Linux    |
| Gerelateerde hulpprogram ma's op de DSVM      |     Visual Studio 2017, RStudio, Juno  |

## <a name="rstudio--desktop"></a>RStudio Desktop 

|    |           |
| ------------- | ------------- |
| Wat is het?   | Client-IDE voor R-taal   |
| Ondersteunde DSVM-versies      | Windows, Linux      |
| Wordt doorgaans gebruikt      |  R-ontwikkeling     |
| Hoe gebruiken en uitvoeren      | Bureaublad snelkoppeling (`C:\Program Files\RStudio\bin\rstudio.exe`) in Windows, snelkoppeling op bureau blad (`/usr/bin/rstudio`) op Linux      |
| Gerelateerde hulpprogram ma's op de DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>RStudio Server 

|    |           |
| ------------- | ------------- |
| Wat is het?   | Client-IDE voor R-taal   |
| Wat is het?   | Webgebaseerde IDE voor R    |
| Ondersteunde DSVM-versies      | Linux      |
| Wordt doorgaans gebruikt      |  R-ontwikkeling     |
| Hoe gebruiken en uitvoeren      | Schakel de service in met _systemctl rstudio-server inschakelen_en start de service vervolgens met _systemctl start rstudio-server_. Meld u vervolgens aan bij de RStudio-server op http: \//uw-VM-IP: 8787.       |
| Gerelateerde hulpprogram ma's op de DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Wat is het?   | IDE-client voor Julia-taal   |
| Ondersteunde DSVM-versies      | Windows, Linux      |
| Wordt doorgaans gebruikt      |  Julia-ontwikkeling     |
| Hoe gebruiken en uitvoeren      | Bureaublad snelkoppeling (`C:\JuliaPro-0.5.1.1\Juno.bat`) in Windows, snelkoppeling op bureau blad (`/opt/JuliaPro-VERSION/Juno`) op Linux      |
| Gerelateerde hulpprogram ma's op de DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| Wat is het?   | IDE-client voor Python-taal    |
| Ondersteunde DSVM-versies      | Linux      |
| Wordt doorgaans gebruikt      |  Python-ontwikkeling     |
| Hoe gebruiken en uitvoeren      | Snelkoppeling bureau blad (`/usr/bin/pycharm`) in Linux      |
| Gerelateerde hulpprogram ma's op de DSVM      |   Visual Studio 2017, Visual Studio Code, RStudio      |



## <a name="power-bi-desktop"></a>Power BI Desktop 

|    |           |
| ------------- | ------------- |
| Wat is het?   | Interactieve gegevens visualisatie en BI-hulp programma    |
| Ondersteunde DSVM-versies      | Windows  |
| Wordt doorgaans gebruikt      |  Gegevens visualisatie en het maken van Dash boards   |
| Hoe gebruiken en uitvoeren      | Snelkoppeling bureau blad (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Gerelateerde hulpprogram ma's op de DSVM      |   Visual Studio 2017, Visual Studio Code, Juno      |

