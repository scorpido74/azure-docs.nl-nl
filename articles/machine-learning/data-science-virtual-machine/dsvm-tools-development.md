---
title: Ontwikkelhulpprogramma’s
titleSuffix: Azure Data Science Virtual Machine
description: Meer informatie over de hulpprogram ma's en geïntegreerde ontwikkel omgevingen die beschikbaar zijn op de Data Science Virtual Machine.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: bc1f40760c1602d81da042bf6909e44a540d35de
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390616"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Ontwikkel hulpprogramma's op de Azure Data Science Virtual Machine

De Data Science Virtual Machine (DSVM) bundelt verschillende populaire hulpprogram ma's in een zeer productieve Integrated Development Environment (IDE). Hier zijn enkele hulpprogramma's die beschikbaar zijn op de DSVM.

## <a name="visual-studio-community-edition"></a>Visual Studio Community Edition

|    |           |
| ------------- | ------------- |
| Wat is het?   | IDE voor algemeen gebruik      |
| Ondersteunde DSVM-versies      | Windows: Visual Studio 2017, Windows 2019 (preview): Visual Studio 2019      |
| Wordt doorgaans gebruikt      | Software ontwikkeling    |
| Hoe wordt het geconfigureerd en geïnstalleerd op de DSVM?      | Data Science workload (python en R-hulpprogram ma's), Azure workload (Hadoop, Data Lake), node. js, SQL Server-hulpprogram ma's, [Azure machine learning voor Visual Studio code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Hoe gebruiken en uitvoeren      | Snelkoppeling bureau blad (`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`). Open Visual Studio met behulp van het bureaublad pictogram of het menu **Start** . Zoek naar Program ma's (Windows-logo toets + S), gevolgd door **Visual Studio**. Van daaruit kunt u projecten maken in talen zoals C#python, R en node. js.   |
| Gerelateerde hulpprogram ma's op de DSVM      |     Visual Studio Code, RStudio, Juno  |

> [!NOTE]
> Mogelijk is, krijgt u een bericht dat uw evaluatieperiode is verlopen. Voer de referenties van uw Microsoft-account. Of maak een nieuwe gratis account voor toegang tot Visual Studio Community.

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Wat is het?   | IDE voor algemeen gebruik      |
| Ondersteunde DSVM-versies      | Windows, Linux     |
| Wordt doorgaans gebruikt      | Code-editor en Git-integratie   |
| Hoe gebruiken en uitvoeren      | Snelkoppeling bureau blad (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) in Windows, bureaublad snelkoppeling of Terminal (`code`) in Linux    |
| Gerelateerde hulpprogram ma's op de DSVM      |     Visual Studio, RStudio, Juno  |

## <a name="rstudio-desktop"></a>RStudio Desktop

|    |           |
| ------------- | ------------- |
| Wat is het?   | Client-IDE voor R-taal   |
| Ondersteunde DSVM-versies      | Windows, Linux      |
| Wordt doorgaans gebruikt      |  R-ontwikkeling     |
| Hoe gebruiken en uitvoeren      | Bureaublad snelkoppeling (`C:\Program Files\RStudio\bin\rstudio.exe`) in Windows, bureaublad snelkoppeling (`/usr/bin/rstudio`) op Linux      |
| Gerelateerde hulpprogram ma's op de DSVM      |   Visual Studio, Visual Studio code, Juno      |

## <a name="rstudio-server"></a>RStudio-server

|    |           |
| ------------- | ------------- |
| Wat is het?   | Client-IDE voor R-taal   |
| Wat is het?   | Webgebaseerde IDE voor R    |
| Ondersteunde DSVM-versies      | Linux      |
| Wordt doorgaans gebruikt      |  R-ontwikkeling     |
| Hoe gebruiken en uitvoeren      | Schakel de service in met _systemctl rstudio-server inschakelen_en start de service vervolgens met _systemctl start rstudio-server_. Meld u vervolgens aan bij de RStudio-server op http:\//your-VM-IP: 8787.       |
| Gerelateerde hulpprogram ma's op de DSVM      |   Visual Studio, Visual Studio code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Wat is het?   | IDE-client voor Julia-taal   |
| Ondersteunde DSVM-versies      | Windows, Linux      |
| Wordt doorgaans gebruikt      |  Julia-ontwikkeling     |
| Hoe gebruiken en uitvoeren      | Bureaublad snelkoppeling (`C:\JuliaPro-0.5.1.1\Juno.bat`) in Windows, bureaublad snelkoppeling (`/opt/JuliaPro-VERSION/Juno`) op Linux      |
| Gerelateerde hulpprogram ma's op de DSVM      |   Visual Studio, Visual Studio code, RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| Wat is het?   | IDE-client voor Python-taal    |
| Ondersteunde DSVM-versies      | Windows 2019 (preview), Linux      |
| Wordt doorgaans gebruikt      |  Python-ontwikkeling     |
| Hoe gebruiken en uitvoeren      | Bureaublad snelkoppeling (`C:\Program Files\tk`) in Windows. Snelkoppeling bureau blad (`/usr/bin/pycharm`) op Linux      |
| Gerelateerde hulpprogram ma's op de DSVM      |   Visual Studio, Visual Studio code, RStudio      |
