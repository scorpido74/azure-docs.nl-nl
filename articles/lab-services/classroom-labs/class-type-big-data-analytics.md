---
title: Een lab opzetten om big data-analyses te leren met Azure Lab Services | Microsoft Documenten
description: Meer informatie over het opzetten van een lab om de big data analytics te leren met Docker deployment van Hortonworks Data Platform (HDP).
services: lab-services
documentationcenter: na
author: nicolela
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/15/2020
ms.author: nicolela
ms.openlocfilehash: c499b7a0f1cd16bf57fef21742b01bda71249916
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538776"
---
# <a name="set-up-a-lab-to-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>Een lab opzetten voor big data analytics met Docker-implementatie van HortonWorks Data Platform

In dit artikel ziet u hoe u een lab instelt om een big data-analyseklasse te onderwijzen.  Met dit type klas leren studenten hoe ze met grote hoeveelheden gegevens om kunnen gaan en machine- en statistische leeralgoritmen toepassen om gegevensinzichten af te leiden.  Een belangrijke doelstelling voor studenten is om te leren om data analytics tools te gebruiken, zoals [Apache Hadoop's open-source software pakket](https://hadoop.apache.org/) dat tools biedt voor het opslaan, beheren en verwerken van big data.

In dit lab zullen studenten gebruik maken van een populaire commerciële versie van Hadoop die wordt geleverd door [Cloudera](https://www.cloudera.com/), genaamd [Hortonworks Data Platform (HDP).](https://www.cloudera.com/products/hdp.html)  In het bijzonder zullen studenten [HDP Sandbox 3.0.1](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html) gebruiken, een vereenvoudigde, gebruiksvriendelijke versie van het platform die gratis is en bedoeld is om te leren en te experimenteren.  Hoewel deze klasse windows- of Linux-virtuele machines (VM) kan gebruiken met HDP Sandbox geïmplementeerd, wordt in dit artikel weergegeven hoe u Windows gebruiken.

Een ander interessant aspect van dit lab, is dat we HDP Sandbox inzetten op het lab VM's met behulp van [Docker](https://www.docker.com/) containers.  Elke Docker-container biedt zijn eigen geïsoleerde omgeving voor softwaretoepassingen die binnen kunnen worden uitgevoerd.  Conceptueel zijn Docker-containers vergelijkbaar met geneste VM's en kunnen ze worden gebruikt om eenvoudig een breed scala aan softwaretoepassingen te implementeren en uit te voeren op basis van containerafbeeldingen die op [Docker Hub](https://www.docker.com/products/docker-hub)worden geleverd.  Cloudera's implementatiescript voor HDP Sandbox haalt automatisch de [HDP Sandbox 3.0.1 Docker-afbeelding](https://hub.docker.com/r/hortonworks/sandbox-hdp) uit Docker Hub en voert twee Docker-containers uit:
  - sandbox-hdp
  - sandbox-proxy

## <a name="lab-configuration"></a>Labconfiguratie

Om dit lab in te stellen, hebt u een Azure-abonnement en labaccount nodig om aan de slag te gaan. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint. Zodra u een Azure-abonnement hebt, u een nieuw labaccount maken in Azure Lab Services. Zie [Zelfstudie om een Lab-account op te zetten voor](tutorial-setup-lab-account.md)meer informatie over het maken van een nieuw labaccount.  U ook een bestaand labaccount gebruiken.

### <a name="lab-account-settings"></a>Lab-accountinstellingen

Schakel de instellingen in die in de onderstaande tabel worden beschreven voor het labaccount. Zie Marketplace-afbeeldingen opgeven die beschikbaar zijn voor makers van het lab voor meer informatie over het inschakelen [van marketplace-afbeeldingen.](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)

| Lab-accountinstelling | Instructies |
| ------------------- | ------------ |
|Afbeelding van Marktplaats| Schakel de Windows 10 Pro-afbeelding in voor gebruik binnen uw labaccount.|

### <a name="lab-settings"></a>Lab-instellingen

Gebruik de instellingen in de onderstaande tabel bij het instellen van een klaslokaallab.  Zie een lesprogramma voor een klaslokaal voor meer informatie over het maken van een [leslokaallab.](tutorial-setup-classroom-lab.md)

| Lab-instellingen | Waarde/instructies |
| ------------ | ------------------ |
|Virtuele machinegrootte| Medium (Geneste virtualisatie). Deze VM-grootte is het meest geschikt voor relationele databases, caches in het geheugen en analyses.  Deze grootte ondersteunt ook geneste virtualisatie.|  
|Afbeelding van virtuele machine| Windows 10 Pro|

> [!NOTE] 
> We moeten Medium (Geneste virtualisatie) gebruiken, omdat het implementeren van HDP Sandbox met Docker vereist:
>   - Windows Hyper-V met geneste virtualisatie
>   - Minstens 10 GB RAM

## <a name="template-machine-configuration"></a>Sjabloonmachineconfiguratie

Om de sjabloonmachine in te stellen, zullen we:
- Docker installeren
- HDP Sandbox implementeren
- PowerShell en Windows Taakplanner gebruiken om de Docker-containers automatisch te starten

### <a name="install-docker"></a>Docker installeren

De stappen in deze sectie zijn gebaseerd op [de instructies van Cloudera voor het implementeren met Docker-containers.](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html) 

Als u Docker-containers wilt gebruiken, moet u dockerbureaublad eerst installeren op de sjabloon-vm:

1. Volg de stappen in de [sectie Voorwaarden](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) om [Docker voor Windows](https://docs.docker.com/docker-for-windows/install/)te installeren. 

    > [!IMPORTANT] 
    > Controleer of de **configuratieoptie Windows-containers gebruiken in plaats van Linux-containers** niet wordt ingeschakeld.

1. Controleer of **de functies Windows Containers en Hyper-V** zijn ingeschakeld.
   ![Windows-onderdelen in- of uitschakelen](../media/class-type-big-data-analytics/windows-hyperv-features.png)

1. Volg de stappen in de sectie [Geheugen voor Windows](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows) om de geheugenconfiguratie van Docker te configureren.

    > [!WARNING]
    > Als u per ongeluk de optie **Windows-containers gebruiken in plaats van Linux-containers** controleert bij het installeren van Docker, ziet u de instellingen voor geheugenconfiguratie niet.  Om dit op te lossen, u overschakelen naar het gebruik van Linux-containers door [te klikken op het Docker-pictogram in de windows-systeemlade;](https://docs.docker.com/docker-for-windows/#docker-settings-dialog) Wanneer het menu Docker Desktop wordt geopend, selecteert u **Overschakelen naar Linux-containers**.
 
### <a name="deploy-hdp-sandbox"></a>HDP Sandbox implementeren

In deze sectie implementeert u HDP Sandbox en krijgt u vervolgens ook toegang tot HDP Sandbox via de browser.

1. Zorg ervoor dat u [Git Bash](https://gitforwindows.org/) hebt geïnstalleerd zoals vermeld in het [gedeelte Voorwaarden](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) van de handleiding, omdat dit wordt aanbevolen voor het voltooien van de volgende stappen.

1. Voer de stappen in de volgende secties uit met de [implementatie- en installatiehandleiding van Clouder voor Docker:](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)
   
   -    HDP Sandbox implementeren
   -    HDP Sandbox verifiëren

    > [!WARNING] 
    > Wanneer u het nieuwste .zip-bestand voor HDP downloadt, moet u ervoor zorgen dat u het .zip-bestand *niet* opslaat in een mappad met witruimte.

    > [!NOTE] 
    > Als u tijdens de implementatie een uitzondering ontvangt waarin staat dat **Drive niet is gedeeld,** moet u uw C-station delen met Docker, zodat de Linux-containers van HDP toegang hebben tot lokale Windows-bestanden.  Als u dit wilt oplossen, [klikt u op het pictogram Docker in de vakleiding van Windows-systeem](https://docs.docker.com/docker-for-windows/#docker-settings-dialog) om het menu Docker Desktop te openen en **instellingen**te selecteren.  Wanneer het dialoogvenster **Instellingen van Docker** wordt geopend, selecteert u **Bronnen > Het delen van bestanden** en schakelt u het **C-station** in.  U vervolgens de stappen herhalen om HDP Sandbox te implementeren.

1. Zodra de Docker-containers voor HDP Sandbox zijn geïmplementeerd en uitgevoerd, hebt u toegang tot de omgeving door uw browser te starten en de instructies van Cloudera te volgen voor het openen van de [Welkomstpagina](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page) van de Sandbox en het starten van het HDP-dashboard.

    > [!NOTE] 
    > Deze instructies gaan ervan uit dat u eerst het lokale IP-adres van de sandbox-omgeving hebt toegewezen aan de sandbox-hdp.hortonworks.com in het hostbestand op uw sjabloon-VM.  Als u deze toewijzing **niet** doet, u de [http://localhost:8080](http://localhost:8080)pagina Sandbox-welkom openen door te navigeren naar.

### <a name="automatically-start-docker-containers-when-students-log-in"></a>Docker-containers automatisch starten wanneer studenten zich aanmelden

Om studenten een gebruiksvriendelijke ervaring te bieden, gebruiken we automatisch een PowerShell-script dat automatisch:
  - Hiermee start u de HDP Sandbox Docker-containers wanneer een student start en verbinding maakt met hun lab-VM.
  - Start de browser en navigeert naar de Sandbox Welkomstpagina.
We gebruiken windows taakplanner ook om dit script automatisch uit te voeren wanneer een student zich aanmeldt bij zijn VM.
Volg de volgende stappen: [Big Data Analytics scripting](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/)om dit in te stellen.

## <a name="cost-estimate"></a>Kostenraming

Als u de kosten van dit lab wilt schatten, u het volgende voorbeeld gebruiken.

Voor een klas van 25 studenten met 20 uur geplande lestijd en 10 uur quotum voor huiswerk of opdrachten, zou de prijs voor het lab zijn:
  - 25 studenten * (20 + 10) uur * 55 Lab Units * 0,01 USD per uur = 412,50 USD

Meer informatie over prijzen, zie [Azure Lab Services Pricing](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusie

Dit artikel heeft u de stappen doorlopen die nodig zijn om een lab te maken voor een big data-analyseklasse die gebruik maakt van Hortonworks Data Platform dat is geïmplementeerd met Docker.  De instelling voor dit klassetype kan worden gebruikt voor vergelijkbare klassen voor gegevensanalyse.  Deze instelling kan ook van toepassing zijn op andere typen klassen die Docker gebruiken voor implementatie.

## <a name="next-steps"></a>Volgende stappen

De volgende stappen zijn gebruikelijk voor het opzetten van een lab.

- [Een sjabloon maken en beheren](how-to-create-manage-template.md)
- [Gebruikers toevoegen](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quotum instellen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Een schema instellen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Koppelingen naar e-mailregistratie naar studenten](how-to-configure-student-usage.md#send-invitations-to-users)
