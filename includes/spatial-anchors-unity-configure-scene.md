---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/2/2019
ms.author: crtreasu
ms.openlocfilehash: 7e7825e8247e78cbc0c0e9e22bdbd9326939e0a8
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83998041"
---
De volgende stap is het configureren van de app om uw account-id en accountsleutel te gebruiken. U hebt ze naar een teksteditor gekopieerd bij het [instellen van de Spatial Anchors-resource](#create-a-spatial-anchors-resource).

Ga in het deelvenster **Project** naar `Assets\AzureSpatialAnchors.SDK\Resources`. Selecteer `SpatialAnchorConfig`. Voer vervolgens in het deelvenster **Controle** de `Account Key` in als de waarde voor `Spatial Anchors Account Key` en de `Account ID` als waarde voor `Spatial Anchors Account Id`.

Open vervolgens `SpatialAnchorManager.cs`. Zoek `CreateSessionAsync()` en voeg de volgende regel toe waarbij u uw accountdomein van eerder gebruikt: `session.Configuration.AccountDomain = "MyAccountDomain";`. U kunt deze regel direct vóór deze opmerking `// Configure authentication` toevoegen.
