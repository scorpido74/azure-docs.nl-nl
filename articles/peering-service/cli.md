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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84872130"
---
# <a name="register-a-peering-service-connection-by-using-the-azure-cli"></a>Een peering service-verbinding registreren met behulp van de Azure CLI

Azure Peering Service is een netwerkservice die de klantconnectiviteit verbetert met Microsoft-cloudservices, zoals Office 365, Dynamics 365, SaaS-services (Software as a Service), Azure of een andere Microsoft-service die toegankelijk is via openbaar internet. In dit artikel leert u hoe u een peering service-verbinding kunt registreren met behulp van de Azure CLI.

Als u nog geen Azure-abonnement hebt, maakt u nu een [account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0.28 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Vereisten 

U moet het volgende hebben:

### <a name="azure-account"></a>Azure-account

U moet beschikken over een geldig en actief Microsoft Azure-account. Dit account is vereist voor het instellen van de Peering Service-verbinding. Peering Service is een resource binnen Azure-abonnementen.

### <a name="connectivity-provider"></a>Connectiveitsprovider

U kunt samenwerken met een internetprovider of een Internet Exchange-partner om Peering Service te verkrijgen om verbinding te maken tussen uw netwerk en het Microsoft-netwerk.

Zorg ervoor dat de connectiviteitsproviders een partnerschap hebben met Microsoft.

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

Registreer het voor voegsel dat wordt verschaft door de connectiviteits provider door de volgende opdrachten uit te voeren via de Azure CLI. In dit voorbeeld wordt het voorvoegsel met de naam myPrefix geregistreerd.

```azurecli-interactive
az peering service prefix create \
  --name  myPrefix\
  --peering-service-name myPeeringService\
  --resource-group  -g myResourceGroup\
```

## <a name="next-steps"></a>Volgende stappen

- Zie [verbinding met peering service](connection.md)voor meer informatie over peering service-verbinding.
- Zie [Peering Service-verbindingstelemetrie](connection-telemetry.md) voor meer informatie over de Peering Service-verbindingstelemetrie.
- Zie [Verbindingstelemetrie meten](measure-connection-telemetry.md) om telemetrie te meten.
- Zie [Een Peering Service-verbinding registreren - Azure PowerShell](powershell.md) als u de verbinding wilt registreren met behulp van Azure PowerShell.
- Als u de verbinding wilt registreren met behulp van de Azure Portal, raadpleegt u [een peering service-verbinding registreren-Azure Portal](azure-portal.md).
