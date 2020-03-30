---
title: Windows Virtual Desktop-beheertool - Azure
description: Problemen met het Windows Virtual Desktop-beheerprogramma oplossen.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 11/09/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9d07ba42e83d9eec071ab047e9e1e92bac1f1411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127484"
---
# <a name="troubleshoot-the-windows-virtual-desktop-management-tool"></a>Problemen met het beheerprogramma voor Windows Virtual Desktop oplossen

In dit artikel worden problemen beschreven die kunnen optreden tijdens het implementeren van het Windows Virtual Desktop-beheerprogramma en hoe u deze oplossen.

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>Fout: Beheertoolservices geconfigureerd, maar geautomatiseerde installatie mislukt

Wanneer u services voor het beheerprogramma hebt ingesteld, maar de automatische installatie mislukt, ziet u dit foutbericht:

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

Dit betekent meestal een van de volgende twee dingen:

- De gebruiker heeft machtigingen voor de eigenaar op zijn abonnement en globale beheerder op tenantniveau, maar kan zich niet aanmelden bij Azure.
- De accountinstellingen van de gebruiker hebben multi-factor authenticatie ingeschakeld.

Om dit op te lossen:

1. Controleer of de gebruiker die u hebt gemaakt voor de Azure Active Directory User Principal Name het abonnementsniveau 'Inzender' heeft.
2. Meld u aan bij <portal.azure.com> met het UPN-account om de accountinstellingen te controleren en ervoor te zorgen dat meervoudige verificatie niet is ingeschakeld. Als het is ingeschakeld, zet het uit.
3. Ga naar de pagina Met toestemming voor virtuele bureaubladen van Windows en zorg ervoor dat de server en client-apps toestemming hebben.
4. Controleer de [zelfstudie van een beheertool](manage-resources-using-ui.md) als het probleem wordt voortgezet en implementeer het hulpprogramma opnieuw.

## <a name="error-job-with-specified-id-already-exists"></a>Fout: taak met opgegeven id bestaat al

Als uw gebruiker het foutbericht 'Taak met opgegeven id al bestaat' ziet, is dat omdat hij geen unieke naam heeft opgegeven in de parameter 'Naam van toepassingen' bij het implementeren van de sjabloon.

Om dit op te lossen, herschikt u het beheergereedschap opnieuw met de parameter 'Toepassingsnaam' gevuld.

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>Gevraagde toestemmingsprompt bij het openen van beheertool

Wanneer u het beheergereedschap implementeert, wordt de toestemmingsprompt mogelijk niet meteen geopend. Dit betekent dat het laden van de Azure Web-app-service langer duurt dan normaal. De prompt moet worden weergegeven nadat Azure Web is geladen.

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>De gebruiker kan de beheertool niet implementeren in de regio Oost-VS

Als een klant de regio instelt op Oost-VS, kan deze de beheertool niet implementeren.

Implementeer de beheertool in een andere regio om dit op te lossen. Het opnieuw implementeren van het gereedschap in een andere regio mag geen invloed hebben op de gebruikerservaring.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over escalatietracks bij [overzicht van probleemoplossing, feedback en ondersteuning](troubleshoot-set-up-overview.md).
- Meer informatie over het melden van problemen met Windows Virtual Desktop-hulpprogramma's bij [ARM-sjablonen voor Extern bureaublad-services](https://github.com/Azure/RDS-Templates/blob/master/README.md).
- Zie [Overzicht, feedback en ondersteuning](troubleshoot-set-up-overview.md)voor een overzicht van probleemoplossing voor het oplossen van problemen met Windows Virtual Desktop en de escalatietracks.
- Zie Een beheertool implementeren voor meer informatie over het implementeren van de [beheertool.](manage-resources-using-ui.md)