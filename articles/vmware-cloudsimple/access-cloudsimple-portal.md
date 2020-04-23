---
title: Toegang tot Azure VMware-oplossing door CloudSimple - Portal
description: Hierin wordt beschreven hoe u vanuit de Azure-portal de VMware Solution by CloudSimple-portal opent
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 91d3dd2eee6f771df23b72eae7a33122c42f1690
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869337"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>Toegang tot de VMware-oplossing per CloudSimple-portal vanuit de Azure-portal

Eenmalige aanmelding wordt ondersteund voor toegang tot de CloudSimple-portal. Nadat u zich hebt aangemeld bij de Azure-portal, hebt u toegang tot de CloudSimple-portal zonder u opnieuw aan te melden. De eerste keer dat u toegang hebt tot de CloudSimple-portal wordt u gevraagd de [cloudsimple-serviceautorisatietoepassing te autoriseren.](#consent-to-cloudsimple-service-authorization-application)  Autorisatie is een eenmalige actie.

## <a name="before-you-begin"></a>Voordat u begint

Gebruikers met ingebouwde **rollen voor eigenaren** en bijdragen **kunnen** toegang krijgen tot cloudsimple-portal.  De rollen moeten worden geconfigureerd in de resourcegroep waarin cloudsimple-service wordt geïmplementeerd.  De rollen kunnen ook worden geconfigureerd op het cloudsimple-serviceobject.  Zie Artikel [Roltoewijzingen weergeven](https://docs.microsoft.com/azure/role-based-access-control/check-access) voor meer informatie over het controleren van uw rol. Alleen gebruikers met ingebouwde **rollen voor eigenaren** en **inzenderen** hebben toegang tot de CloudSimple-portal.  De rollen moeten op het abonnement worden geconfigureerd.  Zie Artikel [Roltoewijzingen weergeven](https://docs.microsoft.com/azure/role-based-access-control/check-access) voor meer informatie over het controleren van uw rol.

Als u aangepaste rollen gebruikt, moet de rol ```Actions```een van de volgende bewerkingen hebben onder .  Zie Aangepaste rollen voor [Azure-resources voor](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)meer informatie over aangepaste rollen.  Als een van de bewerkingen deel uitmaakt van ```NotActions```, heeft de gebruiker geen toegang tot de CloudSimple-portal.

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Toegang tot de CloudSimple-portal

1. Selecteer **Alle services**.

2. Zoek naar **CloudSimple Services**.

3. Selecteer de CloudSimple-service waarop u uw Private Cloud wilt maken.

4. Klik **op** de pagina Overzicht op **Ga naar de CloudSimple-portal**.  Als u de CloudSimple-portal voor de eerste keer opent vanuit de Azure-portal, wordt u gevraagd de [cloudsimple-serviceautorisatietoepassing te](#consent-to-cloudsimple-service-authorization-application) autoriseren. 

    ![CloudSimple-portal starten](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Als u een Private Cloud-bewerking (zoals het maken of uitbreiden van een Private Cloud) rechtstreeks vanuit de Azure-portal selecteert, wordt de Portal CloudSimple geopend naar de aangegeven pagina.

Selecteer in de CloudSimple-portal **Start** in het zijmenu om overzichtsinformatie over uw Private Clouds weer te geven. De resources en capaciteit van uw Private Clouds worden weergegeven, samen met waarschuwingen en taken die aandacht vereisen. Klik voor veelvoorkomende taken op de benoemde pictogrammen boven aan de pagina.

![Startpagina](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>Toestemming voor cloudsimple-serviceautorisatietoepassing

Voor het voor het eerst starten van de CloudSimple-portal vanuit de Azure-portal is uw toestemming nodig voor de CloudSimple Service Authorization-toepassing.  Selecteer **Accepteren** om gevraagde machtigingen te verlenen en toegang te krijgen tot de CloudSimple-portal.

![Toestemming voor CloudSimple Service Authorization - beheerders](media/cloudsimple-azure-consent.png)

Als u een globale beheerdersbevoegdheid hebt, u toestemming geven voor uw organisatie.  Selecteer **Toestemming namens uw organisatie**.

![Toestemming voor CloudSimple Service Authorization - globale beheerder](media/cloudsimple-azure-consent-global-admin.png)

Als uw machtigingen geen toegang tot de CloudSimple-portal toestaan, neemt u contact op met de globale beheerder van uw tenant om vereiste machtigingen te verlenen.  Een globale beheerder kan namens uw organisatie toestemming geven.

![Toestemming voor CloudSimple Service Authorization - vereist beheerders](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het maken van een private cloud](https://docs.microsoft.com/azure/vmware-cloudsimple/create-private-cloud/)
* Meer informatie over [het configureren van een private cloudomgeving](quickstart-create-private-cloud.md)
