---
title: Webhook-abonnee verificatie configureren-Azure Event Grid IoT Edge | Microsoft Docs
description: Webhook-abonnee verificatie configureren
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 97ed1e2ad84d895e9da0d96cd070e14acb46385d
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992481"
---
# <a name="configure-webhook-subscriber-authentication"></a>Webhook-abonnee verificatie configureren

Deze hand leiding bevat voor beelden van de mogelijke configuratie van de webhook-abonnee voor een Event Grid module. Standaard worden alleen HTTPS-eind punten geaccepteerd voor webhook-abonnees. De module Event Grid wordt geweigerd als de abonnee een zelfondertekend certificaat presenteert.

## <a name="allow-only-https-subscriber"></a>Alleen HTTPS-abonnee toestaan

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>HTTPS-abonnee met zelfondertekend certificaat toestaan

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Stel de eigenschap `outbound:webhook:allowUnknownCA` in op `true` alleen in test omgevingen, omdat u meestal zelfondertekende certificaten kunt gebruiken. Voor werk belastingen voor productie raden wij aan dat ze worden ingesteld op **Onwaar**.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>HTTPS-abonnee toestaan, maar certificaat validatie overs Laan

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=true",
    "outbound:webhook:allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>Stel de eigenschap `outbound:webhook:skipServerCertValidation` in op `true` alleen in test omgevingen als u mogelijk geen certificaat presenteert dat moet worden geverifieerd. Voor werk belastingen voor productie raden wij aan dat ze worden ingesteld op **Onwaar** .

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>Zowel HTTP als HTTPS toestaan met zelfondertekende certificaten

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=false",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Stel de eigenschap `outbound:webhook:httpsOnly` in op `false` alleen in test omgevingen als u mogelijk eerst een HTTP-abonnee wilt weer geven. Voor werk belastingen voor productie raden wij aan dat deze worden ingesteld op **waar**
