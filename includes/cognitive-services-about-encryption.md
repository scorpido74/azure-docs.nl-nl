---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: 2a348827b1c992e0ef0a4592cc0b9c5c0fa0ca19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372170"
---
## <a name="about-cognitive-services-encryption"></a>Versleuteling van Cognitive Services

Gegevens worden versleuteld en gedecodeerd met [FIPS 140-2-compatibele](https://en.wikipedia.org/wiki/FIPS_140-2) [256-bits AES-versleuteling.](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) Versleuteling en decryptie zijn transparant, wat betekent dat encryptie en toegang voor u worden beheerd. Uw gegevens zijn standaard beveiligd en u hoeft uw code of toepassingen niet te wijzigen om te profiteren van versleuteling.

## <a name="about-encryption-key-management"></a>Over beheer van versleutelingssleutels

Standaard maakt uw abonnement gebruik van door Microsoft beheerde versleutelingssleutels. Als u een prijscategorie gebruikt die door de klant beheerde sleutels ondersteunt, u de versleutelingsinstellingen voor uw bron zien in het gedeelte **Versleuteling** van de [Azure-portal](https://portal.azure.com), zoals weergegeven in de volgende afbeelding.

![Versleutelingsinstellingen weergeven](../articles/cognitive-services/media/cognitive-services-encryption/encryptionblade.png)

Voor abonnementen die alleen door Microsoft beheerde versleutelingssleutels ondersteunen, hebt u geen **sectie Versleuteling.**

## <a name="customer-managed-keys-with-azure-key-vault"></a>Door de klant beheerde sleutels met Azure Key Vault

Er is ook een optie om uw abonnement met uw eigen sleutels te beheren. Door de klant beheerde sleutels (CMK), ook wel bekend als Bring your own key (BYOK), bieden meer flexibiliteit om toegangsbesturingselementen te maken, te roteren, uit te schakelen en in te trekken. U ook de versleutelingssleutels controleren die worden gebruikt om uw gegevens te beschermen.
