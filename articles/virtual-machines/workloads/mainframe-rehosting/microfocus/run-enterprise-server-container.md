---
title: Micro Focus Enterprise Server 4.0 uitvoeren in een Dockercontainer op Azure Virtual Machines
description: Host uw IBM z/OS mainframeworkloads opnieuw door Micro Focus Enterprise Server uit te voeren in een Docker-container op Azure Virtual Machines.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61488338"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>Micro Focus Enterprise Server 4.0 uitvoeren in een DockerContainer op Azure

U Micro Focus Enterprise Server 4.0 uitvoeren in een Docker-container op Azure. Deze tutorial laat je zien hoe. Het maakt gebruik van de Windows CICS (Customer Information Control System) acctdemo demonstratie voor Enterprise Server.

Docker voegt draagbaarheid en isolatie toe aan toepassingen. U bijvoorbeeld een Docker-afbeelding exporteren van de ene Windows-vm om op een andere te draaien of vanuit een opslagplaats naar een Windows-server met Docker. De Docker-afbeelding wordt uitgevoerd op de nieuwe locatie met dezelfde configuratie, zonder enterprise server te hoeven installeren. Het is een deel van het beeld. Licentieoverwegingen zijn nog steeds van toepassing.

In deze zelfstudie wordt de virtuele machine **Van Windows 2016 Datacenter with Containers** (VM) geïnstalleerd vanuit de Azure Marketplace. Deze VM bevat **Docker 18.09.0**. In de onderstaande stappen ziet u hoe u de container implementeert, uitvoert en er vervolgens verbinding mee maakt met een 3270-emulator.

## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, raadpleegt u de volgende vereisten:

- Een Azure-abonnement. Als je nog geen account hebt, maak je een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat je begint.

- De Micro Focus software en een geldige licentie (of proeflicentie). Als u een bestaande Micro Focus-klant bent, neemt u contact op met uw Micro Focus-vertegenwoordiger. Anders, [vraag een proef aan.](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)

     > [!NOTE]
     > De Docker-demonstratiebestanden zijn opgenomen bij Enterprise Server 4.0. Deze zelfstudie\_maakt\_gebruik\_van ent server dockerfiles 4.0\_windows.zip. Krijg toegang tot het vanaf dezelfde plaats waar u het installatiebestand van Enterprise Server hebt geopend of ga naar *Micro Focus* om aan de slag te gaan.

