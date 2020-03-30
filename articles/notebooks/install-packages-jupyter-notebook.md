---
title: Pakketten installeren in Jupyter-notitieblokken - Proefversie van Azure Notebooks
description: Meer informatie over het installeren van Python-, R- en F#-pakketten vanuit een Jupyter-notitieblok dat op Azure wordt uitgevoerd.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: f6359b4c010834ffaee00b6208d309997339f36e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646225"
---
# <a name="install-packages-from-within-azure-notebooks-preview"></a>Pakketten installeren vanuit Azure Notebooks Preview

Hoewel u de [omgeving voor uw notitieblok op projectniveau](configure-manage-azure-notebooks-projects.md#configure-the-project-environment)configureren, u pakketten rechtstreeks in een afzonderlijk notitieblok installeren.

Pakketten die vanuit het notitieblok zijn geïnstalleerd, zijn alleen van toepassing op de huidige serversessie. Pakketinstallaties worden niet gehandhaafd zodra de server is uitgeschakeld.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="python"></a>Python

Pakketten in Python kunnen worden geïnstalleerd met behulp van pip of conda met behulp van opdrachten in codecellen:

```bash
!pip install <package_name>

!conda install <package_name> -y
```

Als de opdrachtuitvoer aangeeft dat al aan de vereiste is voldaan, kunnen Azure-notitieblokken het pakket standaard bevatten. Het pakket kan ook worden geïnstalleerd via een [projectomgevinginstallatiestap.](configure-manage-azure-notebooks-projects.md#configure-the-project-environment)

## <a name="r"></a>R

Pakketten in R kunnen vanaf CRAN of `install.packages` GitHub worden geïnstalleerd met behulp van de functie in een codecel:

```r
install.packages("package_name")
```

U ook prerelease-versies en andere ontwikkelingspakketten van GitHub installeren met behulp van de devtools-bibliotheek:

```r
options(unzip = 'internal')
library(devtools)
install_github('<user>/<repo>')
```

## <a name="f"></a>F#

Pakketten in F# kunnen vanaf [nuget.org](https://www.nuget.org) worden geïnstalleerd door de afhankelijkheidsmanager paket vanuit codecellen te bellen. Laad eerst de Paket-manager:

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

Laad vervolgens de Paket generator:
```fsharp
#load "Paket.Generated.Refs.fsx"
```

Open de bibliotheek:
```fsharp
open MathNet.Numerics
```

## <a name="next-steps"></a>Volgende stappen

- [Hoe: Projecten configureren en beheren](configure-manage-azure-notebooks-projects.md)
- [Hoe: Een diavoorstelling presenteren](present-jupyter-notebooks-slideshow.md)
