---
title: Een Azure Red Hat open Shift 4,3-cluster maken | Microsoft Docs
description: Een cluster maken met Azure Red Hat open Shift 3,11
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: Aro, open Shift, AZ Aro, Red Hat, cli
ms.openlocfilehash: 3c336a1fbfb9f991ff824e8deafe84f3d899771d
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082826"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>Een Azure Red Hat open Shift 4,3-cluster maken, openen en beheren

> [!IMPORTANT]
> Azure Red Hat open Shift 4,3 is momenteel alleen beschikbaar als persoonlijke preview in VS-Oost. Privé preview-acceptatie is alleen via de uitnodiging. Zorg ervoor dat u uw abonnement registreert voordat u deze functie inschakelt: [Azure Red Hat open Shift-inschrijving voor preview](https://aka.ms/aro-preview-register)

> [!NOTE]
> Preview-functies zijn self-service en worden aangeboden als en beschikbaar en zijn uitgesloten van de Service Level Agreement (SLA) en beperkte garantie. De functies zijn daarom niet bedoeld voor productie gebruik.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om een Azure Red Hat open Shift 4,3-cluster te maken:

- Azure CLI-versie 2.0.72 of hoger
  
- De ' AZ Aro-extensie

- Een virtueel netwerk met twee lege subnetten die elk zonder een netwerk beveiligings groep zijn gekoppeld.  Uw cluster wordt geïmplementeerd in deze subnetten.

- Een geclusterde AAD-toepassing (client-ID en geheim), Service-Principal of voldoende AAD-machtigingen voor `az aro create` om automatisch een AAD-toepassing en Service-Principal te maken.

- De services principal van de RP-service en de Cluster-service moeten elk de rol Inzender hebben op het virtuele cluster netwerk.  Als u de rol ' gebruikers toegangs beheerder ' op het virtuele netwerk hebt, `az aro create` de roltoewijzingen automatisch voor u instellen.

### <a name="install-the-az-aro-extension"></a>De ' AZ Aro-extensie installeren
Met de `az aro`-extensie kunt u Azure Red Hat open Shift-clusters rechtstreeks maken, openen en verwijderen vanaf de opdracht regel met behulp van de Azure CLI.

> [!Note] 
> De uitbrei ding van de `az aro` is actueel als preview-versie. Dit kan in een toekomstige versie worden gewijzigd of verwijderd.
> Als u zich wilt aanmelden voor de `az aro` extensie, moet u de resource provider voor `Microsoft.RedHatOpenShift` registreren.
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. Aanmelden bij Azure.

   ```console
   az login
   ```

2. Voer de volgende opdracht uit om de `az aro`-extensie te installeren:

   ```console
   az extension add --source https://arosvc.blob.core.windows.net/az-preview/aro-0.1.0-py2.py3-none-any.whl
   ```

3. Controleer of de ARO-extensie is geregistreerd.

   ```console
   az -v
   ...
   Extensions:
   aro                                0.1.0
   ...
   ```
  
### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Een virtueel netwerk met twee lege subnetten maken

Volg deze stappen om een virtueel netwerk te maken met twee lege subnetten.

1. Stel de volgende variabelen in.

   ```console
   LOCATION=eastus        #the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    #the name of the resource group where you want to create your cluster
   CLUSTER=cluster        #the name of your cluster
   ```

2. Maak een resource groep voor uw cluster.

   ```console
   az group create -g "$RESOURCEGROUP" -l $LOCATION
   ```

3. Maak het virtuele netwerk.

   ```console
   az network vnet create \
     -g "$RESOURCEGROUP" \
     -n vnet \
     --address-prefixes 10.0.0.0/9 \
     >/dev/null
   ```

4. Voeg twee lege subnetten toe aan het virtuele netwerk.

   ```console
    for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
     az network vnet subnet create \
       -g "$RESOURCEGROUP" \
       --vnet-name vnet \
       -n "$subnet" \
       --address-prefixes 10.$((RANDOM & 127)).$((RANDOM & 255)).0/24 \
       --service-endpoints Microsoft.ContainerRegistry \
       >/dev/null
   done
   ```

5. Schakel netwerk beleid voor privé koppelings service uit op uw virtuele netwerk en subnetten. Dit is een vereiste voor de ARO-service voor toegang tot en beheer van het cluster.

   ```console
   az network vnet subnet update \
     -g "$RESOURCEGROUP" \
     --vnet-name vnet \
     -n "$CLUSTER-master" \
     --disable-private-link-service-network-policies true \
     >/dev/null
   ```

## <a name="create-a-cluster"></a>Een cluster maken

Voer de volgende opdracht uit om een cluster te maken.

```console
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet vnet \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker"
```

>[!NOTE]
> Het maken van een cluster duurt normaal gesp roken ongeveer 35 minuten.

## <a name="access-the-cluster-console"></a>Toegang tot de cluster console

U vindt de cluster console-URL (van het formulier `https://console-openshift-console.apps.<random>.<location>.aroapp.io/`) onder de Azure Red Hat open Shift 4,3-cluster resource. Voer de volgende opdracht uit om de resource weer te geven:

```console
az aro list -o table
```

U kunt zich aanmelden bij het cluster met behulp van de `kubeadmin` gebruiker.  Voer de volgende opdracht uit om het wacht woord voor de `kubeadmin` gebruiker te zoeken:

```dotnetcli
az aro list-credentials -g "$RESOURCEGROUP" -n "$CLUSTER"
```

## <a name="delete-a-cluster"></a>Een cluster verwijderen

Voer de volgende opdracht uit om een cluster te verwijderen.

```console
az aro delete -g "$RESOURCEGROUP" -n "$CLUSTER"

# (optional)
for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
  az network vnet subnet delete -g "$RESOURCEGROUP" --vnet-name vnet -n "$subnet"
done
```