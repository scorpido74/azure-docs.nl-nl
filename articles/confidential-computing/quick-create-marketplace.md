---
title: 'Snelstartgids: een virtuele machine voor Azure-computers met de Marketplace maken'
description: Ga aan de slag met uw implementaties door te leren hoe u snel een computer met een vertrouwelijke computing maakt met Marketplace.
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: 62ee8ddb03c00ba7d6ae495ee360c708b00c615c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82208632"
---
# <a name="quickstart-deploy-an-azure-confidential-computing-vm-in-the-marketplace"></a>Snelstartgids: een Azure vertrouwelijk computing-VM implementeren in Marketplace

Ga aan de slag met Azure vertrouwelijk computing door Azure Marketplace te gebruiken voor het maken van een virtuele machine (VM) die wordt ondersteund door Intel SGX. Vervolgens installeert u de open enclave Software Development Kit (SDK) om uw ontwikkel omgeving in te stellen. 

Deze zelf studie wordt aanbevolen als u snel een virtuele machine voor vertrouwelijke computing wilt implementeren. De virtuele machines worden uitgevoerd op speciale hardware en vereisen dat specifieke configuratie-ingangen worden uitgevoerd zoals bedoeld. De Marketplace-aanbieding die in deze Quick Start wordt beschreven, maakt het gemakkelijker om te implementeren door gebruikers invoer te beperken.

Als u geïnteresseerd bent in de implementatie van een computer met een vertrouwelijke Compute-machine met een meer aangepaste configuratie, volgt u de stappen voor de implementatie van de [Azure Portal vertrouwelijk Compute virtual machine](quick-create-portal.md).

## <a name="prerequisites"></a>Vereisten

Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) voordat u begint.

> [!NOTE]
> Accounts voor gratis proef versies hebben geen toegang tot de virtuele machines die in deze zelf studie worden gebruikt. Voer een upgrade uit naar een abonnement met betalen per gebruik.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Typ bovenaan **Azure vertrouwelijk computing** in de zoek balk.

1. Selecteer **Azure vertrouwelijk Computing (virtuele machine)** in het gedeelte **Marketplace** . 

    ![Marketplace selecteren](media/quick-create-marketplace/portal-search-marketplace.png)    

1. Selecteer op de pagina start van de Azure-onderrekender **maken**.
 

## <a name="configure-your-virtual-machine"></a>Uw virtuele machine configureren

1. Selecteer in het tabblad **Basisinformatie** uw **Abonnement** en **Resourcegroep**. De resource groep moet leeg zijn om een virtuele machine uit deze sjabloon te kunnen implementeren.

1. Typ of selecteer de volgende waarden:

   * **Regio**: Selecteer de Azure-regio die het meest geschikt is voor u.

        > [!NOTE]
        > Virtuele machines met een vertrouwelijke reken kracht worden alleen uitgevoerd op gespecialiseerde hardware die beschikbaar is in bepaalde regio's. Zie [beschik bare regio's](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)voor de meest recente beschik bare regio's voor vm's uit de DCsv2-serie.
    
    * **Afbeelding kiezen**: Selecteer een wille keurige afbeelding. Als u deze specifieke zelf studie wilt volt ooien, selecteert u Ubuntu 18,04 (Gen 2). Anders wordt u omgeleid aan de hand van de onderstaande stappen. 

    * De naam van de **virtuele machine**, voer een naam in voor de nieuwe VM.

    * **Verificatie type**: Selecteer de **open bare SSH-sleutel** als u een virtuele Linux-machine maakt. 

         > [!NOTE]
         > U hebt de keuze om voor de verificatie een openbare SSH-sleutel of een wachtwoord te gebruiken. SSH is veiliger. Zie [SSH-sleutels maken in Linux en Mac voor virtuele Linux-machines in Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys) voor instructies over het maken van een SSH-sleutel.

    * **Gebruikers naam**: Voer de naam van de beheerder voor de virtuele machine in.

    * **Open bare SSH-sleutel**: Voer, indien van toepassing, de open bare RSA-sleutel in.
    
    * **Wacht woord**: Voer uw wacht woord in voor verificatie, indien van toepassing.
 
