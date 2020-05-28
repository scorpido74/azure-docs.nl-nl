---
title: Het Remote Rendering-pakket voor Unity installeren
description: Uitleg over het installeren van de externe rendering client-Dll's voor Unity
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 3375b28d94956d5c368db4bf3026bdf52ee2d58e
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84021140"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Het Remote Rendering-pakket voor Unity installeren

Azure remote rendering maakt gebruik van een eenheids pakket om de integratie in Unit te integreren.

## <a name="manage-the-remote-rendering-packages-in-unity"></a>De externe rendering-pakketten in eenheid beheren

Unity-pakketten zijn containers die kunnen worden beheerd via [Package Manager](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html)van Unity.
Dit pakket bevat de volledige C# API en alle binaire bestanden voor invoeg toepassingen die vereist zijn voor het gebruik van externe rendering van Azure met eenheid.
Het naam schema van de volgende eenheid voor pakketten wordt het pakket **com. micro soft. Azure. remote-rendering**genoemd.

Het pakket maakt geen deel uit van de [opslag plaats ARR](https://github.com/Azure/azure-remote-rendering)-voor beelden en is niet beschikbaar in het interne pakket register van de eenheid. Als u het aan een project wilt toevoegen, moet u het bestand van het project hand matig bewerken `manifest.md` om het volgende toe te voegen:
```json
{
  "scopedRegistries": [
    {
      "name": "Azure Mixed Reality Services",
      "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
      "scopes": ["com.microsoft.azure"]
    }
   ],
  "dependencies": {
    "com.microsoft.azure.remote-rendering": "0.1.11",
    ...existing dependencies...
  }
}
```
Zodra dit is toegevoegd, kunt u unit package manager gebruiken om te controleren of u de meest recente versie hebt.
Meer uitgebreide instructies vindt u in de [zelf studie: een geheel nieuw eenheids project instellen](../../tutorials/unity/project-setup.md).

## <a name="unity-render-pipelines"></a>Unit weergave-pijp lijnen

Externe rendering werkt met zowel de **:::no-loc text="Universal render pipeline":::** als **:::no-loc text="Standard render pipeline":::** . Uit prestatie overwegingen wordt de universele rendering-pijp lijn aanbevolen.

Als u het wilt gebruiken, moet het **:::no-loc text="Universal render pipeline":::** bijbehorende pakket worden geïnstalleerd in eenheid. Dit kan worden gedaan in de gebruikers interface van de **Package Manager** van Unit (pakket naam **Universal RP**, version 7.2.1 of hoger) of via het `Packages/manifest.json` bestand, zoals beschreven in de [zelf studie over unit project Setup](../../tutorials/unity/project-setup.md#configure-the-projects-manifest).

## <a name="next-steps"></a>Volgende stappen

* [Unit-Game objecten en-onderdelen](objects-components.md)
* [Zelf studie: een geheel nieuw eenheids project instellen](../../tutorials/unity/project-setup.md)