- De documentatie voor [Enterprise Server en Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#").

## <a name="create-a-vm"></a>Een virtuele machine maken

1. Beveilig de media\_van\_het\_ent-serverdockerfiles 4.0\_windows.zip-bestand. Beveilig het licentiebestand ES-Docker-Prod-XXXXXXXX.mflic (vereist om de Docker-afbeeldingen te bouwen).

2. Maak de VM. Open hiervoor Azure portal, selecteer **Een resource** maken linksboven en filter op *windows server.* Selecteer in de resultaten **Windows Server 2016 Datacenter – met containers**.

     ![Zoekresultaten voor Azure-portalen](media/01-portal.png)

3. Als u de eigenschappen voor de VM wilt configureren, kiest u instantiedetails:

    1. Kies een VM-grootte. Overweeg voor deze zelfstudie een **Standaard\_DS2 v2** VM met 2 vCPU's en 7 GB geheugen te gebruiken.

    2. Selecteer de **regio-** en **resourcegroep** die u wilt implementeren.

    3. Voor **beschikbaarheidsopties**gebruikt u de standaardinstelling.

    4. Typ **bij Gebruikersnaam**het beheerdersaccount dat u wilt gebruiken en het wachtwoord.

    5. Controleer of **poort 3389 RDP** open is. Alleen deze poort moet openbaar worden gemaakt, zodat u zich aanmelden bij de VM. Accepteer vervolgens alle standaardwaarden en klik op **Controleren+ maken.**

     ![Een deelvenster voor virtuele machines maken](media/container-02.png)

4. Wacht tot de implementatie is voltooid (een paar minuten). In een bericht staat dat uw vm is gemaakt.

5. Klik **op Ga naar Resource om** naar het **overzichtsblad** voor uw virtuele machine te gaan.

6. Klik rechts op de knop **Verbinding maken.** De opties **voor verbinding met virtuele machines** worden aan de rechterkant weergegeven.

7. Klik op de knop **RDP-bestand downloaden** om het RDP-bestand te downloaden waarmee u aan de VM koppelen.

8. Nadat het bestand is gedownload, opent u het en typt u de gebruikersnaam en het wachtwoord in die u voor de virtuele machine hebt gemaakt.

     > [!NOTE]
     > Gebruik uw bedrijfsreferenties niet om u aan te melden. (De RDP-client gaat ervan uit dat u deze wilt gebruiken. Dat doe je niet.)

9.  Selecteer **Meer keuzes**en selecteer vervolgens uw VM-referenties.

Op dit punt wordt de VM uitgevoerd en bevestigd via RDP. Je bent aangemeld en klaar voor de volgende stap.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Een sandboxmap maken en het zip-bestand uploaden

1.  Maak een map op de VM waar u de demo- en licentiebestanden uploaden. **C:\\Sandbox**.

2.  Upload **\_ent\_server\_dockerfiles\_4.0 windows.zip** en het **ES-Docker-Prod-XXXXXXXX.mflic-bestand** naar de directory die u hebt gemaakt.

3.  Haal de inhoud van het zip-bestand naar de **ent\_server\_dockerfiles\_4.0\_windows** directory gemaakt door het uithaalproces. Deze map bevat een readme-bestand (als .html- en .txt-bestand) en twee submappen, **EnterpriseServer** en **Voorbeelden**.

4.  Kopieer **ES-Docker-Prod-XXXXXXXX.mflic** naar\\de\\C: Sandbox\_ent\_server\\\_\_dockerfiles\\4.0 windows EnterpriseServer en C: Sandbox\\ent\_server\_dockerfiles\_4.0\_windows\\Voorbeelden\\CICS-mappen.

> [!NOTE]
> Zorg ervoor dat u het licentiebestand naar beide mappen kopieert. Ze zijn vereist voor de Docker build stap om ervoor te zorgen dat de beelden zijn goed gelicentieerd.

## <a name="check-docker-version-and-create-base-image"></a>Docker-versie controleren en basisafbeelding maken

> [!IMPORTANT]
> Het maken van de juiste Docker-afbeelding is een proces in twee stappen. Maak eerst de basisafbeelding Enterprise Server 4.0.Maak vervolgens een andere afbeelding voor het x64-platform. Hoewel u een x86-afbeelding (32-bits) maken, gebruikt u de 64-bits afbeelding.

1. Open een opdrachtprompt.

2. Controleer of Docker is geïnstalleerd. Typ het volgende na de opdrachtprompt:

    ```
        docker version
    ```

     De versie was bijvoorbeeld 18.09.0 toen deze werd geschreven.

3. Als u de map wilt wijzigen, typt u **cd \Sandbox\ent_server_dockerfiles_4.0_windows\EnterpriseServer**.

4. Typ **bld.bat IacceptEULA** om het bouwproces voor het oorspronkelijke basisbeeld te starten. Wacht een paar minuten tot dit proces wordt uitgevoerd. In de resultaten ziet u de twee afbeeldingen die zijn gemaakt: één voor x64 en één voor x86:

     ![Opdrachtvenster met afbeeldingen](media/container-04.png)

5. Als u de uiteindelijke afbeelding voor de CICS-demonstratie wilt maken, schakelt u over naar de CICS-map door **\\cd\\Sandbox ent\_server\_dockerfiles\_4.0\_windows\\Examples\\CICS**te typen.

6. Als u de afbeelding wilt maken, typt u **bld.bat x64**. Wacht een paar minuten tot het proces is uitgevoerd en het bericht dat de afbeelding is gemaakt.

7. Typ **dockerafbeeldingen** om een lijst weer te geven van alle Docker-afbeeldingen die op de VM zijn geïnstalleerd. Zorg ervoor dat **microfocus / es-acctdemo** is een van hen.

     ![Opdrachtvenster met es-acctdemo-afbeelding](media/container-05.png)

## <a name="run-the-image"></a>De afbeelding uitvoeren 

1.  Ga als volgende voor de lancering van Enterprise Server 4.0 en de acctdemo-toepassing op het type opdrachtprompt:

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  Installeer een 3270 terminal emulator zoals [x3270](http://x3270.bgp.nu/) en gebruik het om, via poort 9040, te koppelen aan de afbeelding die wordt uitgevoerd.

3.  Haal het IP-adres van de acctdemo-container op, zodat Docker kan fungeren als DHCP-server voor de containers die het beheert:

    1.  Haal de id van de lopende container. Typ **Docker ps** bij de opdrachtprompt en noteer de ID **(22a0fe3159d0** in dit voorbeeld). Bewaar het voor de volgende stap.

    2.  Als u het IP-adres voor de acctdemo-container wilt ophalen, gebruikt u de container-ID van de vorige stap als volgt:

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       Bijvoorbeeld:

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. Let op het IP-adres voor de acctdemo-afbeelding. Het adres in de volgende uitvoer is bijvoorbeeld 172.19.202.52.

     ![Opdrachtvenster met IP-adres](media/container-06.png)

5. Monteer de afbeelding met behulp van de emulator. Configureer de emulator om het adres van de acctdemo-afbeelding en poort 9040 te gebruiken. Hier is het **172.19.202.52:9040**. Die van jou zal hetzelfde zijn. Het **scherm Signon to CICS** wordt geopend.

    ![Signon to CICS-scherm](media/container-07.png)

6. Meld u aan bij de CICS-regio door **SYSAD** in te voeren voor de **USERID** en **SYSAD** voor het **wachtwoord.**

7. Maak het scherm leeg met behulp van de keymap van de emulator. Selecteer voor x3270 de optie **Keymap menu.**

8. Typ **ACCT**om de acctdemo-toepassing te starten. Het eerste scherm voor de toepassing wordt weergegeven.

     ![Scherm Accountdemo](media/container-08.png)

9. Experimenteer met weergaveaccounttypen. Typ bijvoorbeeld **D** voor de aanvraag en **11111** voor het **account**. Andere rekeningnummers om te proberen zijn 22222, 33333, enzovoort.

     ![Scherm Accountdemo](media/container-09.png)

10. Als u de console Enterprise Server Administration wilt weergeven, gaat u naar de opdrachtprompt en typt u **de start http:172.19.202.52:86**

     ![Enterprise Server Administration-console](media/container-010.png)

Dat is alles. Nu voert u een CICS-toepassing uit in een Docker-container.

## <a name="next-steps"></a>Volgende stappen

- [Micro Focus Enterprise Server 4.0 en Enterprise Developer 4.0 installeren op Azure](./set-up-micro-focus-azure.md)
- [Migratie van mainframetoepassingen](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
