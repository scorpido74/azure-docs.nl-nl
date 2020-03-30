---
title: Een Azure Red Hat OpenShift 4.3-cluster maken | Microsoft Documenten
description: Een cluster maken met Azure Red Hat OpenShift 4.3
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: aro, openshift, az aro, rode hoed, cli
ms.openlocfilehash: 423f09c135da51b8401c1933a4a271d0becd2c8f
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349439"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>Een Azure Red Hat OpenShift 4.3-cluster maken, openen en beheren

> [!IMPORTANT]
> Houd er rekening mee dat Azure Red Hat OpenShift 4.3 momenteel alleen beschikbaar is in een privépreview in Oost-VS. Private preview-acceptatie is alleen op uitnodiging. Zorg ervoor dat u uw abonnement registreert voordat u deze functie probeert in te schakelen: [Azure Red Hat OpenShift Private Preview-registratie](https://aka.ms/aro-preview-register)

> [!NOTE]
> Preview-functies zijn selfservice en worden geleverd zoals het is en zoals beschikbaar en zijn uitgesloten van de service-level agreement (SLA) en beperkte garantie. Daarom zijn de functies niet bedoeld voor productiegebruik.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om een Azure Red Hat OpenShift 4.3-cluster te maken:

- Azure CLI-versie 2.0.72 of hoger
  
- De 'az aro' uitbreiding

- Een virtueel netwerk met twee lege subnetten, elk zonder netwerkbeveiligingsgroep.  Uw cluster wordt geïmplementeerd in deze subnetten.

- Een cluster AAD-toepassing (client-ID en geheim) en `az aro create` serviceprincipal, of voldoende AAD-machtigingen voor het automatisch maken van een AAD-toepassing en serviceprincipal voor u.

- De hoofdsom van de RP-service en de clusterserviceprincipal moeten elk de rol van bijdrager in het virtuele clusternetwerk hebben.  Als u de rol 'Gebruikerstoegangsbeheerder' in `az aro create` het virtuele netwerk hebt, worden de roltoewijzingen automatisch voor u ingesteld.

### <a name="install-the-az-aro-extension"></a>Installeer de 'az aro' extensie
Met `az aro` de extensie u Azure Red Hat OpenShift-clusters rechtstreeks vanuit de opdrachtregel maken, openen en verwijderen met behulp van de Azure CLI.

> [!Note] 
> De `az aro` extensie is actueel in preview. Het kan worden gewijzigd of verwijderd in een toekomstige release.
> Als u zich wilt `az aro` aanmelden voor het `Microsoft.RedHatOpenShift` voorbeeld van de extensie, moet u de resourceprovider registreren.
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. Aanmelden bij Azure.

   ```console
   az login
   ```

2. Voer de volgende opdracht `az aro` uit om de extensie te installeren:

   ```console
   az extension add -n aro --index https://az.aroapp.io/preview
   ```

3. Controleer of de ARO-extensie is geregistreerd.

   ```console
   az -v
   ...
   Extensions:
   aro                                0.1.0
   ...
   ```
  
### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Een virtueel netwerk maken met twee lege subnetten

Volg deze stappen om een virtueel netwerk te maken met twee lege subnetten.

1. Stel de volgende variabelen in.

   ```console
   LOCATION=eastus        #the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    #the name of the resource group where you want to create your cluster
   CLUSTER=cluster        #the name of your cluster
   PULL_SECRET="<optional-pull-secret>"
   ```
   >[!NOTE]
   > Met het optionele pull-geheim heeft uw cluster toegang tot red hat-containerregisters, samen met extra inhoud.
   >
   > Krijg toegang tot je https://cloud.redhat.com/openshift/install/azure/installer-provisioned pull-geheim door naar en op *Copy Pull Secret*te klikken.
   >
   > Je moet inloggen op je Red Hat-account, of een nieuw Red Hat-account aanmaken met je zakelijke e-mail en de algemene voorwaarden accepteren.
 

2. Maak een resourcegroep voor uw cluster.

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

4. Voeg twee lege subnetten toe aan uw virtuele netwerk.

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

5. Netwerkbeleid voor Private Link Service uitschakelen op uw virtuele netwerk en subnetten. Dit is een vereiste voor de ARO-service om toegang te krijgen tot en het cluster te beheren.

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
  --pull-secret "$PULL_SECRET"
```

>[!NOTE]
> Het duurt normaal gesproken ongeveer 35 minuten om een cluster te maken.

## <a name="access-the-cluster-console"></a>Toegang tot de clusterconsole

U de URL van de `https://console-openshift-console.apps.<random>.<location>.aroapp.io/`clusterconsole (van het formulier) vinden onder de Azure Red Hat OpenShift 4.3-clusterbron. Voer de volgende opdracht uit om de resource weer te geven:

```console
az aro list -o table
```

U zich aanmelden `kubeadmin` bij het cluster met behulp van de gebruiker.  Voer de volgende opdracht uit `kubeadmin` om het wachtwoord voor de gebruiker te vinden:

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
