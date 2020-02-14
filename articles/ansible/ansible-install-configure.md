---
title: 'Quick Start: Ansible installeren op virtuele Linux-machines in azure'
description: In deze Quick Start leert u hoe u Ansible kunt installeren en configureren voor het beheer van Azure-resources op Ubuntu, CentOS en SLES
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 44007000475793005560914fd816cd0c16927f9a
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202408"
---
# <a name="quickstart-install-ansible-on-linux-virtual-machines-in-azure"></a>Snelstartgids: Ansible installeren op virtuele Linux-machines in azure

U kunt Ansible ook gebruiken om de implementatie en configuratie van resources in uw omgeving te automatiseren. In dit artikel wordt beschreven hoe u Ansible configureert voor een aantal van de meest voorkomende Linux-distributies. Als u Ansible wilt installeren op andere distributies, past u de geïnstalleerde pakketten voor uw specifieke platform aan. 

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-sp.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
- **Toegang tot Linux of een virtuele Linux-machine**: als u geen Linux-machine hebt, maakt u een [virtuele Linux-machine](/azure/virtual-network/quick-create-cli).

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Ansible installeren op een virtuele Linux-machine in Azure

Meld u aan bij uw Linux-machine en selecteer een van de volgende versies voor instructies voor het installeren van Ansible:

- [CentOS 7.4](#centos-74)
- Ubuntu 16.04 LTS
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

In deze sectie configureert u CentOS voor het gebruik van Ansible.

1. Open een terminalvenster.

1. Voer de volgende opdracht in om de vereiste pakketten te installeren voor de Azure python SDK-modules:

    ```bash
    sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
    sudo yum install -y python-pip python-wheel
    ```

1. Voer de volgende opdracht in om de vereiste pakketten Ansible te installeren:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Maak de Azure-referenties](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

In deze sectie configureert u Ubuntu voor het gebruik van Ansible.

1. Open een terminalvenster.

1. Voer de volgende opdracht in om de vereiste pakketten te installeren voor de Azure python SDK-modules:

    ```bash
    sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip
    ```

1. Voer de volgende opdracht in om de vereiste pakketten Ansible te installeren:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Maak de Azure-referenties](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

In deze sectie configureert u SLES voor het gebruik van Ansible.

1. Open een terminalvenster.

1. Voer de volgende opdracht in om de vereiste pakketten te installeren voor de Azure python SDK-modules:

    ```bash
    sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
        python-devel libopenssl-devel libtool python-pip python-setuptools
    ```

1. Voer de volgende opdracht in om de vereiste pakketten Ansible te installeren:

    ```bash
    sudo pip install ansible[azure]
    ```

1. Voer de volgende opdracht in om een conflicterende python Cryptography-pakket te verwijderen:

    ```bash
    sudo pip uninstall -y cryptography
    ```

1. [Maak de Azure-referenties](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Azure-referenties maken

Als u de Ansible-referenties wilt configureren, hebt u de volgende informatie nodig:

* Uw Azure-abonnements-ID 
* De waarden van de Service-Principal

Als u Ansible Tower of Jenkins gebruikt, declareert u de Service-Principal-waarden als omgevings variabelen.

Configureer de Ansible-referenties met behulp van een van de volgende technieken:

- [Een Ansible-referentiebestand maken](#file-credentials)
- [Ansible-omgevingsvariabelen gebruiken](#env-credentials)

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/> Ansible-referentiebestand maken

In deze sectie maakt u een lokaal referentie bestand om referenties op te geven voor Ansible. 

Zie [referenties voor Azure-modules opgeven](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules)voor meer informatie over het definiëren van Ansible-referenties.

1. Voor een ontwikkel omgeving maakt u een bestand met de naam `credentials` op de virtuele machine van de host:

    ```bash
    mkdir ~/.azure
    vi ~/.azure/credentials
    ```

1. Voeg de volgende regels in het bestand in. Vervang de tijdelijke aanduidingen door de waarden van de Service-Principal.

    ```bash
    [default]
    subscription_id=<your-subscription_id>
    client_id=<security-principal-appid>
    secret=<security-principal-password>
    tenant=<security-principal-tenant>
    ```

1. Sla het bestand op en sluit het.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Ansible-omgevingsvariabelen gebruiken

In deze sectie exporteert u de Service-Principal-waarden voor het configureren van uw Ansible-referenties.

1. Open een terminalvenster.

1. Exporteer de waarden van de Service-Principal:

    ```bash
    export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
    export AZURE_CLIENT_ID=<security-principal-appid>
    export AZURE_SECRET=<security-principal-password>
    export AZURE_TENANT=<security-principal-tenant>
    ```

## <a name="verify-the-configuration"></a>De configuratie controleren

Gebruik Ansible om een Azure-resource groep te maken om de geslaagde configuratie te controleren.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Snelstartgids: een virtuele Linux-machine configureren in azure met behulp van Ansible](./ansible-create-vm.md)