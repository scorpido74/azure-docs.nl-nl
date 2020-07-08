---
title: bestand opnemen
description: bestand opnemen
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: include
ms.date: 02/28/2019
ms.author: mayg
ms.custom: include file
ms.openlocfilehash: 7c682105113dac7c1d457489cf926210ead77993
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67175749"
---
1. Voer het installatiebestand voor de geïntegreerde Setup uit.
2. In **voordat u begint**, selecteert u **de configuratie server en proces server installeren**.

    ![Voordat u begint](./media/site-recovery-add-configuration-server/combined-wiz1.png)

3. Klik bij **Licentievoorwaarden voor software van derden** op **Ik ga akkoord** om MySQL te downloaden en te installeren.

    ![Software van derden](./media/site-recovery-add-configuration-server/combined-wiz2.png)
4. Selecteer bij **Registratie** de registratiesleutel die u hebt gedownload uit de kluis.

    ![Registratie](./media/site-recovery-add-configuration-server/combined-wiz3.png)
5. Geef bij **Internetinstellingen** op hoe de provider die op de configuratieserver wordt uitgevoerd, via internet verbinding moet maken met Azure Site Recovery. Zorg ervoor dat u de vereiste Url's hebt toegestaan.

    - Als u verbinding wilt maken met de proxy die momenteel op de computer is ingesteld, selecteert u **verbinding maken met Azure site Recovery met behulp van een proxy server**.
    - Als u wilt dat de provider rechtstreeks verbinding maakt, selecteert u **rechtstreeks verbinding maken met Azure site Recovery zonder proxy server**.
    - Als voor de bestaande proxy verificatie is vereist of als u een aangepaste proxy voor de provider verbinding wilt gebruiken, selecteert u **verbinding maken met aangepaste proxy instellingen**en geeft u het adres, de poort en de referenties op.
     ![Firewall](./media/site-recovery-add-configuration-server/combined-wiz4.png)
6. Tijdens Setup wordt in **Controle op vereisten** gecontroleerd of de installatie kan worden uitgevoerd. Als er een waarschuwing wordt weergegeven over **Synchronisatiecontrole voor algemene tijd**, moet u controleren of de tijd op de systeemklok (instellingen voor **datum en tijd**) overeenkomt met de tijdzone.

    ![Vereisten](./media/site-recovery-add-configuration-server/combined-wiz5.png)
7. Maak bij **MySQL-configuratie** referenties voor aanmelden bij de MySQL-serverinstantie die is geïnstalleerd.

    ![MySQL](./media/site-recovery-add-configuration-server/combined-wiz6.png)
8. Selecteer in **omgevings Details**Nee als u Azure stack vm's of fysieke servers wilt repliceren. 
9. Selecteer bij **Installatielocatie** waar u de binaire bestanden wilt installeren en de cache wilt opslaan. Het station dat u selecteert, moet ten minste 5 GB vrije schijfruimte bevatten, maar wij raden u aan een cachestation te gebruiken met minstens 600 GB vrije ruimte.

    ![Installatielocatie](./media/site-recovery-add-configuration-server/combined-wiz8.png)
10. Selecteer in **netwerk selectie**eerst de NIC die de ingebouwde proces server gebruikt voor detectie en push-installatie van Mobility service op bron machines, en selecteer vervolgens de NIC die door de configuratie server wordt gebruikt voor de connectiviteit met Azure. Poort 9443 is de standaardpoort voor het verzenden en ontvangen van replicatieverkeer, maar u kunt dit poortnummer aanpassen aan de vereisten van de omgeving. Naast poort 9443 wordt ook poort 443 geopend. Deze wordt door een webserver gebruikt om replicatiebewerkingen in te delen. Gebruik poort 443 niet voor het verzenden of ontvangen van replicatie verkeer.

    ![Netwerk selecteren](./media/site-recovery-add-configuration-server/combined-wiz9.png)


11. Lees de informatie bij **Samenvatting** en klik op **Installeren**. Wanneer de installatie is voltooid, wordt er een wachtwoordzin gegenereerd. U hebt deze nodig bij het inschakelen van de replicatie. Kopieer de wachtwoordzin daarom en bewaar deze op een veilige locatie.

    ![Samenvatting](./media/site-recovery-add-configuration-server/combined-wiz10.png)

Nadat de registratie is voltooid, wordt de server weer gegeven op de Blade **instellingen**  >  **servers** in de kluis.
