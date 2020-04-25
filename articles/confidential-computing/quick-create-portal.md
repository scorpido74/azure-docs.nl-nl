---
title: 'Snelstartgids: een virtuele machine van Azure vertrouwelijk computing maken in de Azure Portal'
description: Ga aan de slag met uw implementaties door te leren hoe u snel een computer met een vertrouwelijke computing maakt in de Azure Portal.
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 04/23/2020
ms.author: JenCook
ms.openlocfilehash: 49824b324df044ae2883ab6844dd321be27cbcbf
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82149240"
---
# <a name="quickstart-deploy-an-azure-confidential-computing-vm-in-the-azure-portal"></a>Snelstartgids: een Azure-machine voor vertrouwelijke computing implementeren in de Azure Portal

Ga aan de slag met Azure vertrouwelijk Computing met behulp van de Azure Portal voor het maken van een virtuele machine (VM) die wordt ondersteund door Intel SGX. Vervolgens installeert u de open enclave Software Development Kit (SDK) om uw ontwikkel omgeving in te stellen. 

Deze zelf studie wordt aanbevolen voor u als u geïnteresseerd bent in de implementatie van een computer met een vertrouwelijke Compute met aangepaste configuratie. Anders kunt u het beste de [implementatie stappen voor de virtuele machine met vertrouwelijke computing volgen voor de micro soft Commercial Marketplace](quick-create-marketplace.md).


## <a name="prerequisites"></a>Vereisten

Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) voordat u begint.

> [!NOTE]
> Accounts voor gratis proef versies hebben geen toegang tot de virtuele machines die in deze zelf studie worden gebruikt. Voer een upgrade uit naar een abonnement met betalen per gebruik.


## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Selecteer bovenaan **een resource maken**.

1. In het deel venster **Marketplace** selecteert u **Compute** aan de linkerkant.

1. Zoek en selecteer de **virtuele machine**.

    ![Een virtuele machine implementeren](media/quick-create-portal/compute-virtual-machine.png)

1. Selecteer op de pagina land van virtuele machine **maken**.


## <a name="configure-a-confidential-computing-virtual-machine"></a>Een virtuele machine voor vertrouwelijke computing configureren

1. Selecteer in het tabblad **Basisinformatie** uw **Abonnement** en **Resourcegroep**.

1. Voer voor de naam van de **virtuele machine**een naam in voor de nieuwe VM.

1. Typ of selecteer de volgende waarden:

   * **Regio**: Selecteer de Azure-regio die het meest geschikt is voor u.

        > [!NOTE]
        > Virtuele machines met een vertrouwelijke reken kracht worden alleen uitgevoerd op gespecialiseerde hardware die beschikbaar is in bepaalde regio's. Zie [beschik bare regio's](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)voor de meest recente beschik bare regio's voor vm's uit de DCsv2-serie.

1. Configureer de installatie kopie van het besturings systeem die u wilt gebruiken voor de virtuele machine.

    * **Afbeelding kiezen**: Selecteer voor deze zelf studie Ubuntu 18,04 LTS. U kunt ook Windows Server 2019, Windows Server 2016 of Ubuntu 16,04 LTS selecteren. Als u ervoor kiest om dit te doen, wordt u in deze zelf studie omgeleid.
    
    * **De afbeelding in-/uitschakelen voor generatie 2: de**virtuele machines met een vertrouwelijke Compute worden alleen uitgevoerd op installatie kopieën van [generatie 2](../virtual-machines/linux/generation-2.md) . Zorg ervoor dat de installatie kopie die u selecteert een installatie kopie van generatie 1 is. Klik op het tabblad **Geavanceerd** hierboven voor het configureren van de virtuele machine. Schuif omlaag totdat u de sectie met de naam ' VM-generatie ' hebt gevonden. Selecteer gen 2 en ga vervolgens terug naar het tabblad **basis beginselen** .
    

        ![Tabblad Geavanceerd](media/quick-create-portal/advanced-tab-virtual-machine.png)


        ![VM-generatie](media/quick-create-portal/gen2-virtual-machine.png)

    * **Terug naar de basis configuratie**: Ga terug naar het tabblad **basis beginselen** met de navigatie bovenaan.

1. Kies een virtuele machine met de mogelijkheden voor het instellen van een vertrouwelijke computer in de grootte kiezer door **grootte wijzigen**te kiezen. Klik in de optie voor de grootte van virtuele machines op **alle filters wissen**. Kies **filter toevoegen**, selecteer **familie** voor het filter type en selecteer vervolgens alleen **vertrouwelijke Compute**.

    ![Vm's uit de DCsv2-serie](media/quick-create-portal/dcsv2-virtual-machines.png)

    > [!TIP]
    > U ziet de grootten **DC1s_v2**, **DC2s_v2**, **DC4s_V2**en **DC8_v2**. Dit zijn de enige grootte van virtuele machines die op dit moment vertrouwelijke computing ondersteunen. [Meer informatie](virtual-machine-solutions.md).

1. Vul de volgende gegevens in:

   * **Verificatie type**: Selecteer de **open bare SSH-sleutel** als u een virtuele Linux-machine maakt. 

        > [!NOTE]
         > U hebt de keuze om voor de verificatie een openbare SSH-sleutel of een wachtwoord te gebruiken. SSH is veiliger. Zie [SSH-sleutels maken in Linux en Mac voor virtuele Linux-machines in Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys) voor instructies over het maken van een SSH-sleutel.

    * **Gebruikers naam**: Voer de naam van de beheerder voor de virtuele machine in.

    * **Open bare SSH-sleutel**: Voer, indien van toepassing, de open bare RSA-sleutel in.
    
    * **Wacht woord**: Voer uw wacht woord in voor verificatie, indien van toepassing.

    * **Open bare binnenkomende poorten**: Kies **geselecteerde poorten toestaan** en selecteer **SSH (22)** en **http (80)** in de lijst **open bare binnenkomende poorten selecteren** . Als u een virtuele Windows-machine implementeert, selecteert u **http (80)** en **RDP (3389)**. In deze Quick start is deze stap nodig om verbinding te maken met de virtuele machine en de open enclave SDK-configuratie te volt ooien. 

     ![Poorten voor inkomend verkeer](media/quick-create-portal/inbound-port-virtual-machine.png)


1. Breng wijzigingen aan op het tabblad **schijven** .

   * Als u een **DC1s_v2**, **DC2s_v2**, **DC4s_V2** virtuele machine hebt gekozen, kiest u een schijf type dat ofwel **Standard-SSD** of **Premium-SSD**is. 
   * Als u een virtuele machine van **DC8_v2** hebt gekozen, kiest u **Standard-SSD** als schijf type.

1. Breng de gewenste wijzigingen aan in de instellingen op de volgende tabbladen of behoud de standaard instellingen.

    * **Netwerken**
    * **Beheer**
    * **Gastconfiguratie**
    * **Tags**

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

[!div class="mx-imgBorder"]
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
