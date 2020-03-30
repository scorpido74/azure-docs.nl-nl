---
title: Dynamics 365 Oplossingvoorbereiding
description: Kader voor het verpakken, installeren en verwijderen van componenten
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: ac1e4fa541e945f20904ced114a36b58d14585ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278583"
---
# <a name="dynamics-365-solution-preparation"></a>Dynamics 365 Oplossingvoorbereiding

Het Dynamics 365-oplossingssysteem is een framework voor het verpakken, installeren en verwijderen van componenten die specifieke bedrijfsfunctionaliteit bieden. Oplossingen worden gebruikt door ISV's en andere Microsoft Dynamics 365-partners om extensies die ze maken te distribueren.

Als u een bestaande Dynamics 365 (xRM) ISV bent, hebt u waarschijnlijk al een beheerde oplossing gemaakt en beschikt u over een solution.zip-bestand. Zorg er in uw oplossing voor dat de velden 'Weergavenaam' en 'Beschrijving' weergeven wat u wilt dat klanten zien. Deze worden weergegeven in het CRM Online Administration Center.

![CRMScreenShot1 CRMScreenShot1](media/CRMScreenShot1.png)

_**Let op:** In het pakketvoorbeeld dat volgt, gaan we ervan uit dat de naam van de oplossing "SampleSolution.zip" is_

Als u een nieuwe ISV bent, u hier meer informatie krijgen over het maken van een oplossing:[https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

Als uw oplossing ondersteunende gegevens vereist:

* De voorbeeldgegevens maken in uw testomgeving
* Gebruik het hulpprogramma voor configuratiemigratie om een schema te maken met vergelijkingsregels voor uw gegevens.
* Sla uw configuratieschema op met uw projectbestanden. U hebt dit later nodig als u uw configuratiegegevens bijwerkt.
* Exporteer uw configuratiegegevens. Zorg ervoor dat u het exportbestand een naam geeft die zinvol is voor uw export.
