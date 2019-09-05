---
title: Data Science Virtual Machine ontwikkelprogramma's - Azure | Microsoft Docs
description: Meer informatie over de hulpprogramma's en geïntegreerde ontwikkelingsomgevingen die vooraf geïnstalleerd op de Data Science Virtual Machine zijn.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: vijetaj
ms.openlocfilehash: 3b8eaae63f0e316d82dd5a1238a802eefd756d9e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68557798"
---
# <a name="development-tools-on-the-data-science-virtual-machine"></a>Ontwikkelhulpprogramma's op de Data Science Virtual Machine

De Data Science Virtual Machine (DSVM) biedt een productieve omgeving voor het ontwikkelen van apps door bundeling van verschillende populaire hulpprogramma's en IDE. Hier zijn enkele hulpprogramma's die beschikbaar zijn op de DSVM. 

## <a name="visual-studio-2019"></a>Visual Studio 2019  

|    |           |
| ------------- | ------------- |
| Wat is het?   | Algemeen gebruik IDE      |
| Ondersteunde DSVM-versies      | Windows      |
| Wordt doorgaans gebruikt      | Ontwikkeling van software    |
| Hoe wordt deze geconfigureerd / geïnstalleerd op de DSVM?      | Data Science-werkbelasting (Python / R tools), Azure-workload (Hadoop, Data Lake), Node.js, SQL Server-hulpprogramma's, [Azure Machine Learning voor Visual Studio Code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Hoe kan ik gebruik / uitvoeren?      | Snelkoppeling op het bureaublad (`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`)    |
| Meer hulpprogramma's op de DSVM      |     Visual Studio Code, RStudio, Juno  |

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Wat is het?   | Algemeen gebruik IDE      |
| Ondersteunde DSVM-versies      | Windows, Linux     |
| Wordt doorgaans gebruikt      | Code-editor en Git-integratie   |
| Hoe kan ik gebruik / uitvoeren?      | Snelkoppeling op het bureaublad (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) in Windows, de snelkoppeling op het bureaublad of terminal (`code`) in Linux    |
| Meer hulpprogramma's op de DSVM      |     Visual Studio 2019, RStudio, Juno  |

## <a name="rstudio--desktop"></a>RStudio Desktop 

|    |           |
| ------------- | ------------- |
| Wat is het?   | Client IDE voor R    |
| Ondersteunde DSVM-versies      | Windows, Linux      |
| Wordt doorgaans gebruikt      |  R-ontwikkeling     |
| Hoe kan ik gebruik / uitvoeren?      | Snelkoppeling op het bureaublad (`C:\Program Files\RStudio\bin\rstudio.exe`) op Windows, snelkoppeling op het bureaublad (`/usr/bin/rstudio`) op Linux      |
| Meer hulpprogramma's op de DSVM      |   Visual Studio 2019, Visual Studio code, Juno      |

## <a name="rstudio--server"></a>RStudio Server 

|    |           |
| ------------- | ------------- |
| Wat is het?   | Webgebaseerde IDE voor R    |
| Ondersteunde DSVM-versies      | Linux      |
| Wordt doorgaans gebruikt      |  R-ontwikkeling     |
| Hoe kan ik gebruik / uitvoeren?      | Schakel de service met _systemctl inschakelen rstudio server_, start u de service met _systemctl start rstudio server_. U kunt zich vervolgens aanmelden bij de RStudio-server op\/http:/Your-VM-IP: 8787.       |
| Meer hulpprogramma's op de DSVM      |   Visual Studio 2019, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Wat is het?   | IDE-client voor Julia-taal   |
| Ondersteunde DSVM-versies      | Windows, Linux      |
| Wordt doorgaans gebruikt      |  Julia-ontwikkeling     |
| Hoe kan ik gebruik / uitvoeren?      | Snelkoppeling op het bureaublad (`C:\JuliaPro-0.5.1.1\Juno.bat`) op Windows, snelkoppeling op het bureaublad (`/opt/JuliaPro-VERSION/Juno`) op Linux      |
| Meer hulpprogramma's op de DSVM      |   Visual Studio 2019, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| Wat is het?   | IDE-client voor Python-taal    |
| Ondersteunde DSVM-versies      | Linux      |
| Wordt doorgaans gebruikt      |  Python-ontwikkeling     |
| Hoe kan ik gebruik / uitvoeren?      | Snelkoppeling op het bureaublad (`/usr/bin/pycharm`) op Linux      |
| Meer hulpprogramma's op de DSVM      |   Visual Studio 2019, Visual Studio Code, RStudio      |



## <a name="powerbi-desktop"></a>PowerBI Desktop 

|    |           |
| ------------- | ------------- |
| Wat is het?   | Interactieve gegevensvisualisatie en BI-hulpprogramma    |
| Ondersteunde DSVM-versies      | Windows  |
| Wordt doorgaans gebruikt      |  Gegevensvisualisatie en het bouwen van Dashboards   |
| Hoe kan ik gebruik / uitvoeren?      | Snelkoppeling op het bureaublad (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Meer hulpprogramma's op de DSVM      |   Visual Studio 2019, Visual Studio code, Juno      |

