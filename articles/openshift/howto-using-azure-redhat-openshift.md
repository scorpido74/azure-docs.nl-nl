---
title: Een Azure Red Hat open Shift 4,3-cluster maken | Microsoft Docs
description: Een cluster maken met Azure Red Hat open Shift 4,3
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: Aro, open Shift, AZ Aro, Red Hat, cli
ms.openlocfilehash: f909c5870be6e394e457ad8f44ea5a253054ffe6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81398897"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>Een Azure Red Hat open Shift 4,3-cluster maken, openen en beheren

> [!IMPORTANT]
> Azure Red Hat open Shift 4,3 is momenteel alleen beschikbaar als particuliere preview in VS-Oost en VS-Oost 2. Privé preview-acceptatie is alleen via de uitnodiging. Zorg ervoor dat u uw abonnement registreert voordat u deze functie inschakelt: [Azure Red Hat open Shift-inschrijving voor preview](https://aka.ms/aro-preview-register)

> [!NOTE]
> Preview-functies zijn self-service en worden aangeboden als en beschikbaar en zijn uitgesloten van de Service Level Agreement (SLA) en beperkte garantie. De functies zijn daarom niet bedoeld voor productie gebruik.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om een Azure Red Hat open Shift 4,3-cluster te maken:

- Azure CLI-versie 2.0.72 of hoger
  
- De ' AZ Aro-extensie

- Een virtueel netwerk met twee lege subnetten die elk zonder een netwerk beveiligings groep zijn gekoppeld.  Uw cluster wordt geïmplementeerd in deze subnetten.

- Een geclusterde AAD-toepassing (client-ID en geheim), Service-Principal of `az aro create` voldoende Aad-machtigingen voor om automatisch een Aad-toepassing en Service-Principal te maken.

- De services principal van de RP-service en de Cluster-service moeten elk de rol Inzender hebben op het virtuele cluster netwerk.  Als u de rol beheerder van gebruikers toegang in het virtuele netwerk hebt, `az aro create` worden de roltoewijzingen automatisch voor u ingesteld.

### <a name="install-the-az-aro-extension"></a>De ' AZ Aro-extensie installeren
Met `az aro` de extensie kunt u Azure Red Hat open Shift-clusters rechtstreeks maken, openen en verwijderen vanaf de opdracht regel met behulp van de Azure cli.

> [!Note] 
> De `az aro` uitbrei ding is actueel als preview. Dit kan in een toekomstige versie worden gewijzigd of verwijderd.
> Als u zich wilt aanmelden voor `az aro` de preview-versie, moet u `Microsoft.RedHatOpenShift` de resource provider registreren.
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. Aanmelden bij Azure.

   ```console
   az login
   ```

2. Voer de volgende opdracht uit om de `az aro` extensie te installeren:

   ```console
   az extension add -n aro --index https://az.aroapp.io/preview
   ```

3. Controleer of de ARO-extensie is geregistreerd.

   ```console
   az -v
   ...
   Extensions:
   aro                                0.3.0
   ...
   ```

### <a name="get-a-red-hat-pull-secret-optional"></a>Een pull-geheim voor Red Hat ophalen (optioneel)

Met een Red Hat pull-geheim kan uw cluster toegang krijgen tot Red Hat-container registers en aanvullende inhoud. Het gebruik van een pull-geheim is optioneel, maar wordt aanbevolen.

Uw pull-geheim ophalen:

1. Ga naar https://cloud.redhat.com/openshift/install/azure/aro-provisioned.
1. Meld u aan bij uw Red Hat-account of maak een nieuw Red Hat-account door gebruik te maken van uw zakelijke e-mail. Accepteer de voor waarden.
1. Selecteer **pull-geheim downloaden**.

Sla het bestand *pull-Secret. txt* op een veilige plek op. telkens wanneer u een cluster maakt, gebruikt u het bestand.

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
  --worker-subnet "$CLUSTER-worker" \
  --cluster-resource-group "aro-$CLUSTER" \
  --domain "$CLUSTER" \
  --pull-secret @pull-secret.txt
```

>[!NOTE]
> Het maken van een cluster duurt normaal gesp roken ongeveer 35 minuten.

## <a name="access-the-cluster-console"></a>Toegang tot de cluster console

U kunt de URL van de cluster console (van het `https://console-openshift-console.apps.<random>.<location>.aroapp.io/`formulier) vinden onder de Azure Red Hat open Shift 4,3-cluster bron. Voer de volgende opdracht uit om de resource weer te geven:

```console
az aro list -o table
```

U kunt zich aanmelden bij het cluster met `kubeadmin` behulp van de gebruiker.  Voer de volgende opdracht uit om het wacht woord voor `kubeadmin` de gebruiker te zoeken:

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
