---
title: Micro Focus Enter prise Server 4,0 uitvoeren in een docker-container op Azure Virtual Machines
description: Host uw IBM z/OS mainframe-workloads door micro focus Enter prise server uit te voeren in een docker-container op Azure Virtual Machines.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/26/2020
ms.locfileid: "61488338"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>Micro Focus Enter prise Server 4,0 uitvoeren in een docker-container in azure

U kunt Micro Focus Enter prise Server 4,0 uitvoeren in een docker-container in Azure. In deze zelf studie ziet u hoe. Het maakt gebruik van het Windows CICS (Customer Information Control System) acctdemo demonstratie voor Enter prise server.

Docker voegt portabiliteit en isolatie toe aan toepassingen. U kunt bijvoorbeeld een docker-installatie kopie van een virtuele Windows-machine exporteren om deze uit te voeren op een andere, of vanuit een opslag plaats naar een Windows-Server met docker. De docker-installatie kopie wordt uitgevoerd op de nieuwe locatie met dezelfde configuratie, zonder dat u de Enter prise-server hoeft te installeren. Het maakt deel uit van de installatie kopie. Overwegingen voor licenties zijn nog steeds van toepassing.

In deze zelf studie wordt het **Windows 2016 Data Center met containers** virtual machine (VM) geïnstalleerd vanuit Azure Marketplace. Deze VM bevat **docker 18.09.0**. De volgende stappen laten zien hoe u de container implementeert, uitvoert en er verbinding mee maakt met een 3270-emulator.

## <a name="prerequisites"></a>Vereisten

Bekijk de volgende vereisten voordat u aan de slag gaat:

- Een Azure-abonnement. Als u er nog geen hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

- De micro focus-software en een geldige licentie (of proef licentie). Als u een bestaande micro focus-klant bent, neemt u contact op met uw micro focus-vertegenwoordiger. Vraag anders [een proef versie](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)aan.

     > [!NOTE]
     > De docker-demonstratie bestanden zijn opgenomen in Enter prise Server 4,0. In deze zelf studie\_wordt\_de\_naam\_server dockerfiles 4,0 Windows. zip gebruikt. Ga naar de locatie waar u het installatie bestand voor de Enter prise-server hebt geopend of bezoek de *micro focus* om aan de slag te gaan.

