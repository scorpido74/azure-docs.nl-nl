---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 40ba5a935e78cd75c4fcd7729e44f1cdf6c2859b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75772917"
---
Als u problemen ondervindt tijdens het uitvoeren van bewerkingen op galerieën met gedeelde installatiekopieën, definities van installatiekopieën en versies van installatiekopieën, voert u de mislukte opdracht opnieuw uit in de foutopsporingsmodus. De foutopsporingsmodus wordt geactiveerd door de **foutopsporingsschakelaar** met CLI en de **foutopsporingsschakelaar** met PowerShell door te geven. Volg dit document om de fouten op te lossen nadat u de fout hebt gevonden.


## <a name="unable-to-create-a-shared-image-gallery"></a>Kan geen galerie met gedeelde installatiekopieën maken

Mogelijke oorzaken:

*De naam van de galerie is ongeldig.*

Toegestane tekens voor galerienaam zijn hoofdletters, cijfers, stippen en perioden. De naam van de galerie mag geen streepjes bevatten. Wijzig de naam van de galerie en probeer het opnieuw. 

*De naam van de galerie is niet uniek in uw abonnement.*

Kies een andere galerijnaam en probeer het opnieuw.


## <a name="unable-to-create-an-image-definition"></a>Kan geen definitie voor de installatiekopie maken 

Mogelijke oorzaken:

*afbeeldingsdefinitienaam is ongeldig.*

Toegestane tekens voor afbeeldingsdefinitie zijn hoofdletters of kleine letters, cijfers, stippen, streepjes en perioden. Wijzig de naam van de afbeeldingsdefinitie en probeer het opnieuw.

*De verplichte eigenschappen voor het maken van een afbeeldingsdefinitie worden niet ingevuld.*

De eigenschappen zoals naam, uitgever, aanbieding, sku en OS-type zijn verplicht. Controleer of alle eigenschappen worden doorgegeven.

Zorg ervoor dat de **OSType**, Linux of Windows, van de afbeeldingsdefinitie hetzelfde is als de bronbeheerde afbeelding die u gebruikt om de afbeeldingsversie te maken. 


## <a name="unable-to-create-an-image-version"></a>Kan geen versie voor de installatiekopie maken 

Mogelijke oorzaken:

*De naam van de afbeeldingsversie is ongeldig.*

Toegestane tekens voor de afbeeldingsversie zijn getallen en perioden. Getallen moeten zich binnen het bereik van een 32-bits geheel getal bevinden. Formaat: *MajorVersion.MinorVersion.Patch*. Wijzig de naam van de afbeeldingsversie en probeer het opnieuw.

*Bronbeheerde afbeelding waaruit de afbeeldingsversie wordt gemaakt, wordt niet gevonden.* 

Controleer of de bronafbeelding bestaat en zich in hetzelfde gebied bevindt als de afbeeldingsversie.

*De beheerde afbeelding is nog niet klaar met inrichten.*

Controleer of de inrichtingsstatus van de door de bron **beheerde**afbeelding is geslaagd .

*De lijst met doelregio's bevat niet het brongebied.*

De lijst met doelregio's moet het brongebied van de afbeeldingsversie bevatten. Zorg ervoor dat u het brongebied hebt opgenomen in de lijst met doelregio's waaraan Azure uw afbeeldingsversie wilt repliceren.

*Replicatie naar alle doelgebieden die niet zijn voltooid.*

Gebruik de vlag **Replicatiestatus uitvouwen** om te controleren of de replicatie naar alle opgegeven doelgebieden is voltooid. Zo niet, wacht dan tot 6 uur voordat de taak is voltooid. Als dit niet lukt, voert u de opdracht opnieuw uit om de afbeeldingsversie te maken en te repliceren. Als er veel doelgebieden zijn waarin de afbeeldingsversie wordt gerepliceerd, u overwegen de replicatie gefaseerd uit te brengen.

## <a name="unable-to-create-a-vm-or-a-scale-set"></a>Kan geen VM of schaalset maken 

Mogelijke oorzaken:

*De gebruiker die een vm- of virtuele machineschaalset probeert te maken, heeft geen leestoegang tot de afbeeldingsversie.*

Neem contact op met de eigenaar van het abonnement en vraag hen om leestoegang te geven tot de afbeeldingsversie of de bovenliggende bronnen (zoals de gedeelde afbeeldingsgalerie of afbeeldingsdefinitie) via [Role Based Access Control](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC). 

*De afbeeldingsversie wordt niet gevonden.*

Controleer of het gebied waarin u een vm- of virtuele machineschaal probeert te maken, is opgenomen in de lijst met doelgebieden van de afbeeldingsversie. Als het gebied al in de lijst met doelgebieden staat, controleert u of de replicatietaak is voltooid. U de vlag **-Replicatiestatus** gebruiken om te controleren of de replicatie naar alle opgegeven doelgebieden is voltooid. 

*Het maken van de vm- of virtuele machineschaalset duurt lang.*

Controleer of het **OSType** van de afbeeldingsversie waarvan u de vm- of virtuele machineschaalset probeert te maken, dezelfde **OSType** heeft als de bronbeheerde afbeelding die u hebt gebruikt om de afbeeldingsversie te maken. 

## <a name="unable-to-share-resources"></a>Kan geen resources delen

Het delen van bronnen voor gedeelde afbeeldingen, afbeeldingsdefinitie en bronnen van afbeeldingsversies tussen abonnementen is ingeschakeld met [RBAC (Role-Based Access Control).](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) 

## <a name="replication-is-slow"></a>Replicatie is traag

Gebruik de vlag **Replicatiestatus uitvouwen** om te controleren of de replicatie naar alle opgegeven doelgebieden is voltooid. Zo niet, wacht dan tot 6 uur voordat de taak is voltooid. Als dit niet lukt, activeert u de opdracht opnieuw om de afbeeldingsversie te maken en te repliceren. Als er veel doelgebieden zijn waarin de afbeeldingsversie wordt gerepliceerd, u overwegen de replicatie gefaseerd uit te brengen.

## <a name="azure-limits-and-quotas"></a>Limieten en quota in Azure 

[Azure-limieten en -quota](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) zijn van toepassing op alle bronnen voor gedeelde afbeeldingen, afbeeldingsdefinitie en afbeeldingsversiebronnen. Zorg ervoor dat u binnen de limieten voor uw abonnementen bent. 