1. Selecteer de knop **volgende: virtuele-machine instellingen** onder aan het scherm.

    > [!IMPORTANT]
    > Wacht tot de pagina is bijgewerkt. Het is *niet* mogelijk dat er een bericht wordt weer gegeven met de tekst "er zijn DCsv2 vm's in een beperkt aantal regio's beschikbaar." Als dit bericht blijft bestaan, gaat u terug naar de vorige pagina en selecteert u een beschik bare DCsv2-serie.

1. Voor **wijzigings grootte**kiest u een virtuele machine met vertrouwelijke reken mogelijkheden in de grootte kiezer. 

    > [!TIP]
    > U ziet de grootten **DC1s_v2**, **DC2s_v2**, **DC4s_V2**en **DC8_v2**. Dit zijn de enige grootte van virtuele machines die op dit moment vertrouwelijke computing ondersteunen. [Meer informatie](virtual-machine-solutions.md).

1. Selecteer een schijf type voor het **schijf type van het besturings systeem**.

1. Maak voor **Virtual Network**een nieuw item of kies uit een bestaande resource.

1. Maak voor **subnet**een nieuw item of kies uit een bestaande resource.

1. Kies voor **open bare binnenkomende poorten selecteren** **SSH (Linux)/RDP (Windows)**. In deze Quick start is deze stap nodig om verbinding te maken met de virtuele machine en de open enclave SDK-configuratie te volt ooien. 

1. Voor **Diagnostische gegevens over opstarten**, moet u deze uitschakelen voor deze Quick Start. 

1. Selecteer **controleren + maken**.

1. Selecteer in het deelvenster **Controleren + maken** de optie **Maken**.

> [!NOTE]
> Ga verder naar de volgende sectie en ga door met deze zelf studie als u een virtuele Linux-machine hebt geïmplementeerd. Als u een virtuele Windows-machine hebt geïmplementeerd, [volgt u deze stappen om verbinding te maken met uw Windows-VM](../virtual-machines/windows/connect-logon.md) en vervolgens [de OE SDK op Windows te installeren](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md).


## <a name="connect-to-the-linux-vm"></a> Verbinding maken met de virtuele Linux-machine

Als u al een BASH-shell gebruikt, moet u verbinding maken met de virtuele Azure-machine via de opdracht **ssh**. In de volgende opdracht vervangt u de VM-gebruikersnaam en het IP-adres om verbinding te maken met uw virtuele Linux-machine.

```bash
ssh azureadmin@40.55.55.555
```

U vindt het open bare IP-adres van uw virtuele machine in de Azure Portal, onder de sectie Overzicht van uw virtuele machines.

![IP-adres in Azure Portal](media/quick-create-portal/public-ip-virtual-machine.png)

Als u Windows gebruikt en geen BASH-shell hebt, installeert u een SSH-client, zoals PuTTY.

