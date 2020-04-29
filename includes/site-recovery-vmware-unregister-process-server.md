---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67176563"
---
Volg de stappen voor uw specifieke omstandigheden.

### <a name="unregister-a-connected-process-server"></a>Registratie van een verbonden proces server ongedaan maken

1. Maak een externe verbinding met de proces server als beheerder.
2. Open **Program ma's > een programma verwijderen**in het **configuratie scherm**.
3. Verwijder het programma **Microsoft Azure site Recovery Mobility service/Master doel server**.
4. Verwijder het programma **Microsoft Azure site Recovery configuratie/proces server**.
5. Nadat de Program ma's in stap 3 en 4 zijn verwijderd, verwijdert u **Microsoft Azure site Recovery afhankelijkheden van de configuratie/proces server**.

### <a name="unregister-a-disconnected-process-server"></a>Registratie van een niet-verbonden proces server ongedaan maken

Gebruik deze stappen alleen als er geen manier is om de computer waarop de proces server is geïnstalleerd, opnieuw in te stellen.

1. Meld u als beheerder aan bij de configuratie server.
2. Open een opdracht prompt met beheerders rechten en blader `%ProgramData%\ASR\home\svsystems\bin`naar.
3. Voer deze opdracht uit om een lijst met een of meer proces servers op te halen.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. Nee: het serie nummer van de proces server.
    - IP/naam: het IP-adres en de naam van de computer waarop de proces server wordt uitgevoerd.
    - Heartbeat: laatste heartbeat van de proces Server computer.
    ![Registratie ongedaan maken-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. Geef het serie nummer op van de proces server waarvoor u de registratie ongedaan wilt maken.
5. Bij het ongedaan maken van de registratie van een proces server worden alle details van het systeem verwijderd en wordt het volgende bericht weer gegeven: de registratie van de **Server naam is ongedaan gemaakt> (server-IP-adres)**

