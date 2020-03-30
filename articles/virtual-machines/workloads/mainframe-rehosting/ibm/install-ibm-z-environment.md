---
title: IBM zD-&T-dev/testomgeving installeren op Azure | Microsoft Documenten
description: Ibm Z Development and Test Environment (zD&T) implementeren op de VM-infrastructuur (Azure Virtual Machine) (IaaS).
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 04/02/2019
tags: ''
keywords: ''
ms.openlocfilehash: 67af4eae03b773fad9cf38964152c1fb9d623bd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72025932"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>IBM zD-&T-dev/testomgeving installeren op Azure

Als u een dev/testomgeving wilt maken voor mainframeworkloads op IBM Z Systems, u IBM Z Development and Test Environment (zD&T) implementeren op De VM-infrastructuur (Azure Virtual Machine) (IaaS).

Met zD&T u profiteren van de kostenbesparingen van het x86-platform voor uw minder kritieke ontwikkel- en testomgevingen en de updates vervolgens terugduwen naar een productieomgeving van het Z-systeem. Zie voor meer informatie de [installatie-instructies van IBM ZD&T.](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL)

Azure en Azure Stack ondersteunen de volgende versies:

- zD&T Personal Edition
- zD&T Parallel Sysplex
- zD&T Enterprise Edition

Alle edities van zD&T draaien alleen op x86 Linux systemen, niet Windows Server. Enterprise Edition wordt ondersteund op Red Hat Enterprise Linux (RHEL) of Ubuntu/Debian. Zowel RHEL- als Debian VM-afbeeldingen zijn beschikbaar voor Azure.

In dit artikel ziet u hoe u zD-&T Enterprise Edition op Azure instellen, zodat u de zD-&T Enterprise Edition-webserver gebruiken voor het maken en beheren van omgevingen. Het installeren van zD&T installeert geen omgevingen. U moet deze afzonderlijk maken als installatiepakketten. Application Developers Controlled Distributions (ADCD) zijn bijvoorbeeld volumeafbeeldingen van testomgevingen. Ze zijn opgenomen in zip-beelden op de media-distributie beschikbaar van IBM. Bekijk hoe u [een ADCD-omgeving instelt op Azure.](demo.md)

Zie voor meer informatie het [zD&T-overzicht](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) in het IBM Knowledge Center.

In dit artikel ziet u hoe u Z Development and Test Environment (zD&T) Enterprise Edition instelt op Azure. Vervolgens u de zD-&T Enterprise Edition-webserver gebruiken om Z-gebaseerde omgevingen op Azure te maken en te beheren.

## <a name="prerequisites"></a>Vereisten

> [!NOTE]
> IBM staat toe dat zD&T Enterprise Edition alleen in dev/testomgevingen worden geïnstalleerd,*niet* in productieomgevingen.

- Een Azure-abonnement. Als je nog geen account hebt, maak je een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat je begint.

- U hebt toegang nodig tot de media, die alleen beschikbaar zijn voor IBM-klanten en -partners. Neem voor meer informatie contact op met uw IBM-vertegenwoordiger of raadpleeg de contactgegevens op de [website van zD&T.](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment)

- Een [licentieserver.](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html) Dit is nodig voor toegang tot de omgevingen. De manier waarop u deze maakt, hangt af van de manier waarop u de software van IBM in licentie licentie draagt:

     - **Hardwaregebaseerde licentieserver** vereist een USB-hardwareapparaat dat de Rationele tokens bevat die nodig zijn om toegang te krijgen tot alle delen van de software. U moet dit verkrijgen van IBM.

     - **Softwaregebaseerde licentieserver** vereist dat u een gecentraliseerde server instelt voor het beheer van de licentiesleutels. Deze methode heeft de voorkeur en vereist dat u de sleutels die u ontvangt van IBM in de beheerserver in te stellen.

## <a name="create-the-base-image-and-connect"></a>De basisafbeelding maken en verbinding maken

1. Maak in Azure-portal [een VM](/azure/virtual-machines/linux/quick-create-portal) met de gewenste configuratie van het besturingssysteem. In dit artikel wordt uitgegaan van een B4ms VM (met 4 vCPU's en 16 GB geheugen) met Ubuntu 16.04.

2. Nadat de VM is gemaakt, opent u binnenkomende poorten 22 voor SSH, 21 voor FTP en 9443 voor de webserver.

3. Download de SSH-referenties op het **overzichtsblad** van de VM via de **connect-knop.** Selecteer het tabblad **SSH** en kopieer de ssh-aanmeldingsopdracht naar het klembord.

4. Meld u aan bij een [Bash-shell](/azure/cloud-shell/quickstart) van uw lokale pc en plak de opdracht. Het zal in de vorm **ssh\<user id\>\@\<IP-adres\>**. Wanneer u om uw referenties wordt gevraagd, voert u deze in om een verbinding met uw thuismap tot stand te brengen.

## <a name="copy-the-installation-file-to-the-server"></a>Het installatiebestand naar de server kopiëren

Het installatiebestand voor de webserver is **ZDT\_Install\_EE\_V12.0.0.1.tgz**. Het is opgenomen in de media geleverd door IBM. U moet dit bestand uploaden naar uw Ubuntu VM.

1. Voer in de opdrachtregel de volgende opdracht in om ervoor te zorgen dat alles up-to-date is in de nieuw gemaakte afbeelding:

    ```
    sudo apt-get update
    ```

2. Maak de map om te installeren op:

    ```
    mkdir ZDT
    ```

3. Kopieer het bestand van uw lokale machine naar de vm:

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> Met deze opdracht wordt het installatiebestand gekopieerd naar de ZDT-map in uw thuismap, die afhankelijk is van of uw client Windows of Linux uitvoert.

## <a name="install-the-enterprise-edition"></a>De Enterprise Edition installeren

1. Ga naar de ZDT-map en\_decomprimeer het ZDT Install\_EE\_V12.0.0.1.tgz-bestand met de volgende opdrachten:

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. Voer de installatieprogramma uit:

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. Selecteer **1** om Enterprise Server te installeren.

4. Druk op **Enter** en lees de licentieovereenkomsten zorgvuldig door. Voer aan het einde van de licentie **Ja** in om door te gaan.

5. Wanneer gevraagd om het wachtwoord voor de nieuw gecreëerde gebruiker te wijzigen, **ibmsys1**, gebruik dan de opdracht **sudo passwd ibmsys1** en voer het nieuwe wachtwoord in.

6. Als u wilt controleren of de installatie is geslaagd, moet u

    ```
    dpkg -l | grep zdtapp
    ```

7. Controleer of de uitvoer de tekenreeks **zdtapp 12.0.0.0**bevat, wat aangeeft dat het pakketgas met succes is geïnstalleerd

### <a name="starting-enterprise-edition"></a>Enterprise Edition starten

Houd er rekening mee dat wanneer de webserver wordt gestart, deze wordt uitgevoerd onder de zD-&T-gebruikers-id die tijdens het installatieproces is gemaakt.

1. Als u de webserver wilt starten, gebruikt u de hoofdgebruikersnaam om de volgende opdracht uit te voeren:

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. Kopieer de URL-uitvoer door het script, dat eruit ziet als:

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. Plak de URL in een webbrowser om de beheercomponent voor uw zD-&T-installatie te openen.

## <a name="next-steps"></a>Volgende stappen

[Een ADCD (Application Developers Controlled Distribution) instellen in IBM zD&T v1](./demo.md)
