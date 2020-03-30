---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176563"
---
Volg de stappen voor uw specifieke omstandigheden.

### <a name="unregister-a-connected-process-server"></a>Het registreren van een verbonden processerver uitschrijven

1. Een externe verbinding met de processerver tot stand brengen als beheerder.
2. Open in het **Configuratiescherm** **Programma's > Een programma verwijderen**.
3. Verwijder het programma **Microsoft Azure Site Recovery Mobility Service/Master Target Server**.
4. Verwijder het programma **Microsoft Azure Site Recovery Configuration/Process Server**.
5. Nadat de programma's in stap 3 en 4 zijn verwijderd, verwijdert u **de afhankelijkheden van Microsoft Azure Site Recovery Configuration/Process Server**.

### <a name="unregister-a-disconnected-process-server"></a>Het registreren van een verbroken processerver uitschrijven

Gebruik deze stappen alleen als er geen manier is om de machine waarop de processerver is geïnstalleerd nieuw leven in te blazen.

1. Meld u aan als beheerder bij de configuratieserver.
2. Open een opdrachtprompt voor `%ProgramData%\ASR\home\svsystems\bin`beheer en blader naar .
3. Voer deze opdracht uit om een lijst met een of meer processervers te krijgen.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. Nee: het serienummer van de processerver.
    - IP/Naam: het IP-adres en de naam van de machine waarop de processerver wordt uitgevoerd.
    - Heartbeat: Laatste hartslag van de processervermachine.
    ![Kassa-cmd uitschrijven](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. Geef het serienummer op van de processerver die u wilt uitschrijven.
5. Als u een processerver uitschrijft, worden alle gegevens van het systeem verwijderd en wordt het bericht weergegeven: **> met niet-geregistreerde servernaam (server-IP-adres)**

