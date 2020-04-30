---
title: Uw VM-installatie kopie voor de Azure Marketplace certificeren
description: Hierin wordt uitgelegd hoe u een VM-installatie kopie kunt testen en verzenden voor Azure Marketplace-certificering.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: dsindona
ms.openlocfilehash: 81dec159a8a84adce97750666baba455ae3fdd93
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147080"
---
# <a name="certify-your-vm-image"></a>Uw VM-installatie kopie certificeren

> [!IMPORTANT]
> Vanaf 13 april 2020 gaan we het beheer van uw Azure Virtual Machine-aanbiedingen naar het partner centrum verplaatsen. Na de migratie maakt en beheert u uw aanbiedingen in partner centrum. Volg de instructies in [certificering van Azure VM-installatie kopie](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-image-certification) om uw gemigreerde aanbiedingen te beheren.

Nadat u uw virtuele machine (VM) hebt gemaakt en geïmplementeerd, moet u de VM-installatie kopie testen en verzenden voor Azure Marketplace-certificering. In dit artikel wordt uitgelegd waar u het *certificerings test hulpprogramma voor Azure Certified*, hoe u dit hulp programma kunt gebruiken om uw VM-installatie kopie te certificeren en hoe u de verificatie resultaten uploadt naar de Azure-container waarin uw vhd's zich bevinden. 


## <a name="download-and-run-the-certification-test-tool"></a>Het hulp programma voor certificerings test downloaden en uitvoeren

Het certificerings test hulpprogramma voor Azure Certified wordt uitgevoerd op een lokale Windows-computer, maar test een op Azure gebaseerde Windows-of Linux-VM.  Er wordt gecontroleerd of uw VM-installatie kopie van de gebruiker compatibel is met Microsoft Azure, dat is voldaan aan de richt lijnen en vereisten rond het voorbereiden van uw VHD. De uitvoer van het hulp programma is een compatibiliteits rapport dat u uploadt naar de [Cloud Partner-Portal](https://cloudpartner.azure.com) om VM-certificering aan te vragen.

1. Down load en installeer het meest recente [certificerings test programma voor Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299). 
2. Open het certificerings programma en klik vervolgens op **nieuwe test starten**.
3. Voer in het scherm **test informatie** een **test naam** in voor de test uitvoering.
4. Selecteer het **platform** voor uw virtuele machine, `Windows Server` ofwel `Linux`of. De platform keuze is van invloed op de resterende opties.
5. Als uw virtuele machine gebruikmaakt van deze database service, selecteert u het selectie vakje **testen voor Azure SQL database** .

   ![Begin pagina CERT-test programma](./media/publishvm_025.png)


## <a name="connect-the-certification-tool-to-a-vm-image"></a>Het certificerings hulpprogramma verbinden met een VM-installatie kopie

  Het hulp programma maakt verbinding met Vm's op basis van Windows met [Power shell](https://docs.microsoft.com/powershell/) en maakt verbinding met virtuele Linux-machines via [SSH.net](https://www.ssh.com/ssh/protocol/).

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Verbind het certificerings programma met een Linux VM-installatie kopie

1. Selecteer de **SSH-verificatie** modus `Password Authentication` : `key File Authentication`of.
2. Als u verificatie op basis van wacht woorden gebruikt, voert u waarden in voor de **DNS-naam van de virtuele machine**, de **gebruikers naam**en het **wacht woord**.  Desgewenst kunt u het standaard-SSH- **poort** nummer wijzigen.

     ![Wachtwoord verificatie van installatie kopie van Linux-VM](./media/publishvm_026.png)

3. Als u gebruikmaakt van verificatie op basis van een sleutel bestand, voert u waarden in voor de **DNS-naam van de virtuele machine**, de **gebruikers naam**en de locatie van de **persoonlijke sleutel** .  U kunt desgewenst een **wachtwoordzin** opgeven of het standaard-SSH- **poort** nummer wijzigen.

     ![Bestands verificatie van installatie kopie van Linux-VM](./media/publishvm_027.png)

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Het certificerings hulpprogramma verbinden met een VM-installatie kopie op basis van Windows**
1. Voer de volledig gekwalificeerde **VM-DNS-naam** in ( `MyVMName.Cloudapp.net`bijvoorbeeld).
2. Voer waarden in voor de **gebruikers naam** en het **wacht woord**.

   ![Wachtwoord verificatie van Windows VM-installatie kopie](./media/publishvm_028.png)


## <a name="run-a-certification-test"></a>Een certificerings test uitvoeren

Nadat u de parameter waarden voor uw VM-installatie kopie in het certificerings programma hebt opgegeven, selecteert u **verbinding testen** om te zorgen voor een geldige verbinding met uw VM. Nadat een verbinding is geverifieerd, selecteert u **volgende** om de test te starten.  Wanneer de test is voltooid, wordt een tabel weer gegeven met de test resultaten (pass/fail/Warning).  In het volgende voor beeld ziet u de test resultaten voor een Linux VM-test. 

![Resultaten van certificerings test voor Linux VM-installatie kopie](./media/publishvm_029.png)

Als een van de tests mislukt, is uw installatie kopie *niet* gecertificeerd. In dit geval raadpleegt u de vereisten en fout berichten, brengt u de aangegeven wijzigingen aan en voert u de test opnieuw uit. 

Na de geautomatiseerde test moet u aanvullende informatie over de VM-installatie kopie op het **vragenlijst** scherm opgeven.  Het bevat twee tabbladen die u moet volt ooien.  Op het tabblad **algemene beoordeling** vindt u vragen over **waar/onwaar** , terwijl de **kernel-aanpassing** meerdere selectie-en vrije-vorm vragen bevat.  Voltooi de vragen op beide tabbladen en selecteer vervolgens **volgende**.

![Vragen lijst voor certificerings Programma's](./media/publishvm_030.png)

In het laatste scherm kunt u aanvullende informatie opgeven, zoals SSH-toegangs gegevens voor een Linux VM-installatie kopie en een uitleg voor eventuele mislukte beoordelingen als u uitzonde ringen zoekt. 

Klik ten slotte op **rapport genereren** om de test resultaten en logboek bestanden voor de uitgevoerde test cases te downloaden naast uw antwoorden op de vragen lijst. Sla de resultaten op in dezelfde container als uw VHD (s).

![Resultaten van certificerings test opslaan](./media/publishvm_031.png)


## <a name="next-steps"></a>Volgende stappen

Vervolgens [genereert u een Uniform Resource Identifier (URI) voor elke VHD](./cpp-get-sas-uri.md) die u naar de Marketplace verzendt. 
