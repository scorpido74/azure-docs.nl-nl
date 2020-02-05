---
title: Toegang tot Azure VMware-oplossingen (AVS)-Portal
description: Hierin wordt beschreven hoe u de Azure Portal van Azure VMware-oplossingen (AVS) kunt gebruiken
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0b0601b9d2cf505aa5b25e10875338352cab771
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015947"
---
# <a name="access-azure-vmware-solutions-avs-from-the-azure-portal"></a>Toegang tot Azure VMware-oplossingen (AVS) vanuit de Azure Portal

Eenmalige aanmelding wordt ondersteund voor toegang tot de AVS-Portal. Nadat u zich hebt aangemeld bij de Azure Portal, kunt u toegang krijgen tot de AVS-Portal zonder u opnieuw aan te melden. De eerste keer dat u de AVS-Portal opent, wordt u gevraagd om de [AVS-service autorisatie](#consent-to-avs-service-authorization-application) toepassing te autoriseren. Autorisatie is een eenmalige actie.

## <a name="before-you-begin"></a>Voordat u begint

Gebruikers met rollen van de ingebouwde **eigenaar** en **Inzender** hebben toegang tot de AVS-Portal. De functies moeten worden geconfigureerd voor de resource groep waarin de AVS-service is geïmplementeerd. De functies kunnen ook worden geconfigureerd op het object AVS-service. Zie het artikel [roltoewijzingen weer geven](https://docs.microsoft.com/azure/role-based-access-control/check-access) voor meer informatie over het controleren van uw rol. Alleen gebruikers met ingebouwde rollen **eigenaar** en **Inzender** hebben toegang tot de AVS-Portal. De rollen moeten worden geconfigureerd in het abonnement. Zie het artikel [roltoewijzingen weer geven](https://docs.microsoft.com/azure/role-based-access-control/check-access) voor meer informatie over het controleren van uw rol.

Als u aangepaste rollen gebruikt, moet de rol onder ```Actions```een van de volgende bewerkingen uitvoeren.  Zie [aangepaste rollen voor Azure-resources](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)voor meer informatie over aangepaste rollen. Als een van de bewerkingen deel uitmaakt van ```NotActions```, heeft de gebruiker geen toegang tot de AVS-Portal. 

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-avs-portal"></a>Toegang tot de AVS-Portal

1. Selecteer **Alle services**.

2. Zoeken naar **AVS-Services**.

3. Selecteer de AVS-service waarop u uw Privécloud wilt maken.

4. Klik op de pagina **overzicht** op **Ga naar de AVS-Portal**. Als u de AVS-Portal vanuit de Azure Portal voor de eerste keer opent, wordt u gevraagd om de toepassing van de [AVS-service autorisatie](#consent-to-avs-service-authorization-application) te autoriseren. 

    ![AVS-Portal starten](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Als u een privé-Cloud bewerking (zoals het maken of uitbreiden van een Privécloud) rechtstreeks vanuit het Azure Portal selecteert, wordt de AVS-portal geopend op de aangegeven pagina.

Selecteer in de AVS-Portal de optie **Home** in het menu aan de zijkant om samenvattings informatie over de privécloud van uw AVS weer te geven. De resources en capaciteit van uw AVS-Privécloud worden weer gegeven, samen met waarschuwingen en taken waarvoor aandacht is vereist. Voor algemene taken klikt u op de benoemde pictogrammen boven aan de pagina.

![Start pagina](media/cloudsimple-portal-home.png)

## <a name="consent-to-avs-service-authorization-application"></a>Toestemming voor de toepassing van de AVS-service autorisatie

Voor het starten van de AVS-Portal vanuit de Azure Portal voor de eerste keer moet u toestemming geven voor de toepassing AVS-service autorisatie. Selecteer **accepteren** om aangevraagde machtigingen te verlenen en toegang te krijgen tot de AVS-Portal.

![Toestemming voor AVS-service autorisatie-beheerders](media/cloudsimple-azure-consent.png)

Als u globale beheerders rechten hebt, kunt u toestemming geven voor uw organisatie. Selecteer **toestemming namens uw organisatie**.

![Toestemming voor de verificatie van de AVS-service-globale beheerder](media/cloudsimple-azure-consent-global-admin.png)

Als uw machtigingen geen toegang tot de AVS-Portal toestaan, neemt u contact op met de globale beheerder van uw Tenant om de vereiste machtigingen te verlenen. Een globale beheerder kan namens uw organisatie toestemming geven.

![Toestemming voor de verificatie van de AVS-service: vereist beheerders](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken van een privécloud](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Meer informatie over het [configureren van een privécloud](quickstart-create-private-cloud.md)
