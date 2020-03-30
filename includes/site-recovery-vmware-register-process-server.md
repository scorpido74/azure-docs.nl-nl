---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67534616"
---
1. Een verbinding met extern bureaublad tot stand brengen met de machine waarop de processerver wordt uitgevoerd. 
2. Voer cspsconfigtool.exe uit om het configuratieprogramma azure site recovery process server te starten.
    - De tool wordt automatisch gestart wanneer u zich voor het eerst aanmeldt bij de processerver.
    - Als deze niet automatisch wordt geopend, klikt u op de snelkoppeling op het bureaublad.

3. Geef in **Configuration server FQDN of IP**de naam of het IP-adres op van de configuratieserver waarmee de processerver moet worden geregistreerd.
4. Zorg er in **configuratieserverpoort**voor dat 443 is opgegeven. Dit is de poort waarop de configuratieserver naar aanvragen luistert.
5. Geef in **Verbindingswachtwoord de**wachtwoordzin op die u hebt opgegeven bij het instellen van de configuratieserver. Ga als volgende voor het volgende:
    -  Ga op de configuratieserver naar de installatiemap Siteherstel **\home\svssystems\bin:\**
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - Voer de onderstaande opdracht uit om de huidige wachtwoordzin uit te printen:
    ```
    genpassphrase.exe -n
    ```

6. Laat in **de poort gegevensoverdracht**de standaardwaarde achter, tenzij u een aangepaste poort hebt opgegeven.

7. Klik **op Opslaan** van de instellingen en registreer de processerver.

    
    ![De processerver registreren](./media/site-recovery-vmware-register-process-server/register-ps.png)
