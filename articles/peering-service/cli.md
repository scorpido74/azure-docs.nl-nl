---
title: Een preview-verbinding voor peering service registreren met behulp van de Azure CLI
description: Meer informatie over het registreren van een peering service-verbinding met behulp van de Azure CLI
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/2/2020
ms.author: derekol
ms.openlocfilehash: fbb9a6237aafe1c1b7c94d9dd23ad4c3665c0c73
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84872130"
---
# <a name="register-a-peering-service-connection-by-using-the-azure-cli"></a>Een peering service-verbinding registreren met behulp van de Azure CLI

Azure peering service is een netwerk service die de connectiviteit van klanten verbetert met micro soft-Cloud Services, zoals Office 365, Dynamics 365, software as a Service (SaaS)-Services, Azure of andere micro soft-services die toegankelijk zijn via het open bare Internet. In dit artikel leert u hoe u een peering service-verbinding kunt registreren met behulp van de Azure CLI.

Als u nog geen abonnement op Azure hebt, maak dan nu een [account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0.28 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Vereisten 

U moet het volgende hebben:

### <a name="azure-account"></a>Azure-account

U moet een geldig en actief Microsoft Azure-account hebben. Dit account is vereist voor het instellen van de peering-service verbinding. Peering-service is een resource binnen Azure-abonnementen.

### <a name="connectivity-provider"></a>Connectiveitsprovider

U kunt samen werken met een Internet serviceprovider of een Internet Exchange-partner om peering service te verkrijgen om verbinding te maken met uw netwerk met het micro soft-netwerk.

Zorg ervoor dat de connectiviteits providers zijn gekoppeld aan micro soft.

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. Meld u aan bij uw Azure-account en selecteer uw abonnement

Meld u aan bij uw Azure-account om de configuratie te starten. Als u de optie Cloud Shell **proberen** te gebruiken, bent u automatisch aangemeld. Gebruik de volgende voor beelden om u te helpen verbinding te maken.

```azurecli-interactive
az login
```

Controleer de abonnementen voor het account.

```azurecli-interactive
az account list
```

Selecteer het abonnement waarvoor u de peering service-verbinding wilt registreren.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

Als u nog geen resource groep hebt, moet u er een maken voordat u de verbinding van de peering-service registreert. U kunt een resource groep maken door de volgende opdracht uit te voeren:

```azurecli-interactive
az group create -n MyResourceGroup -l "West US"
```

### <a name="2-register-your-subscription-with-the-resource-provider-and-feature-flag"></a>2. Registreer uw abonnement met de resource provider en functie vlag

Voordat u verdergaat met de stappen voor het registreren van de peering service-verbinding met behulp van de Azure CLI, registreert u uw abonnement met de resource provider en functie vlag door gebruik te maken van de Azure CLI. De Azure CLI-opdrachten worden hier opgegeven:

```azurecli-interactive

az feature register --namespace Microsoft.Peering --name AllowPeeringService

```

### <a name="3-register-the-peering-service-connection"></a>3. de peering service-verbinding registreren

Registreer de peering service-verbinding met behulp van de volgende reeks opdrachten via de Azure CLI. In dit voor beeld wordt de peering service-verbinding met de naam myPeeringService geregistreerd.

```azurecli-interactive
az peering service create : Create peering service\
  --location -l \
  --name myPeeringService\
  --resource-group -g MyResourceGroup\
  --peering-service-location\
  --peering-service-provider\
  --tags
```

### <a name="4-register-the-prefix"></a>4. het voor voegsel registreren

Registreer het voor voegsel dat wordt verschaft door de connectiviteits provider door de volgende opdrachten uit te voeren via de Azure CLI. In dit voor beeld wordt het voor voegsel geregistreerd met de naam myPrefix.

```azurecli-interactive
az peering service prefix create \
  --name  myPrefix\
  --peering-service-name myPeeringService\
  --resource-group  -g myResourceGroup\
```

## <a name="next-steps"></a>Volgende stappen

- Zie [verbinding met peering service](connection.md)voor meer informatie over peering service-verbinding.
- Zie [telemetrie-verbinding met peering service](connection-telemetry.md)voor meer informatie over de telemetrie van de peering-service verbinding.
- Zie [verbindings-telemetrie meten](measure-connection-telemetry.md)om telemetrie te meten.
- Als u de verbinding wilt registreren met behulp van Azure PowerShell, raadpleegt u [een peering service-verbinding registreren-Azure PowerShell](powershell.md).
- Als u de verbinding wilt registreren met behulp van de Azure Portal, raadpleegt u [een peering service-verbinding registreren-Azure Portal](azure-portal.md).
