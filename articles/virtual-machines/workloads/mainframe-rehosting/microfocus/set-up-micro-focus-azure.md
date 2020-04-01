---
title: Micro Focus Enterprise Server 4.0 en Enterprise Developer 4.0 installeren op Azure | Microsoft Documenten
description: Host uw IBM z/OS mainframeworkloads opnieuw met behulp van de Micro Focus-ontwikkel- en testomgeving op virtuele Azure-machines (VM's).
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 03/30/2020
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: 5b3fa5faccf17df17bc4f7cc2d8b023b868fdbe1
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411200"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>Micro Focus Enterprise Server 4.0 en Enterprise Developer 4.0 installeren op Azure

In dit artikel ziet u hoe [u Micro Focus Enterprise Server 4.0](https://www.microfocus.com/documentation/enterprise-developer/es30/) en Micro Focus Enterprise Developer [4.0](https://www.microfocus.com/documentation/enterprise-developer/ed_30/) inStelt op Azure.

Een veelvoorkomende werkbelasting op Azure is een ontwikkel- en testomgeving. Dit scenario komt vaak voor omdat het zo kosteneffectief en gemakkelijk te implementeren en af te bouwen is. Met Enterprise Server heeft Micro Focus een van de grootste mainframe rehostingplatforms gecreëerd die beschikbaar zijn. U z/OS-workloads uitvoeren op een goedkoper x86-platform op Azure met behulp van virtuele Windows- of Linux-virtuele machines (VM's).

> [!NOTE]
> Binnenkort beschikbaar: instructies voor het instellen van [Micro Focus Enterprise Server 5.0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110) op Azure VM's.

Deze instelling maakt gebruik van Azure VM's waarop de Windows Server 2016-afbeelding wordt uitgevoerd vanuit de Azure Marketplace met Microsoft SQL Server 2017 die al is geïnstalleerd. Deze instelling is ook van toepassing op Azure Stack.

De bijbehorende ontwikkelomgeving voor Enterprise Server is Enterprise Developer, die draait op Microsoft Visual Studio 2017 of hoger, Visual Studio Community (gratis te downloaden) of Eclipse. In dit artikel ziet u hoe u deze implementeert met behulp van een virtuele windows server 2016-machine die wordt geleverd met Visual Studio 2017 of hoger.

## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, raadpleegt u de volgende vereisten:

- Een Azure-abonnement. Als je nog geen account hebt, maak je een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat je begint.

- De Micro Focus software en een geldige licentie (of proeflicentie). Als u een bestaande Micro Focus-klant bent, neemt u contact op met uw Micro Focus-vertegenwoordiger. Anders, [vraag een proef aan.](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)

- Download de documentatie voor [Enterprise Server en Enterprise Developer.](https://www.microfocus.com/documentation/enterprise-developer/#")

> [!NOTE]
> Een aanbevolen praktijk is het instellen van een vpn-tunnel (site-to-site virtual private network) of een jumpbox, zodat u de toegang tot de Azure VM's beheren.

## <a name="install-enterprise-server"></a>Enterprise-server installeren

1. Voor een betere beveiliging en beheerbaarheid u overwegen om een nieuwe resourcegroep te maken voor dit project, bijvoorbeeld **RGMicroFocusEntServer.** Gebruik het eerste deel van de naam in Azure om het type resource te kiezen om het gemakkelijker te maken om in een lijst te herkennen.

2. Hiermee maakt u een virtuele machine. Selecteer in de Azure Marketplace de gewenste virtuele machine en besturingssysteem. Hier is een aanbevolen setup:

    - **Enterprise Server**: Selecteer ES2 v3 VM (met 2 vCPU's en 16 GB geheugen) met Windows Server 2016 en SQL Server 2017 geïnstalleerd. Deze afbeelding is beschikbaar op de Azure Marketplace. Enterprise Server kan azure SQL Database ook gebruiken.

    - **Enterprise Developer**: Selecteer B2ms VM (met 2 vCPU's en 8 GB geheugen) met Windows 10 en Visual Studio geïnstalleerd. Deze afbeelding is beschikbaar op de Azure Marketplace.

3. Voer in de sectie **Basisbeginselen** uw gebruikersnaam en wachtwoord in. Selecteer het **abonnement** en **locatie/regio** dat u wilt gebruiken voor de VM's. Selecteer **RGMicroFocusEntServer** voor de resourcegroep.

4. Zet beide VM's in hetzelfde virtuele netwerk, zodat ze met elkaar kunnen communiceren.

5. Accepteer de standaardinstellingen voor de rest van de instellingen. Onthoud de gebruikersnaam en het wachtwoord dat u maakt voor de beheerder van deze VM's.

6. Wanneer de virtuele machines zijn gemaakt, opent u binnenkomende poorten 9003, 86 en 80 voor HTTP en 3389 voor RDP op de Enterprise Server-machine en 3389 op de machine Developer.

7. Als u zich wilt aanmelden bij de virtuele machine van Enterprise Server, selecteert u in Azure-portal de ES2 v3 VM. Ga naar de sectie **Overzicht** en selecteer **Verbinding maken** om een RDP-sessie te starten. Meld u aan met de referenties die u voor de vm hebt gemaakt.

8. Laad vanuit de RDP-sessie de volgende twee bestanden. Omdat u Windows gebruikt, u de bestanden slepen en neerzetten in de RDP-sessie:

    - **es\_40.exe**, het installatiebestand van Enterprise Server.

    - **mflic**, het bijbehorende licentiebestand — Enterprise Server wordt niet geladen zonder.

9. Dubbelklik op het bestand om de installatie te starten. Selecteer in het eerste venster de installatielocatie en accepteer de licentieovereenkomst voor eindgebruikers.

     ![Scherm Micro Focus Enterprise Server Setup](media/01-enterprise-server.png)

     Wanneer de installatie is voltooid, wordt het volgende bericht weergegeven:

     ![Scherm Micro Focus Enterprise Server Setup](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>Naar updates zoeken

Controleer na de installatie op eventuele aanvullende updates, omdat een aantal vereisten, zoals de Microsoft C++ Redistributable en .NET Framework, samen met Enterprise Server zijn geïnstalleerd.

### <a name="upload-the-license"></a>De licentie uploaden

1. Start de Micro Focus License Administration.

2. Klik **op** \> **Licentiebeheer voor Microfocus starten** \> **License Administration**en klik vervolgens op het tabblad **Installeren.** Kies het type licentie-indeling dat u wilt uploaden: een licentiebestand of een licentiecode met 16 tekens. Blader bijvoorbeeld voor een bestand in **licentiebestand**naar het **mfplic-bestand dat** eerder naar de vm is geüpload en selecteer **Licenties installeren.**

     ![Dialoogvenster Micro Focus-licentiebeheer](media/03-enterprise-server.png)

3. Controleer of Enterprise Server wordt geladen. Probeer de site Enterprise Server Administration te <http://localhost:86/> starten vanuit een browser met deze URL . De pagina Enterprise Server Beheer wordt weergegeven zoals weergegeven.

     ![Pagina Enterprise Server Beheer](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>Enterprise Developer installeren op de ontwikkelaarsmachine

1. Selecteer de eerder gemaakte brongroep (bijvoorbeeld **RGMicroFocusEntServer)** en selecteer vervolgens de ontwikkelaarsafbeelding.

2. Als u zich wilt aanmelden bij de virtuele machine, gaat u naar de sectie **Overzicht** en selecteert u **Verbinding maken**. Met dit aanmelden wordt een RDP-sessie gestart. Meld u aan met de referenties die u voor de vm hebt gemaakt.

3. Laad vanuit de RDP-sessie de volgende twee bestanden (slepen en neerzetten als je wilt):

    - **edvs2017.exe**, het installatiebestand van Enterprise Server.

    - **mflic**, het bijbehorende licentiebestand (Enterprise Developer zal niet laden zonder).

4. Dubbelklik op het **bestand edvs2017.exe** om de installatie te starten. Selecteer in het eerste venster de installatielocatie en accepteer de licentieovereenkomst voor eindgebruikers. Kies **Rumba 9.5 installeren** om deze terminalemulator te installeren, die u waarschijnlijk nodig hebt.

     ![Dialoogvenster Micro Focus Enterprise Developer voor Visual Studio 2017 Setup](media/04-enterprise-server.png)

5. Nadat de installatie is voltooid, wordt het volgende bericht weergegeven:

     ![Geslaagd bericht instellen](media/05-enterprise-server.png)

6. Start de Micro Focus License Manager net als voor Enterprise Server. Kies **Start** \> **Licentiebeheer** \> **License Administration**voor microfocus starten en klik op het tabblad **Installeren.**

7. Kies het type licentie-indeling dat u wilt uploaden: een licentiebestand of een licentiecode van 16 tekens. Blader bijvoorbeeld voor een bestand in **licentiebestand**naar het **mfplic-bestand dat** eerder naar de vm is geüpload en selecteer **Licenties installeren.**

     ![Dialoogvenster Micro Focus-licentiebeheer](media/07-enterprise-server.png)

Wanneer Enterprise Developer wordt geladen, is de implementatie van een Micro Focus-ontwikkel- en testomgeving op Azure voltooid!

## <a name="next-steps"></a>Volgende stappen

- [De bankdemo-toepassing instellen](./demo.md)
- [Enterprise Server uitvoeren in Docker-containers](./run-enterprise-server-container.md)
- [Migratie van mainframetoepassingen](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
