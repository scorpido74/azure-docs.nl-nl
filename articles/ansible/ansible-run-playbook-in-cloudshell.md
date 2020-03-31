---
title: Quickstart - Ansible playbooks uitvoeren via Bash in Azure Cloud Shell
description: In deze quickstart leert u hoe u verschillende Ansible-taken uitvoert met Bash in Azure Cloud Shell
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.date: 04/30/2019
ms.openlocfilehash: 2a938179cf2e07a61749042db32ef9e1c9d843ba
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78247897"
---
# <a name="quickstart-run-ansible-playbooks-via-bash-in-azure-cloud-shell"></a>Snelstart: Ansible playbooks uitvoeren via Bash in Azure Cloud Shell

Azure Cloud Shell is een interactieve, via de browser toegankelijke shell voor het beheren van Azure-resources. Met Cloud Shell u een Opdrachtregel voor Bash of PowerShell gebruiken. In dit artikel gebruikt u Bash binnen Azure Cloud Shell om een Ansible-draaiboek uit te voeren.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Azure Cloud Shell configureren** - Als u nieuw bent in Azure Cloud Shell, raadpleegt u [Snelstarten voor Bash in Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart).
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Automatische configuratie van referenties

Ansible wordt geverifieerd bij Azure wanneer u zich aanmeldt bij Cloud Shell om de infrastructuur te beheren zonder extra configuratie. 

Wanneer u met meerdere abonnementen werkt, geeft u `AZURE_SUBSCRIPTION_ID` het abonnement op dat Ansible gebruikt door de omgevingsvariabele te exporteren. 

Voer de volgende opdracht uit om al uw Azure-abonnementen weer te geven:

```azurecli-interactive
az account list
```

Stel als volgt de `AZURE_SUBSCRIPTION_ID` volgende gegevens in:

```console
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>De configuratie controleren
Als u de geslaagde configuratie wilt verifiëren, gebruikt u Ansible om een Azure-brongroep te maken.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Snelstart: virtuele machine configureren in Azure met Ansible](./ansible-create-vm.md)