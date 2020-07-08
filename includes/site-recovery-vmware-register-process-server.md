---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67534616"
---
1. Stel een Verbinding met extern bureaublad in op de computer waarop de proces server wordt uitgevoerd. 
2. Voer cspsconfigtool.exe uit om het hulp programma voor configuratie van Azure Site Recovery proces server te starten.
    - Het hulp programma wordt automatisch gestart bij de eerste keer dat u zich aanmeldt bij de proces server.
    - Als deze niet automatisch wordt geopend, klikt u op de snelkoppeling op het bureau blad.

3. Geef in **FQDN of IP van de configuratie server**de naam of het IP-adres op van de configuratie server waarmee de proces server moet worden geregistreerd.
4. Zorg ervoor dat in de **Configuratie server poort**443 is opgegeven. Dit is de poort waarop de configuratie server luistert naar aanvragen.
5. Geef bij **wachtwoordzin voor verbinding**de wachtwoordzin op die u hebt opgegeven bij het instellen van de configuratie server. De wachtwoordzin zoeken:
    -  Ga op de configuratie server naar de installatiemap van Site Recovery **\home\svssystems\bin \* *:
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - Voer de onderstaande opdracht uit om de huidige wachtwoordzin af te drukken:
    ```
    genpassphrase.exe -n
    ```

6. In **gegevensoverdracht poort**, tenzij u een aangepaste poort hebt opgegeven, wijzigt u de standaard waarde.

7. Klik op **Opslaan** de instellingen opslaan en registreer de proces server.

    
    ![De proces server registreren](./media/site-recovery-vmware-register-process-server/register-ps.png)
