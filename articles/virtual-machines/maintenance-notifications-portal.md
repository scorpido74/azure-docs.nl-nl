---
title: De portal gebruiken voor onderhoudsmeldingen
description: Bekijk onderhoudsmeldingen voor virtuele machines die in Azure worden uitgevoerd en start selfserviceonderhoud met behulp van de portal.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 46fcc825ac49f0181ac74e9c3e2deaea577f3329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77115734"
---
# <a name="handling-planned-maintenance-notifications-using-the-portal"></a>Geplande onderhoudsmeldingen afhandelen via de portal

**Dit artikel is van toepassing op virtuele machines met zowel Linux als Windows.**

Zodra een [geplande onderhoudsgolf](maintenance-notifications.md) is gepland, u controleren op een lijst van virtuele machines die worden beïnvloed. 

U de Azure-portal gebruiken en zoeken naar VM's die zijn gepland voor onderhoud.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Klik in de linkernavigatie op **Virtuele machines**.

3. Selecteer in het deelvenster Virtuele machines de knop **Kolommen bewerken** om de lijst met beschikbare kolommen te openen.

4. Selecteer en voeg de volgende kolommen toe:

   **Onderhoudsstatus**: geeft de onderhoudsstatus van de VM weer. Hieronder volgen de potentiële waarden:
      
      | Waarde | Beschrijving |
      |-------|-------------|
      | Nu opstarten | De VM bevindt zich in het selfservice onderhoudsvenster waarmee u het onderhoud zelf starten. Zie hieronder hoe u het onderhoud van uw VM starten. | 
      | Gepland | Er is onderhoud gepland voor uw virtuele machine, zonder mogelijkheid dit onderhoud zelf te starten. U het onderhoudsvenster leren door het venster Onderhoud - Gepland in deze weergave te selecteren of door op de vm te klikken. | 
      | Reeds bijgewerkt | Uw VM is al bijgewerkt en er is op dit moment geen verdere actie vereist. | 
      | Later opnieuw proberen | U bent gestart met onderhoud zonder succes. U op een later tijdstip gebruik maken van de selfservice onderhoudsoptie. | 
      | Probeer het nu opnieuw | U een eerder niet-mislukt zelfgeïnitieerd onderhoud opnieuw proberen. | 
      | - | Uw VM maakt geen deel uit van een geplande onderhoudsgolf. |
      

   **Onderhoud - Selfservicevenster**: toont het tijdvenster wanneer u zelf onderhoud aan uw VM's starten.
   
   **Onderhoud - Gepland venster:** toont het tijdvenster wanneer Azure uw VM onderhoudt om het onderhoud te voltooien. 



## <a name="notification-and-alerts-in-the-portal"></a>Meldingen en waarschuwingen in de portal

Azure communiceert een planning voor gepland onderhoud door een e-mail te sturen naar de eigenaar van het abonnement en de groep mede-eigenaren. U extra geadresseerden en kanalen aan deze communicatie toevoegen door waarschuwingen voor Azure-activiteitenlogboeken te maken. Zie [Waarschuwingen voor activiteitenlogboeken maken op servicemeldingen voor](../azure-monitor/platform/alerts-activity-log-service-notifications.md)meer informatie.

Zorg ervoor dat u het **gebeurtenistype** instelt als **gepland onderhoud**en **Services** als **virtuele machineschaalsets** en/of **virtuele machines**.

## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Onderhoud op uw vm starten vanuit de portal

Terwijl u naar de VM-details kijkt, u meer onderhoudsgerelateerde details zien.  
Boven aan de weergave VM-details wordt een nieuw meldingslint toegevoegd als uw vm is opgenomen in een geplande onderhoudsgolf. Daarnaast wordt een nieuwe optie toegevoegd om het onderhoud waar mogelijk te starten. 


Klik op de onderhoudsmelding voor de onderhoudspagina met meer informatie over het geplande onderhoud. Van daaruit u het onderhoud aan uw VM **starten.**

Zodra u met het onderhoud begint, wordt uw virtuele machine onderhouden en wordt de onderhoudsstatus bijgewerkt om het resultaat binnen enkele minuten weer te geven.

Als u het selfservicevenster hebt gemist, u het venster nog steeds zien wanneer uw vm wordt onderhouden door Azure. 


## <a name="next-steps"></a>Volgende stappen

U ook gepland onderhoud afhandelen met de [Azure CLI](maintenance-notifications-cli.md) of [PowerShell.](maintenance-notifications-powershell.md)