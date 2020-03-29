---
title: Virtuele machines voor Azure Data Science gebruiken
description: Meer informatie over hoe u verbinding maken met een Azure Data Science Virtual Machine (DSVM) om de beschikbare rekenkracht uit te breiden naar Azure Notebooks Preview.
author: getroyer
manager: andneil
ms.author: getroyer
ms.topic: how-to
ms.date: 06/13/2019
ms.openlocfilehash: b4da63b7b2a6da4316215b85a09ca7420745251c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898404"
---
# <a name="use-azure-data-science-virtual-machines"></a>Virtuele machines voor Azure Data Science gebruiken

Standaard worden projecten uitgevoerd op de **free compute-laag,** die beperkt is tot 4 GB geheugen en 1 GB aan gegevens om misbruik te voorkomen. U deze beperkingen omzeilen door een andere virtuele machine te gebruiken die u hebt ingericht in een Azure-abonnement. De beste keuze hiervoor is een Azure Data Science Virtual Machine (DSVM) met behulp van de **Data Science Virtual Machine for Linux (Ubuntu)** afbeelding. Zo'n DSVM is vooraf geconfigureerd met alles wat u nodig hebt voor Azure-notitieblokken en verschijnt automatisch in de vervolgkeuzelijst **Uitvoeren** in Azure-notitieblokken.

> [!Note]
> Azure Notebooks worden alleen ondersteund op DSVMs die zijn gemaakt met de on Linux Ubuntu-afbeelding. Notitieblokken worden niet ondersteund op Windows 2012-, Windows 2016- of Linux CentOS-afbeeldingen.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-dsvm-instance"></a>Een DSVM-exemplaar maken

Als u een nieuwe DSVM-instantie wilt maken, volgt u de instructies [voor Een Ubuntu Data Science VM maken.](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro) Zie Data Science Virtual [Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)voor meer informatie, inclusief prijsinformatie.

## <a name="connect-to-the-dsvm"></a>Verbinding maken met de DSVM

Zodra u de DSVM hebt gemaakt, selecteert u de vervolgkeuzelijst **Uitvoeren** op het projectdashboard Azure Notebooks en selecteert u de juiste DSVM-instantie. In de vervolgkeuzelijst worden DSVM-exemplaren weergegeven als de volgende voorwaarden waar zijn:

- U bent aangemeld bij Azure Notebooks met een account dat gebruikmaakt van Azure Active Directory (AAD), zoals een bedrijfsaccount.
- Uw account is verbonden met een Azure-abonnement.
- Je hebt een of meer virtuele machines in dat abonnement, met ten minste Reader toegang, dat gebruik maakt van de Data Science Virtual Machine voor Linux (Ubuntu) afbeelding.)

![Data Science Virtual Machine-exemplaren in de vervolgkeuzelijst op het projectdashboard](media/project-compute-tier-dsvm.png)

Wanneer u een DSVM-exemplaar selecteert, kunnen Azure-notitieblokken u vragen om de specifieke machinereferenties die worden gebruikt bij het maken van de vm.

> [!Important]
> De gebruikersnaam moet kleine letters zijn om het te gebruiken met JupyterHub.

Als niet aan een van de voorwaarden wordt voldaan, u nog steeds verbinding maken met de DSVM. Selecteer in de vervolgkeuzelijst de optie **Direct berekenen,** waarin u wordt gevraagd om een naam (om in de lijst weer te geven), het IP-adres en de poort van de VM (meestal 8000, de standaardpoort waarnaar JupyterHub luistert) en de VM-referenties:

![Vragen om servergegevens te verzamelen voor de optie Direct Compute](media/project-compute-tier-direct.png)

U verkrijgt deze waarden op de DSVM-pagina in de Azure-portal.

## <a name="accessing-azure-notebooks-files-from-the-dsvm"></a>Toegang tot Azure Notebooks-bestanden vanuit de DSVM

Toegang tot bestandssystemen wordt ondersteund voor DSVM-versies 19.06.15 of hoger. Als u de versie wilt controleren, maakt u eerst verbinding met `curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2018-10-01"` uw DSVM via SSH en voert u vervolgens de volgende opdracht uit: (u moet het exacte IP-adres dat hier wordt weergegeven) gebruiken. Het versienummer wordt weergegeven in de uitvoer voor "versie".

Als u de pariteit van bestandspaden met de laag **Vrije gegevens berekenen** wilt behouden, u slechts één project tegelijk openen op een DSVM. Als u een nieuw project wilt openen, moet u eerst het open project afsluiten.

Wanneer een project op een vm wordt uitgevoerd, worden de bestanden gemonteerd op de hoofdmap van de Jupyter-server (de map in JupyterHub), ter vervanging van de standaardAzure-notitieblokkenbestanden. Wanneer u de vm afsluit met de knop **Afsluiten** op de gebruikersinterface van het notitieblok, worden de standaardbestanden hersteld van Azure Notebooks.

![Knop Afsluiten in Azure-notitieblokken](media/shutdown.png)

## <a name="create-new-dsvm-users"></a>Nieuwe DSVM-gebruikers maken

Als meerdere gebruikers een DSVM delen, u voorkomen dat u elkaar blokkeert door voor elke notebookgebruiker een DSVM-gebruiker te maken en te gebruiken:

1. Navigeer op de [Azure-portal](https://portal.azure.com)naar uw virtuele machine.
1. Selecteer **Onder Ondersteuning + probleemoplossing** in de linkermarge de optie Wachtwoord opnieuw **instellen**.
1. Voer een nieuwe **gebruikersnaam in**. De gebruikersnaam moet kleine letters zijn om het te gebruiken met JupyterHub. Voer een wachtwoord in. Selecteer vervolgens **Bijwerken**. (Bestaande gebruikersnamen worden niet beïnvloed.)
1. Herhaal de vorige stap voor eventuele extra gebruikers.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over DSVMs over [inleiding tot azure data science virtuele machines](/azure/machine-learning/data-science-virtual-machine/overview).
