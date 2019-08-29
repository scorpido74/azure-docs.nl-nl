---
title: Upgrade van het besturings systeem voor de SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: Upgrade van het besturings systeem uitvoeren voor SAP HANA op Azure (grote exemplaren)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6341f58791c2fad71a65650e32cff02fb52d78c0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098681"
---
# <a name="operating-system-upgrade"></a>Upgrade van besturings systeem
In dit document worden de details van upgrades van besturings systemen op de HANA grote instanties beschreven.

>[!NOTE]
>De upgrade van het besturings systeem is verantwoordelijk voor micro soft-ondersteuning en kan u helpen bij de belangrijkste gebieden die u tijdens de upgrade kunt bekijken. Neem ook contact op met de leverancier van uw besturings systeem voordat u een upgrade gaat plannen.

Op het moment van het inrichten van de HLI-eenheid installeert het micro soft Operations-team het besturings systeem. U moet de volgende keer dat u het besturings systeem houdt: Patches Toep assen, afstemmen, upgraden enz.) op de HLI-eenheid.

Voordat u belang rijke wijzigingen doorvoert aan het besturings systeem (bijvoorbeeld een upgrade van SP1 naar SP2), moet u contact opnemen met het micro soft Operations-team door een ondersteunings ticket te openen om te raadplegen.

In uw ticket insluiten:

* De ID van uw HLI-abonnement.
* De naam van uw server.
* Het patch niveau dat u wilt Toep assen.
* De datum waarop u deze wijziging plant. 

U wordt aangeraden dit ticket ten minste één week voor de gewenste upgrade datum te openen, omdat het operationele team een controle moet uitvoeren als er een firmware-upgrade nodig is op de Blade van de server.


Zie [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581)voor de ondersteunings matrix van de verschillende SAP Hana versies met de verschillende Linux-versies.


## <a name="known-issues"></a>Bekende problemen

Hier volgen enkele veelvoorkomende bekende problemen tijdens de upgrade:
- De Software Foundation-software (SFS) wordt na de upgrade van het besturings systeem verwijderd uit de SKU-klasse. U moet de compatibele SFS na de upgrade van het besturings systeem opnieuw installeren.
- Ethernet-kaart Stuur programma's (ENIC en FNIC) worden teruggedraaid naar een oudere versie. Na de upgrade moet u de compatibele versie van de Stuur Programma's opnieuw installeren.

## <a name="next-steps"></a>Volgende stappen
- Zie [back-up en herstellen](hana-overview-high-availability-disaster-recovery.md) voor het back-uptype van het besturings systeem I SKU-klasse.
- Raadpleeg de [back-up van het besturings systeem voor de type II sku's van Revision 3-stem pels](os-backup-type-ii-skus.md) voor de klasse type II SKU.
