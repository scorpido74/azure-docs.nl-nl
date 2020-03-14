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
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79372170"
---
## <a name="about-cognitive-services-encryption"></a>Over Cognitive Services versleuteling

Gegevens worden versleuteld en ontsleuteld met behulp van [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) [-compatibele 256-bits AES-](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) versleuteling. Versleuteling en ontsleuteling zijn transparant, wat betekent dat versleuteling en toegang voor u worden beheerd. Uw gegevens zijn standaard beveiligd en u hoeft uw code of toepassingen niet te wijzigen om te kunnen profiteren van versleuteling.

## <a name="about-encryption-key-management"></a>Over het beheer van versleutelings sleutels

Uw abonnement maakt standaard gebruik van door micro soft beheerde versleutelings sleutels. Als u een prijs categorie gebruikt die door de klant beheerde sleutels ondersteunt, kunt u de versleutelings instellingen voor uw resource bekijken in de sectie **versleuteling** van het [Azure Portal](https://portal.azure.com), zoals wordt weer gegeven in de volgende afbeelding.

![Versleutelings instellingen weer geven](../articles/cognitive-services/media/cognitive-services-encryption/encryptionblade.png)

Voor abonnementen die alleen door micro soft beheerde versleutelings sleutels ondersteunen, hebt u geen sectie **versleuteling** .

## <a name="customer-managed-keys-with-azure-key-vault"></a>Door de klant beheerde sleutels met Azure Key Vault

Er is ook een optie voor het beheren van uw abonnement met uw eigen sleutels. Door de klant beheerde sleutels (CMK), ook wel bekend als het nemen van uw eigen sleutel (BYOK), bieden meer flexibiliteit voor het maken, draaien, uitschakelen en intrekken van toegangs beheer. U kunt ook de versleutelings sleutels controleren die worden gebruikt voor het beveiligen van uw gegevens.
