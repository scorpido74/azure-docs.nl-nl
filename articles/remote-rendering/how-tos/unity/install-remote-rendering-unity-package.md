---
title: Het remote rendering-pakket voor Unity installeren
description: Legt uit hoe u de DLL's voor externe renderingclient voor Unity installeert
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 86ffe47c009f9e0ae121ffc6ab57bff8fb73210f
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681179"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Het remote rendering-pakket voor Unity installeren

Azure Remote Rendering maakt gebruik van een Unity-pakket om de integratie in Unity in te kapselen.

## <a name="manage-the-remote-rendering-packages-in-unity"></a>De externe renderingpakketten beheren in Unity

Unity-pakketten zijn containers die kunnen worden beheerd via Unity's [Package Manager.](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html)
Dit pakket bevat de volledige C#API en alle plug-inbinaire bestanden die nodig zijn om Azure Remote Rendering with Unity te gebruiken.
Na unity's naamgevingsschema voor pakketten, heet het pakket **com.microsoft.azure.remote-rendering**.

Het pakket maakt geen deel uit van de [ARR-samplesrepository](https://github.com/Azure/azure-remote-rendering)en is niet beschikbaar in het interne pakketregister van Unity. Als u het aan een project wilt toevoegen, moet `manifest.md` u het bestand van het project handmatig bewerken om het volgende toe te voegen:
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
Zodra dit is toegevoegd, u de Unity Package Manager gebruiken om ervoor te zorgen dat u de nieuwste versie hebt.
Meer uitgebreide instructies worden gegeven in de [tutorial: Het opzetten van een Unity project vanaf nul](../../tutorials/unity/project-setup.md).

## <a name="unity-render-pipelines"></a>Unity renderpipelines

Remote Rendering werkt met zowel de **Universele renderpijplijn** als de **standaardrenderpijplijn.** Om prestatieredenen wordt de universele renderpijplijn aanbevolen.

Om de **Universele renderpijplijn**te gebruiken, moet het pakket in Unity worden geïnstalleerd. Dit kan worden gedaan in Unity's **Package Manager** UI (pakketnaam **Universal RP**, versie `Packages/manifest.json` 7.2.1 of nieuwer), of via het bestand, zoals beschreven in de [Unity project setup tutorial](../../tutorials/unity/project-setup.md#configure-the-projects-manifest).

## <a name="next-steps"></a>Volgende stappen

* [Unity game objecten en componenten](objects-components.md)
* [Zelfstudie: Een Unity-project vanaf nul instellen](../../tutorials/unity/project-setup.md)
