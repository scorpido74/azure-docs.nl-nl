---
title: Verbinding maken met uw virtuele machine op basis van Microsoft Azure | Azure Marketplace
description: Hierin wordt uitgelegd hoe u verbinding maakt met de nieuwe virtuele machine die is gemaakt in Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: 3256115821abf5e81d04268ffd2eb310d213ab06
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432014"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>Verbinding maken met uw virtuele machine op basis van Azure

In dit artikel wordt uitgelegd hoe u verbinding maakt met en hoe u zich aanmeldt bij de virtuele machines (Vm's) die u hebt gemaakt in Azure.  Zodra u verbinding hebt gemaakt, kunt u met de virtuele machine werken alsof u lokaal bent aangemeld bij de hostserver. 

## <a name="connect-to-a-windows-based-vm"></a>Verbinding maken met een VM op basis van Windows

U gebruikt de extern bureau blad-client om verbinding te maken met de op Windows gebaseerde VM die wordt gehost op Azure.  De meeste versies van Windows bevatten systeem eigen ondersteuning voor Remote Desktop Protocol (RDP).  Voor andere computers kunt u meer informatie vinden over clients in [extern bureaublad-clients](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).  

In het volgende artikel vindt u informatie over het gebruik van de ingebouwde Windows RDP-ondersteuning om verbinding te maken met uw VM: [verbinding maken en aanmelden bij een virtuele machine van Azure waarop Windows wordt uitgevoerd](../../../virtual-machines/windows/connect-logon.md).  

>[!TIP]
> U kunt tijdens het proces beveiligings waarschuwingen ontvangen, bijvoorbeeld dat het RDP-bestand van een onbekende uitgever is of dat uw gebruikers referenties niet kunnen worden geverifieerd.  Het is veilig om deze waarschuwingen te negeren.


## <a name="connect-to-a-linux-based-vm"></a>Verbinding maken met een op Linux gebaseerde VM

Als u verbinding wilt maken met de op Linux gebaseerde VM, hebt u een SSH-client (Secure Shell Protocol) nodig.  Deze bespreking maakt gebruik van de gratis [putty](https://www.ssh.com/ssh/putty/) shh-Terminal.

1. Ga naar de [Azure Portal](https://ms.portal.azure.com). Zoek en selecteer **virtuele machines**. 
2. Selecteer de virtuele machine waarmee u verbinding wilt maken.  
3. **Start** de virtuele machine als deze nog niet wordt uitgevoerd.
4. Klik op de naam van de virtuele machine om de pagina **overzicht** te openen.
5. Noteer het open bare IP-adres en de DNS-naam van uw virtuele machine.  (Als deze waarden niet zijn ingesteld, moet u [een netwerk interface maken](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)

   ![VM-overzichts instellingen](./media/publishvm_019.png)
 
6. Open de PuTTy-toepassing.  
7. Voer in het dialoog venster PuTTy-configuratie het IP-adres of de DNS-naam van uw virtuele machine in. 

   ![PuTTy-Terminal instellingen](./media/publishvm_020.png)
 
8. Klik op **openen** om een putty-Terminal te openen.  
9. Wanneer u hierom wordt gevraagd, voert u de account naam en het wacht woord in van uw Linux-VM-account. 

Als u verbindings problemen ondervindt, raadpleegt u de documentatie voor uw SSH-client, bijvoorbeeld [hoofd stuk 10: algemene fout berichten](https://www.ssh.com/ssh/putty/putty-manuals).

Zie [extern bureaublad installeren en configureren om verbinding te maken met een virtuele Linux-machine in azure](../../../virtual-machines/linux/use-remote-desktop.md)voor meer informatie, zoals het toevoegen van een bureau blad aan een ingerichte Linux VM.


## <a name="stop-unused-vms"></a>Ongebruikte Vm's stoppen
Azure-facturen voor VM-hosting wanneer een virtuele machine wordt uitgevoerd *of niet actief*is.  Daarom is het best practice om Vm's te stoppen die momenteel niet worden gebruikt.  Testen, back-ups of buiten gebruik gestelde Vm's zijn bijvoorbeeld kandidaten voor het afsluiten. Voer de volgende stappen uit om een virtuele machine af te sluiten:

1. Selecteer op de Blade **virtuele machines** de virtuele machine die u wilt stoppen. 
2. Klik in de werk balk aan de bovenkant van de pagina op de knop **stoppen** .

   ![Een VM stoppen](./media/publishvm_018.png)

Azure stopt snel de virtuele machine in een proces met de naam *detoewijzing*, waardoor niet alleen het besturings systeem op de virtuele machine wordt afgesloten, maar ook de hardware en netwerk bronnen die eerder zijn ingericht, worden vrijgegeven.

Als u later een gestopt VM opnieuw wilt activeren, selecteert u deze en klikt u op de knop **starten** .


## <a name="next-steps"></a>Volgende stappen

Nadat u extern bent verbonden, bent u klaar om [uw VM te configureren](./cpp-configure-vm.md).
