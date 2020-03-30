---
title: Uw VM-afbeelding certificeren voor de Azure Marketplace
description: Hier wordt uitgelegd hoe u een VM-afbeelding voor Azure Marketplace-certificering testen en verzenden.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: dsindona
ms.openlocfilehash: 0225069179e0d7d94a983c5161976a5c1933fac5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278192"
---
# <a name="certify-your-vm-image"></a>Uw VM-afbeelding certificeren

Nadat u uw virtuele machine (VM) hebt gemaakt en geïmplementeerd, moet u de VM-afbeelding voor Azure Marketplace-certificering testen en verzenden. In dit artikel wordt uitgelegd waar u het *certificeringstestprogramma voor Azure Certified*krijgen, hoe u deze tool gebruiken om uw VM-afbeelding te certificeren en hoe u de verificatieresultaten uploadt naar de Azure-container waar uw VHD's zich bevinden. 


## <a name="download-and-run-the-certification-test-tool"></a>Het certificeringstestprogramma downloaden en uitvoeren

De Certificeringstesttool voor Azure Certified wordt uitgevoerd op een lokale Windows-machine, maar test een Windows- of Linux-vm op Azure.  Hierin wordt gecontroleerd of de VM-afbeelding van uw gebruiker compatibel is met Microsoft Azure, of aan de richtlijnen en vereisten voor de voorbereiding van uw VHD is voldaan. De uitvoer van de tool is een compatibiliteitsrapport dat u uploadt naar de [Cloud Partner Portal](https://cloudpartner.azure.com) om VM-certificering aan te vragen.

1. Download en installeer de meest recente [Certification Test Tool voor Azure Certified.](https://www.microsoft.com/download/details.aspx?id=44299) 
2. Open het certificeringsgereedschap en klik op **Nieuwe test starten**.
3. Voer in het scherm **Testinformatie** een **testnaam** in voor de testrun.
4. Selecteer het **platform** voor `Windows Server` uw `Linux`vm of . Uw platformkeuze is van invloed op de overige opties.
5. Als uw VM deze databaseservice gebruikt, schakelt u het selectievakje **Testen voor Azure SQL Database in.**

   ![Beginpagina van cert-testprogramma's](./media/publishvm_025.png)


## <a name="connect-the-certification-tool-to-a-vm-image"></a>Het certificeringsgereedschap verbinden met een VM-afbeelding

  De tool maakt verbinding met VMs op basis van Windows met [PowerShell](https://docs.microsoft.com/powershell/) en maakt verbinding met Linux VM's via [SSH.Net.](https://www.ssh.com/ssh/protocol/)

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Het certificeringsprogramma verbinden met een Linux VM-afbeelding

1. Selecteer de **SSH-verificatiemodus:** `Password Authentication` of `key File Authentication`.
2. Als u verificatie op basis van een wachtwoord gebruikt, voert u waarden in voor de **VM DNS-naam**, **gebruikersnaam**en **wachtwoord**.  Optioneel u het standaard **SSH-poortnummer** wijzigen.

     ![Wachtwoordverificatie van Linux VM Image](./media/publishvm_026.png)

3. Als u verificatie op basis van sleutelbestanden gebruikt, voert u waarden in voor de **locatie VM DNS,** **Gebruikersnaam**en **Privésleutel.**  Optioneel u een **Wachtwoordzin** leveren of het standaard **SSH-poortnummer** wijzigen.

     ![Bestandsverificatie van Linux VM Image](./media/publishvm_027.png)

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Het certificeringshulpprogramma verbinden met een VM-afbeelding op basis van Windows**
1. Voer de volledig gekwalificeerde **VM DNS-naam** in `MyVMName.Cloudapp.net`(bijvoorbeeld).
2. Voer waarden in voor de **gebruikersnaam** en **het wachtwoord**.

   ![Wachtwoordverificatie van Windows VM Image](./media/publishvm_028.png)


## <a name="run-a-certification-test"></a>Een certificeringstest uitvoeren

Nadat u de parameterwaarden voor uw VM-afbeelding in het certificeringshulpprogramma hebt opgegeven, selecteert u **Verbinding testen** om een geldige verbinding met uw VM te garanderen. Nadat een verbinding is geverifieerd, selecteert u **Volgende** om de test te starten.  Wanneer de test is voltooid, wordt een tabel weergegeven met de testresultaten (Pass/Fail/Warning).  In het volgende voorbeeld worden de testresultaten voor een Linux VM-test weergegeven. 

![Certificeringstestresultaten voor Linux VM-afbeelding](./media/publishvm_029.png)

Als een van de tests mislukt, is uw afbeelding *niet* gecertificeerd. Bekijk in dit geval de vereisten en foutberichten, breng de aangegeven wijzigingen aan en voer de test opnieuw uit. 

Na de geautomatiseerde test moet u aanvullende informatie over uw VM-afbeelding op het **scherm Vragenlijst** verstrekken.  Het bevat twee tabbladen die u moet voltooien.  Het tabblad **Algemene beoordeling** bevat **true/false-vragen,** terwijl de **kernelcustomization** meerdere selectie- en vrijevragen bevat.  Vul de vragen op beide tabbladen in en selecteer **Volgende**.

![Vragenlijst certificeringstool](./media/publishvm_030.png)

Het laatste scherm stelt u in staat om aanvullende informatie te verstrekken, zoals SSH-toegangsinformatie voor een Linux VM-afbeelding en een uitleg voor mislukte beoordelingen als u uitzonderingen zoekt. 

Klik tot slot op **Rapport genereren** om de testresultaten en logbestanden voor de uitgevoerde testcases te downloaden, naast uw antwoorden op de vragenlijst. Sla de resultaten op in dezelfde container als uw VHD(s).

![Testresultaten van certificering opslaan](./media/publishvm_031.png)


## <a name="next-steps"></a>Volgende stappen

Vervolgens genereert u [een uniforme resource-id (URI) voor elke VHD](./cpp-get-sas-uri.md) die u aan de marktplaats indient. 
