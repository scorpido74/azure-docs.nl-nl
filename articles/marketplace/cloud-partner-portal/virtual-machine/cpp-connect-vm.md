---
title: Verbinding maken met uw virtuele machine op basis van Microsoft Azure | Azure Marketplace
description: Hier wordt uitgelegd hoe u verbinding maken met de nieuwe virtuele machine die op Azure is gemaakt.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: 6f2652a98aa081b62ba80951212ef3a3de513593
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272658"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>Verbinding maken met uw virtuele machine op Azure

> [!IMPORTANT]
> Vanaf 13 april 2020 beginnen we met het verplaatsen van het beheer van uw Azure Virtual Machine-aanbiedingen naar Partner Center. Na de migratie maak en beheer je je aanbiedingen in partnercentrum. Volg de instructies in [Uw technische assets van Azure Virtual Machine maken](https://aka.ms/AzureVMTechAsset) om uw gemigreerde aanbiedingen te beheren.

In dit artikel wordt uitgelegd hoe u verbinding maken met en u aanmelden bij de virtuele machines (VM's) die u op Azure hebt gemaakt.  Zodra u verbinding hebt gemaakt, u met de VM werken alsof u lokaal bent aangemeld bij de hostserver. 

## <a name="connect-to-a-windows-based-vm"></a>Verbinding maken met een windows-vm

U gebruikt de externe desktopclient om verbinding te maken met de op Windows gebaseerde VM die op Azure wordt gehost.  De meeste versies van Windows bevatten native ondersteuning voor het Remote Desktop Protocol (RDP).  Voor andere machines vindt u meer informatie over clients in [Extern bureaublad-clients.](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)  

In het volgende artikel wordt uitgelegd hoe u de ingebouwde Windows RDP-ondersteuning gebruiken om verbinding te maken met uw VM: [Verbinding maken en inloggen op een virtuele Azure-machine met Windows](../../../virtual-machines/windows/connect-logon.md).  

>[!TIP]
> U tijdens het proces beveiligingswaarschuwingen krijgen, bijvoorbeeld dat het RDP-bestand van een onbekende uitgever is of dat uw gebruikersreferenties niet kunnen worden geverifieerd.  Het is veilig om deze waarschuwingen te negeren.


## <a name="connect-to-a-linux-based-vm"></a>Verbinding maken met een op Linux gebaseerde VM

Om de Op Linux gebaseerde VM aan te sluiten, hebt u een Secure Shell Protocol (SSH) client nodig.  Deze discussie zal gebruik maken van de gratis [PuTTY](https://www.ssh.com/ssh/putty/) SHH terminal.

1. Ga naar de [Azure-portal.](https://ms.portal.azure.com) Zoeken naar virtuele machines en selecteer **deze**. 
2. Selecteer de VM waarmee u verbinding wilt maken.  
3. **Start** de VM als deze nog niet actief is.
4. Klik op de naam van de VM om de **overzichtspagina** te openen.
5. Let op het openbare IP-adres en de DNS-naam van uw vm.  (Als deze waarden niet zijn ingesteld, moet u [een netwerkinterface maken](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)

   ![VM-overzichtsinstellingen](./media/publishvm_019.png)
 
6. Open de PuTTY-toepassing.  
7. Voer in het dialoogvenster PuTTY-configuratie het IP-adres of de DNS-naam van uw vm in. 

   ![PuTTY-terminalinstellingen](./media/publishvm_020.png)
 
8. Klik **op Openen** om een PuTTY-terminal te openen.  
9. Wanneer u wordt gevraagd, voert u de accountnaam en het wachtwoord van uw Linux VM-account in. 

Als u verbindingsproblemen ondervindt, raadpleegt u de documentatie voor uw SSH-client, bijvoorbeeld [hoofdstuk 10: algemene foutmeldingen.](https://www.ssh.com/ssh/putty/putty-manuals)

Zie Extern bureaublad installeren en configureren om verbinding te [maken met een Linux-vm in Azure](../../../virtual-machines/linux/use-remote-desktop.md)voor meer informatie, waaronder het toevoegen van een bureaublad aan een ingerichte Linux-vm.


## <a name="stop-unused-vms"></a>Ongebruikte VM's stoppen
Azure-facturen voor VM-hosting wanneer een VM wordt uitgevoerd *of niet actief is.*  Als zodanig is het best om vm's te stoppen die momenteel niet worden gebruikt.  Test, back-up of gepensioneerde VM's zijn bijvoorbeeld kandidaten voor afsluiten. Voer de volgende stappen uit om een virtuele machine af te sluiten:

1. Selecteer op het **blad Virtuele machines** de VM die u wilt stoppen. 
2. Klik op de werkbalk boven aan de pagina op de knop **Stoppen.**

   ![Een VM stoppen](./media/publishvm_018.png)

Azure stopt de VM snel in een proces genaamd *deallocatie*, waarbij niet alleen het besturingssysteem op de VM wordt afgesloten, maar ook de hardware- en netwerkbronnen die er eerder voor zijn ingericht, worden vrijgegeven.

Als u later een gestopte VM opnieuw wilt activeren, selecteert u deze en klikt u op de knop **Start.**


## <a name="next-steps"></a>Volgende stappen

Nadat u op afstand bent verbonden, u [uw vm configureren.](./cpp-configure-vm.md)
