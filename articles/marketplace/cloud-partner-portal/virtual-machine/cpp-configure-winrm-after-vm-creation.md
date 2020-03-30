---
title: WinRM configureren na het maken van azure virtuele machines | Azure Marketplace
description: Hier wordt uitgelegd hoe u Windows Remote Management (WinRM) configureert na het maken van een door Azure gehoste virtuele machine.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: dsindona
ms.openlocfilehash: 673fe1f31f6a8602225e7cde3bf1eb4c3b28b8a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278141"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>WinRM configureren na het maken van virtuele machines

In dit artikel wordt uitgelegd hoe u een bestaande virtuele machine (VM) met Azure configureren om WinRM via HTTPS in te schakelen.  Deze configuratie is alleen van toepassing op vm's in Windows en vereist het volgende proces in twee stappen:

1. Poortverkeer inschakelen voor het WinRM-protocol via HTTPS.  U configureert deze instelling voor uw VM in de Azure-portal.
2. Configureer de VM om WinRM in te schakelen door de meegeleverde PowerShell-scripts uit te voeren.


## <a name="enabling-port-traffic"></a>Havenverkeer inschakelen

Het WinRM-over-HTTPS-protocol maakt gebruik van poort 5986, die standaard niet is ingeschakeld op vooraf geconfigureerde Windows VM's die op de Azure Marketplace worden aangeboden. Als u dit protocol wilt inschakelen, gebruikt u de volgende stappen om een nieuwe regel toe te voegen aan de netwerkbeveiligingsgroep (NSG) met de [Azure-portal.](https://portal.azure.com)  Zie [Beveiligingsgroepen voor](https://docs.microsoft.com/azure/virtual-network/security-overview)meer informatie over NSG's .

1.  Navigeer naar de **virtuele machines van **   <het blad >*vm-naam* >   **> Instellingen/Netwerken**.
2.  Klik op de NSG-naam (in dit voorbeeld **testvm1002)** om de eigenschappen ervan weer te geven:

    ![Eigenschappen van netwerkbeveiligingsgroepen](./media/nsg-properties.png)
 
3. Selecteer **onder Instellingen**de **inkomende beveiligingsregels** om dit blad weer te geven.
4. Klik **op +Toevoegen** om `WinRM_HTTPS` een nieuwe regel te maken die is aangeroepen voor TCP-poort 5986.

    ![Inkomende netwerkbeveiligingsregel toevoegen](./media/nsg-new-rule.png)

5. Klik **op OK** wanneer u klaar bent met het leveren van waarden.  De lijst met binnenkomende beveiligingsregels moet de volgende nieuwe vermeldingen bevatten.

    ![Vermelding van binnenkomende netwerkbeveiligingsregels](./media/nsg-new-inbound-listing.png)


## <a name="configure-vm-to-enable-winrm"></a>VM configureren om WinRM in te schakelen 

Gebruik de volgende stappen om de functie Windows Extern beheer op uw Windows-vm in te schakelen en te configureren.   

1. Een verbinding met Extern bureaublad tot stand brengen met uw door Azure gehoste VM.  Zie [Verbinding maken en aanmelden bij een virtuele Azure-machine met Windows voor](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)meer informatie.  De resterende stappen worden uitgevoerd op uw VM.
2. Download de volgende bestanden en sla ze op in een map op uw virtuele machine:
    - [ConfigureWinRM.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1)
    - [makecert.exe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe)
    - [winrmconf.cmd](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd)
3. Open de **PowerShell-console** met verhoogde bevoegdheden **(Uitvoeren als administrator).** 
4. Voer de volgende opdracht uit en lever de vereiste parameter: de volledig gekwalificeerde domeinnaam (FQDN) voor uw VM: <br/>
   `ConfigureWinRM.ps1 <vm-domain-name>`

    ![Powershell-configuratiescript 1](./media/powershell-file1.png)

    Dit script is afhankelijk van de andere twee bestanden die in dezelfde map.


## <a name="next-steps"></a>Volgende stappen

Zodra u WinRM hebt geconfigureerd, bent u klaar om [uw VM te implementeren vanaf de samenstellende VHD's.](./cpp-deploy-vm-vhd.md)
