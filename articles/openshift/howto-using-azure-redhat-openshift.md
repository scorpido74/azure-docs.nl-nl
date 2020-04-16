---
title: Een Azure Red Hat OpenShift 4.3-cluster maken | Microsoft Documenten
description: Een cluster maken met Azure Red Hat OpenShift 4.3
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: aro, openshift, az aro, rode hoed, cli
ms.openlocfilehash: f909c5870be6e394e457ad8f44ea5a253054ffe6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81398897"
---
# <a name="create-access-and-manage-an-azure-red-hat-openshift-43-cluster"></a>Een Azure Red Hat OpenShift 4.3-cluster maken, openen en beheren

> [!IMPORTANT]
> Houd er rekening mee dat Azure Red Hat OpenShift 4.3 momenteel alleen beschikbaar is in een privépreview in Oost-VS en Oost-VS 2. Private preview-acceptatie is alleen op uitnodiging. Zorg ervoor dat u uw abonnement registreert voordat u deze functie probeert in te schakelen: [Azure Red Hat OpenShift Private Preview-registratie](https://aka.ms/aro-preview-register)

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
   aro                                0.3.0
   ...
   ```

### <a name="get-a-red-hat-pull-secret-optional"></a>Krijg een Red Hat pull geheim (optioneel)

Een Red Hat pull geheim stelt uw cluster in staat om toegang te krijgen tot Red Hat container registers en extra inhoud. Het gebruik van een pull secret is optioneel, maar aanbevolen.

Om je pull geheim te krijgen:

1. Ga naar https://cloud.redhat.com/openshift/install/azure/aro-provisioned.
1. Meld u aan bij uw Red Hat-account of maak een nieuw Red Hat-account met behulp van uw zakelijke e-mail; de algemene voorwaarden te accepteren.
1. Selecteer **Pull-geheim downloaden**.

Sla het *pull-secret.txt-bestand* ergens veilig op; u gebruikt het bestand telkens wanneer u een cluster maakt.

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Een virtueel netwerk maken met twee lege subnetten

Volg deze stappen om een virtueel netwerk te maken met twee lege subnetten.

1. Stel de volgende variabelen in.

   ```console
   LOCATION=eastus        #the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    #the name of the resource group where you want to create your cluster
   CLUSTER=cluster        #the name of your cluster
   ```

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
  --cluster-resource-group "aro-$CLUSTER" \
  --domain "$CLUSTER" \
  --pull-secret @pull-secret.txt
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
