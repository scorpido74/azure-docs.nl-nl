---
title: Pakketten in Jupyter-notebooks installeren-Azure Notebooks preview
description: 'Meer informatie over het installeren van python-, R-en F #-pakketten in een Jupyter-notebook dat wordt uitgevoerd op Azure.'
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 43d38c9f3a4b0095967ab3e103ea729ec86ea2bd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85831213"
---
# <a name="install-packages-from-within-azure-notebooks-preview"></a>Pakketten installeren vanuit Azure Notebooks preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Hoewel u de [omgeving voor uw notitie blok kunt configureren op project niveau](configure-manage-azure-notebooks-projects.md#configure-the-project-environment), wilt u mogelijk pakketten rechtstreeks in een afzonderlijk notitie blok installeren.

Pakketten die vanaf het notitie blok zijn geïnstalleerd, zijn alleen van toepassing op de huidige Server sessie. Pakket installaties blijven niet behouden zodra de server is afgesloten.

## <a name="python"></a>Python

Pakketten in python kunnen worden geïnstalleerd met behulp van PIP of Conda met behulp van opdrachten in code cellen:

```bash
!pip install <package_name>

!conda install <package_name> -y
```

Als de uitvoer van de opdracht aangeeft dat de vereiste al is voldaan, dan kan Azure Notebooks standaard het pakket bevatten. Het pakket kan ook worden geïnstalleerd via een [installatie stap van een project omgeving](configure-manage-azure-notebooks-projects.md#configure-the-project-environment).

## <a name="r"></a>R

Pakketten in R kunnen worden geïnstalleerd vanuit KRANen of GitHub met behulp van de `install.packages` functie in een code-cel:

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

Pakketten in F # kunnen worden geïnstalleerd vanuit [nuget.org](https://www.nuget.org) door de Paket-afhankelijkheids Manager aan te roepen vanuit code cellen. Eerst moet u de Paket Manager laden:

```fsharp
#load "Paket.fsx"
```

Installeer vervolgens pakketten:

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

- [Procedure: projecten configureren en beheren](configure-manage-azure-notebooks-projects.md)
- [Procedure: een diavoorstelling presen teren](present-jupyter-notebooks-slideshow.md)