- De documentatie voor [Enter prise server en Enter prise Developer](https://www.microfocus.com/documentation/enterprise-developer/#").

## <a name="create-a-vm"></a>Een virtuele machine maken

1. Beveilig de media van het bestand\_server\_dockerfiles\_4,0\_Windows. zip. Beveilig het licentie bestand ES-docker-Prod-XXXXXXXX. mflic (vereist om de docker-installatie kopieën te bouwen).

2. Maak de virtuele machine. Hiertoe opent u Azure Portal, selecteert u **een resource maken** in de linkerbovenhoek en filtert u op *Windows Server*. Selecteer in de resultaten **Windows Server 2016 Data Center – met containers**.

     ![Zoek resultaten Azure Portal](media/01-portal.png)

3. Als u de eigenschappen voor de virtuele machine wilt configureren, kiest u exemplaar Details:

    1. Kies een VM-grootte. Voor deze zelf studie kunt u overwegen een **standaard\_DS2 v2** -VM te gebruiken met 2 vcpu's en 7 GB geheugen.

    2. Selecteer de **regio** en de **resource groep** die u wilt implementeren.

    3. Gebruik de standaard instelling voor **beschikbaarheids opties**.

    4. Typ bij **gebruikers naam**het beheerders account dat u wilt gebruiken en het wacht woord.

    5. Controleer of **poort 3389 RDP** is geopend. Alleen deze poort moet openbaar worden blootgesteld, zodat u zich kunt aanmelden bij de VM. Accepteer vervolgens alle standaard waarden en klik op **beoordelen + maken**.

     ![Een deel venster voor een virtuele machine maken](media/container-02.png)

4. Wacht tot de implementatie is voltooid (een paar minuten). Er wordt een bericht vermeld waarin staat dat uw virtuele machine is gemaakt.

5. Klik op **Go to resource** om naar de Blade **overzicht** voor uw virtuele machine te gaan.

6. Klik aan de rechter kant op de knop **verbinden** . De opties **verbinding met virtuele machine maken** worden aan de rechter kant weer gegeven.

7. Klik op de knop **RDP-bestand downloaden** om het RDP-bestand te downloaden waarmee u kunt koppelen aan de virtuele machine.

8. Nadat het downloaden van het bestand is voltooid, opent u het en typt u de gebruikers naam en het wacht woord die u hebt gemaakt voor de virtuele machine.

     > [!NOTE]
     > Gebruik niet uw bedrijfs referenties om u aan te melden. (De RDP-client veronderstelt dat u deze kunt gebruiken. Dit is niet het geval.)

9.  Selecteer **meer opties**en selecteer vervolgens uw VM-referenties.

Op dit moment wordt de virtuele machine uitgevoerd en aangesloten via RDP. U bent aangemeld en is klaar voor de volgende stap.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Een sandbox-map maken en het zip-bestand uploaden

1.  Maak een map op de virtuele machine waar u de demo-en licentie bestanden kunt uploaden. Bijvoorbeeld **C:\\sandbox**.

2.  Upload **\_server\_dockerfiles\_4,0\_Windows. zip** en het bestand **es-docker-Prod-xxxxxxxx. mflic** naar de map die u hebt gemaakt.

3.  Pak de inhoud van het zip-bestand uit **naar\_de\_map\_dockerfiles\_4,0 Windows** die door het uitpak proces is gemaakt. Deze map bevat een Leesmij-bestand (als HTML-en txt-bestand) en twee submappen, **EnterpriseServer** en **voor beelden**.

4.  Kopieer **es-docker-Prod-xxxxxxxx. mflic** naar de map C\\:\\sandbox\_\_\_dockerfiles\_4,0 Windows\\EnterpriseServer en C:\\de sandbox\\\_server\_dockerfiles\_4,0\_Windows\\-voor\\beelden van CICS-directory's.

> [!NOTE]
> Zorg ervoor dat u het licentie bestand naar beide directory's kopieert. Ze zijn vereist voor de stap docker build om te controleren of de installatie kopieën op de juiste wijze zijn gelicentieerd.

## <a name="check-docker-version-and-create-base-image"></a>Docker-versie controleren en basis installatie kopie maken

> [!IMPORTANT]
> Het maken van de juiste docker-installatie kopie is een proces dat uit twee stappen bestaat. Maak eerst de basis installatie kopie voor Enter prise Server 4,0.Maak vervolgens een andere installatie kopie voor het x64-platform. Hoewel u een x86-installatie kopie (32-bits) kunt maken, gebruikt u de 64-bits installatie kopie.

1. Open een opdrachtprompt.

2. Controleer of docker is geïnstalleerd. Typ het volgende na de opdrachtprompt:

    ```
        docker version
    ```

     De versie was bijvoorbeeld 18.09.0 toen deze werd geschreven.

3. Als u de map wilt wijzigen, typt u **cd \sandbox\ ent_server_dockerfiles_4.0_windows \enterpriseserver**.

4. Typ **bld. bat IacceptEULA** om het bouw proces voor de oorspronkelijke basis installatie kopie te starten. Wacht enkele minuten totdat dit proces is uitgevoerd. In de resultaten ziet u de twee installatie kopieën die zijn gemaakt: één voor x64 en een voor x86:

     ![Opdrachtvenster weer geven van installatie kopieën](media/container-04.png)

5. Als u de laatste installatie kopie voor de CICS-demonstratie wilt maken, gaat u naar de CICS-map door **\\cd\_sandbox\\\\\\\_\_dockerfiles\_4,0 Windows-voor beelden CICS**te typen.

6. Als u de installatie kopie wilt maken, typt u **bld. bat x64**. Wacht een paar minuten totdat het proces is uitgevoerd en het bericht verschijnt dat de installatie kopie is gemaakt.

7. Typ **docker-installatie kopieën** om een lijst weer te geven van alle docker-installatie kopieën die op de virtuele machine zijn geïnstalleerd. Zorg ervoor dat er een van de **focussen/es-acctdemo** is.

     ![Opdrachtvenster met es-acctdemo-installatie kopie](media/container-05.png)

## <a name="run-the-image"></a>De installatie kopie uitvoeren 

1.  Als u Enter prise Server 4,0 en de acctdemo-toepassing wilt starten, typt u bij de opdracht prompt het volgende:

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  Installeer een 3270-Terminal Emulator zoals [x3270](http://x3270.bgp.nu/) en gebruik deze om via poort 9040 een koppeling toe te voegen aan de installatie kopie die wordt uitgevoerd.

3.  Haal het IP-adres van de acctdemo-container op, zodat docker kan fungeren als een DHCP-server voor de containers die worden beheerd:

    1.  Haal de ID van de container die wordt uitgevoerd op. Typ **docker PS** bij de opdracht prompt en noteer de id (**22a0fe3159d0** in dit voor beeld). Sla het bestand op voor de volgende stap.

    2.  Als u het IP-adres voor de acctdemo-container wilt ophalen, gebruikt u de container-ID uit de vorige stap als volgt:

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       Bijvoorbeeld:

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. Noteer het IP-adres voor de acctdemo-installatie kopie. Het adres in de volgende uitvoer is bijvoorbeeld 172.19.202.52.

     ![Opdrachtvenster met IP-adres](media/container-06.png)

5. Koppel de installatie kopie met behulp van de emulator. Configureer de emulator om het adres van de acctdemo-installatie kopie en poort 9040 te gebruiken. Hier is het **172.19.202.52:9040**. Dit is vergelijkbaar. Het scherm **aanmelding naar CICS** wordt geopend.

    ![Aanmelding naar CICS-scherm](media/container-07.png)

6. Meld u aan bij de CICS-regio door **SYSAD** voor de **gebruikers-id** en **SYSAD** voor het **wacht woord**in te voeren.

7. Wis het scherm met behulp van de keymap van de emulator. Selecteer voor x3270 de menu optie **keymap** .

8. Als u de acctdemo-toepassing wilt starten, typt u **acct**. Het eerste scherm voor de toepassing wordt weer gegeven.

     ![Account demo scherm](media/container-08.png)

9. Experimenteer met typen weergave accounts. Typ bijvoorbeeld **D** voor de aanvraag en **11111** voor het **account**. Andere account nummers die moeten worden geprobeerd, zijn 22222, 33333, enzovoort.

     ![Account demo scherm](media/container-09.png)

10. Als u de beheer console van de Enter prise-server wilt weer geven, gaat u naar de opdracht prompt en typt u **Start http: 172.19.202.52:86**

     ![Enter prise server-beheer console](media/container-010.png)

Dat is alles. Nu voert u een CICS-toepassing uit en beheert u deze in een docker-container.

## <a name="next-steps"></a>Volgende stappen

- [Micro Focus Enter prise Server 4,0 en Enter prise Developer 4,0 op Azure installeren](./set-up-micro-focus-azure.md)
- [Migratie van mainframetoepassingen](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