1. [PuTTY downloaden en installeren](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

1. Voer PuTTY uit.

1. Voer het openbare IP-adres van de virtuele machine in op het configuratiescherm van PuTTY.

1. Selecteer **Openen** en voer uw gebruikersnaam en wachtwoord in bij de prompts.

Zie [Een virtuele Linux-machine in Azure maken met behulp van de portal](../virtual-machines/linux/quick-create-portal.md) voor meer informatie over verbinding maken met virtuele Linux-machines.

> [!NOTE]
> Als er een PuTTY-beveiligingswaarschuwing wordt weergegeven dat de hostsleutel van de server niet in het register wordt opgeslagen, kunt u uit de volgende opties kiezen. Als u deze host vertrouwt, selecteert u **Ja** om de sleutel aan de PuTTy-cache toe te voegen en door te gaan met verbinding maken. Als u eenmalig verbinding wilt maken, zonder de sleutel aan de cache toe te voegen, selecteert u **Nee**. Als u deze host niet vertrouwt, selecteert u **Annuleren** om de verbinding te verbreken.

## <a name="install-the-open-enclave-sdk-oe-sdk"></a>De open enclave SDK (OE SDK) installeren<a id="Install"></a>

Volg de stapsgewijze instructies voor het installeren van de [OE SDK](https://github.com/openenclave/openenclave) op uw virtuele machine met de DCsv2-serie waarop een Ubuntu 18,04 LTS gen 2-installatie kopie wordt uitgevoerd. 

Als uw virtuele machine wordt uitgevoerd op Ubuntu 16,04 LTS gen 2, moet u de [installatie-instructies voor Ubuntu 16,04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md)volgen. 

#### <a name="1-configure-the-intel-and-microsoft-apt-repositories"></a>1. Configureer de Intel-en micro soft APT-opslag plaatsen

```bash
echo 'deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main' | sudo tee /etc/apt/sources.list.d/intel-sgx.list
wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | sudo apt-key add -

echo "deb http://apt.llvm.org/bionic/ llvm-toolchain-bionic-7 main" | sudo tee /etc/apt/sources.list.d/llvm-toolchain-bionic-7.list
wget -qO - https://apt.llvm.org/llvm-snapshot.gpg.key | sudo apt-key add -

echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/18.04/prod bionic main" | sudo tee /etc/apt/sources.list.d/msprod.list
wget -qO - https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
```

#### <a name="2-install-the-intel-sgx-dcap-driver"></a>2. Installeer het Intel SGX DCAP-stuur programma

```bash
sudo apt update
sudo apt -y install dkms
wget https://download.01.org/intel-sgx/sgx-dcap/1.4/linux/distro/ubuntuServer18.04/sgx_linux_x64_driver_1.21.bin -O sgx_linux_x64_driver.bin
chmod +x sgx_linux_x64_driver.bin
sudo ./sgx_linux_x64_driver.bin
```

> [!WARNING]
> Gebruik het meest recente Intel SGX DCAP-stuur programma van [de SGX-site van Intel](https://01.org/intel-software-guard-extensions/downloads).

#### <a name="3-install-the-intel-and-open-enclave-packages-and-dependencies"></a>3. Installeer de Intel-en open enclave-pakketten en-afhankelijkheden

```bash
sudo apt -y install clang-7 libssl-dev gdb libsgx-enclave-common libsgx-enclave-common-dev libprotobuf10 libsgx-dcap-ql libsgx-dcap-ql-dev az-dcap-client open-enclave
```

> [!NOTE] 
> Met deze stap wordt ook het [AZ-dcap-client-](https://github.com/microsoft/azure-dcap-client) pakket geïnstalleerd dat nodig is voor het uitvoeren van externe Attestation in Azure.

#### <a name="4-verify-the-open-enclave-sdk-install"></a>4. **Controleer de open ENCLAVE SDK-installatie**

Zie [de open ENCLAVE SDK](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/Linux_using_oe_sdk.md) op github gebruiken voor het controleren en gebruiken van de geïnstalleerde SDK.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de VM niet meer nodig hebt, kunt u de resourcegroep, de machine zelf én alle gerelateerde resources verwijderen. 

Selecteer de resource groep voor de virtuele machine en selecteer vervolgens **verwijderen**. Bevestig de naam van de resource groep om het verwijderen van de resources te volt ooien.

## <a name="next-steps"></a>Volgende stappen

In deze Snelstartgids hebt u een computer voor vertrouwelijke computing geïmplementeerd en de open enclave-SDK geïnstalleerd. Zie [oplossingen op virtual machines](virtual-machine-solutions.md)voor meer informatie over virtuele machines met vertrouwelijke computing in Azure. 

Als u een virtuele Windows-machine hebt geïmplementeerd, leert u hoe u toepassingen kunt bouwen met de [OE SDK-voor beelden voor Windows](https://github.com/openenclave/openenclave/blob/master/samples/README_Windows.md) op github. 

Ontdek hoe u vertrouwelijke computing toepassingen kunt bouwen op Linux door door te gaan met de open enclave SDK Linux-voor beelden op GitHub. 

> [!div class="nextstepaction"]
> [Open enclave SDK-voor beelden bouwen op Linux](https://github.com/openenclave/openenclave/blob/master/samples/README_Linux.md)
