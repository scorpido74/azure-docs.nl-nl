---
title: Pakketten in een Jupyter-notebook op Azure installeren
description: Het installeren van Python, R, en F# pakketten uit binnen een Jupyter-notebook op Azure.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 6f089c12-128b-4dbd-96e3-1320d37eeba4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: b0881cb6dac9ec83d2126942c758508e760f9c83
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274427"
---
# <a name="install-packages-from-within-a-notebook"></a>Installeren van pakketten van binnen een laptop

Hoewel u kunt configureren de [omgeving voor uw laptop op het niveau van het project](configure-manage-azure-notebooks-projects.md#configure-the-project-environment), kunt u rechtstreeks vanuit de laptop van een afzonderlijke-pakketten installeren.

Pakketten geïnstalleerd vanuit het notitieblok gelden alleen voor de huidige sessie. Installatie van het pakket zijn niet persistent gemaakt nadat de server wordt afgesloten.

## <a name="python"></a>Python

Pakketten in Python kunnen worden geïnstalleerd met behulp van pip of met behulp van de opdrachten binnen code cellen conda:

```bash
!pip install <package_name>

!conda install <package_name> -y
```

Als uitvoer van de opdracht geeft aan dat al door de voorwaarde is voldaan, wordt Azure notitieblokken kan het pakket bevatten standaard. Het pakket kan ook worden geïnstalleerd via een [project omgeving instellingsstap](configure-manage-azure-notebooks-projects.md#configure-the-project-environment).

## <a name="r"></a>R

Pakketten in R kunnen worden geïnstalleerd vanuit kranen of github met `install.packages` behulp van de functie in een code-cel:

```r
install.packages("package_name")
```

U kunt ook prerelease-versies en andere ontwikkel pakketten van GitHub installeren met behulp van de devtools-bibliotheek:

```r
options(unzip = 'internal')
library(devtools)
install_github('<user>/<repo>')
```

## <a name="f"></a>F#

Pakketten in F# kan worden geïnstalleerd vanaf [nuget.org](https://www.nuget.org) door het aanroepen van de afhankelijkheid Paket manager uit binnen de cellen van code. Laad eerst de Paket manager:

```fsharp
#load "Paket.fsx"
```

Installeer vervolgens de pakketten:

```fsharp
Paket.Package
  [ "MathNet.Numerics"
    "MathNet.Numerics.FSharp"
  ]
```

Laad vervolgens de Paket-Generator:
```fsharp
#load "Paket.Generated.Refs.fsx"
```

Open de bibliotheek:
```fsharp
open MathNet.Numerics
```

## <a name="next-steps"></a>Volgende stappen

- [Procedure: Projecten configureren en beheren](configure-manage-azure-notebooks-projects.md)
- [Procedure: Een diavoorstelling weer geven](present-jupyter-notebooks-slideshow.md)
