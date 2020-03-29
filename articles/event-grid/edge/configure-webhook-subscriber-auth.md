---
title: Webhook-abonneeverificatie configureren - Azure Event Grid IoT Edge | Microsoft Documenten
description: Verificatie van webhook-abonnee configureren
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 101dcae5870322878cec48098f2efae32cc68c14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841727"
---
# <a name="configure-webhook-subscriber-authentication"></a>Verificatie van webhook-abonnee configureren

Deze handleiding geeft voorbeelden van de mogelijke webhook abonneeconfiguraties voor een Event Grid module. Standaard worden alleen HTTPS-eindpunten geaccepteerd voor webhook-abonnees. De module Event Grid wordt geweigerd als de abonnee een zelfondertekend certificaat presenteert.

## <a name="allow-only-https-subscriber"></a>Alleen HTTPS-abonnee toestaan

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>HTTPS-abonnee toestaan met zelfondertekend certificaat

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Stel de `outbound__webhook__allowUnknownCA` `true` eigenschap alleen in testomgevingen in, omdat u doorgaans zelfondertekende certificaten gebruiken. Voor productieworkloads raden we ze aan om op **false**te worden ingesteld.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>HTTPS-abonnee toestaan, maar certificaatvalidatie overslaan

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=true",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>Stel de `outbound__webhook__skipServerCertValidation` `true` eigenschap alleen in testomgevingen in, omdat u mogelijk geen certificaat presenteert dat moet worden geverifieerd. Voor productieworkloads raden we ze aan om op **valse**

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>Zowel HTTP als HTTPS toestaan met zelfondertekende certificaten

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=false",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Stel de `outbound__webhook__httpsOnly` `false` eigenschap alleen in testomgevingen in, omdat u misschien eerst een HTTP-abonnee wilt ophalen. Voor productieworkloads raden we ze aan om **op true**
