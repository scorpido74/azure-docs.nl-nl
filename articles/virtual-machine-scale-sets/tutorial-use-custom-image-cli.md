---
title: Zelf studie-een aangepaste VM-installatie kopie gebruiken in een schaalset met Azure CLI
description: Informatie over het gebruik van Azure CLI voor het maken van een aangepaste VM-installatiekopie die u kunt gebruiken om een virtuele-machineschaalset te implementeren
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc
ms.reviewer: akjosh
ms.openlocfilehash: 22f3fd44fbeb3d951d4add7b90a0e9aebd863ebf
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792833"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>Zelfstudie: Een aangepaste installatiekopie voor virtuele-machineschaalsets maken en gebruiken met Azure CLI
Wanneer u een schaalset maakt, geeft u een installatiekopie op die moet worden gebruikt wanneer de VM-exemplaren zijn geïmplementeerd. Om het aantal taken na de implementatie van VM-exemplaren te verminderen, kunt u een aangepaste VM-installatiekopie gebruiken. Deze aangepaste VM-installatiekopie bevat alle geïnstalleerde toepassingen of configuraties die vereist zijn. Alle VM-exemplaren die in de schaalset zijn gemaakt, gebruiken de aangepaste VM-installatiekopie en zijn gereed voor uw toepassingsverkeer. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een galerie met gedeelde afbeeldingen maken
> * Een gespecialiseerde afbeeldings definitie maken
> * Een versie van een installatie kopie maken
> * Een schaalset maken op basis van een gespecialiseerde afbeelding
> * Een galerie met installatie kopieën delen


Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze zelf studie gebruikmaken van de Azure CLI-versie 2.4.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli).

## <a name="overview"></a>Overzicht

Een [Galerie met gedeelde afbeeldingen](shared-image-galleries.md) vereenvoudigt het delen van aangepaste afbeeldingen in uw organisatie. Aangepaste installatiekopieën zijn soortgelijk aan Marketplace-installatiekopieën, maar u kunt deze zelf maken. Aangepaste installatiekopieën kunnen worden gebruikt voor het opstarten van configuraties, zoals het vooraf laden van toepassingen, toepassingsconfiguraties en andere besturingssysteemconfiguraties. 

Met de galerie gedeelde afbeeldingen kunt u uw aangepaste VM-installatie kopieën delen met anderen. Kies welke installatie kopieën u wilt delen, in welke regio's u ze beschikbaar wilt maken en met wie u wilt delen. 

## <a name="create-and-configure-a-source-vm"></a>Een bron-VM maken en configureren

Voordat u een virtuele machine kunt maken, moet u eerst een resourcegroep maken met [az group create](/cli/azure/group) en daarna een virtuele machine maken met [az vm create](/cli/azure/vm). Deze VM wordt vervolgens gebruikt als de bron voor de installatie kopie. In het volgende voorbeeld wordt een virtuele machine gemaakt met de naam *myVM* in de resourcegroep met de naam *myResourceGroup*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

> [!IMPORTANT]
> De **id** van uw virtuele machine wordt weer gegeven in de uitvoer van de opdracht [AZ VM Create](/cli/azure/vm) . Kopieer deze ergens veilig, zodat u deze later in deze zelf studie kunt gebruiken.

Het open bare IP-adres van uw virtuele machine wordt ook weer gegeven in de uitvoer van de opdracht [AZ VM Create](/cli/azure/vm) . SSH in het openbare IP-adres van uw VM als volgt:

```console
ssh azureuser@<publicIpAddress>
```

We gaan een eenvoudige webserver voor het aanpassen van uw VM installeren. Bij implementatie van het VM-exemplaar in de schaalset heeft dit hiermee alle vereiste pakketten om een webtoepassing uit te voeren. Gebruik `apt-get` om *NGINX* als volgt te installeren:

```bash
sudo apt-get install -y nginx
```

Wanneer u klaar bent, typt `exit` u om de SSH-verbinding te verbreken.

## <a name="create-an-image-gallery"></a>Een galerie met installatie kopieën maken 

Een galerie met installatie kopieën is de primaire resource die wordt gebruikt voor het inschakelen van het delen van afbeeldingen. 

Toegestane tekens voor de naam van de galerie bestaan uit hoofd letters of kleine letters, cijfers, punten en punten. De naam van de galerie mag geen streepjes bevatten.   Galerie namen moeten uniek zijn binnen uw abonnement. 

Maak een galerie met installatie kopieën met [AZ sig Create](/cli/azure/sig#az-sig-create). In het volgende voor beeld wordt een resource groep met de naam Gallery gemaakt met de naam *myGalleryRG* in *VS-Oost*en een galerie met de naam *myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Een definitie van een installatie kopie maken

Afbeeldings definities maken een logische groepering voor installatie kopieën. Ze worden gebruikt voor het beheren van informatie over de installatie kopieën die in deze versies worden gemaakt. 

Definitie namen van afbeeldingen kunnen bestaan uit hoofd letters, kleine letters, cijfers, punten, streepjes en punten. 

Zorg ervoor dat de definitie van de afbeelding het juiste type is. Als u de VM hebt gegeneraliseerd (met behulp van Sysprep voor Windows of waagent-deprovisioning voor Linux), moet u een `--os-state generalized`algemene definitie voor de installatie kopie maken met behulp van. Als u de virtuele machine wilt gebruiken zonder bestaande gebruikers accounts te verwijderen, maakt u een gespecialiseerde definitie `--os-state specialized`van de installatie kopie met behulp van.

Zie [afbeeldings definities](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions)voor meer informatie over de waarden die u kunt opgeven voor de definitie van een installatie kopie.

Maak een definitie van een installatie kopie in de galerie met behulp van [AZ sig image-definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

In dit voor beeld heeft de definitie van de installatie kopie de naam *myImageDefinition*en is voor een [gespecialiseerde](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images) Linux-installatie kopie van het besturings systeem. Als u een definitie voor installatie kopieën wilt maken met een Windows `--os-type Windows`-besturings systeem, gebruikt u. 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```

> [!IMPORTANT]
> De **id** van de definitie van de installatie kopie wordt weer gegeven in de uitvoer van de opdracht. Kopieer deze ergens veilig, zodat u deze later in deze zelf studie kunt gebruiken.


## <a name="create-the-image-version"></a>De versie van de installatie kopie maken

Maak een installatie kopie versie van de virtuele machine met behulp van [AZ Image Gallery Create-Image galerie-version](/cli/azure/sig/image-version#az-sig-image-version-create).  

Toegestane tekens voor de versie van de installatie kopie zijn getallen en punten. Getallen moeten binnen het bereik van een 32-bits geheel getal zijn. Indeling: *MajorVersion*. *MinorVersion*. *Patch*.

In dit voor beeld is de versie van de afbeelding *1.0.0* en we gaan 1 replica maken in de regio *Zuid-Centraal VS* en 1 replica in de regio *VS Oost 2* . De replicatie regio's moeten de regio bevatten waarin de bron-VM zich bevindt.

Vervang de waarde van `--managed-image` in dit voor beeld door de id van uw virtuele machine uit de vorige stap.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1" \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> U moet wachten tot de versie van de installatie kopie volledig is gebouwd en gerepliceerd voordat u dezelfde beheerde installatie kopie kunt gebruiken om een andere versie van de installatie kopie te maken.
>
> U kunt uw installatie kopie ook opslaan in Premium Storage met een `--storage-account-type  premium_lrs`toevoeging of [zone-redundante opslag](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) door toe te voegen `--storage-account-type  standard_zrs` wanneer u de versie van de installatie kopie maakt.
>




## <a name="create-a-scale-set-from-the-image"></a>Een schaalset maken op basis van de afbeelding
Een schaalset maken op basis van de gespecialiseerde [`az vmss create`](/cli/azure/vmss#az-vmss-create)afbeelding met behulp van. 

Maak de schaalset met [`az vmss create`](/cli/azure/vmss#az-vmss-create) behulp van de--gespecialiseerde para meter om aan te geven dat de afbeelding een gespecialiseerde afbeelding is. 

Gebruik de definitie-ID van `--image` de installatie kopie om de instanties van de schaalset te maken op basis van de meest recente versie van de installatie kopie die beschikbaar is. U kunt ook instanties van een schaalset maken op basis van een specifieke versie door de versie-ID `--image`van de installatie kopie op te geven voor. 

Maak een schaalset met de naam *myScaleSet* de nieuwste versie van de *myImageDefinition* -installatie kopie die u eerder hebt gemaakt.

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --specialized
```

Het duurt enkele minuten om alle schaalsetresources en VM's te maken en te configureren.


## <a name="test-your-scale-set"></a>Uw schaalset testen
Als u wilt dat verkeer de webserver kan bereiken, maakt u een load balancer-regel met behulp van [az network lb rule create](/cli/azure/network/lb/rule). In het volgende voorbeeld wordt een regel met de naam *myLoadBalancerRuleWeb* gemaakt die verkeer toestaat op *TCP*-poort *80*:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

Als u de schaalset in actie wilt zien, achterhaalt u het openbare IP-adres van uw load balancer met [az network public-ip show](/cli/azure/network/public-ip). In het volgende voorbeeld wordt het IP-adres voor *myScaleSetLBPublicIP* opgehaald, dat is gemaakt als onderdeel van de schaalset:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Voer in uw webbrowser het openbare IP-adres in. De standaard NGINX-webpagina wordt weergegeven, zoals in het volgende voorbeeld:

![NGINX uitgevoerd vanuit een aangepaste VM-installatiekopie](media/tutorial-use-custom-image-cli/default-nginx-website.png)



## <a name="share-the-gallery"></a>De galerie delen

U kunt afbeeldingen in abonnementen delen met behulp van op rollen gebaseerde Access Control (RBAC). U kunt afbeeldingen delen met de galerie, de afbeeldings definitie of de versie van de installatie kopie. Elke gebruiker met lees machtigingen voor een installatie kopie versie, zelfs via abonnementen, kan een virtuele machine implementeren met de versie van de installatie kopie.

We raden u aan om te delen met andere gebruikers op het niveau van de galerie. Gebruik [AZ sig show](/cli/azure/sig#az-sig-show)om de object-id van uw galerie op te halen.

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Gebruik de object-ID als bereik, samen met een e-mail adres en [AZ-roltoewijzing maken](/cli/azure/role/assignment#az-role-assignment-create) om een gebruiker toegang te geven tot de galerie met gedeelde afbeeldingen. Vervang `<email-address>` en `<gallery iD>` door uw eigen gegevens.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Zie [toegang beheren met RBAC en Azure cli](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)voor meer informatie over het delen van resources met RBAC.


## <a name="clean-up-resources"></a>Resources opschonen
Als u de schaalset en aanvullende resources wilt verwijderen, verwijdert u de resource groep en alle bijbehorende resources met [AZ Group delete](/cli/azure/group). De parameter `--no-wait` retourneert het besturingselement naar de prompt zonder te wachten totdat de bewerking is voltooid. De parameter `--yes` bevestigt dat u de resources wilt verwijderen, zonder een extra prompt om dit te doen.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd hoe u een aangepaste VM-installatiekopie maakt en gebruikt voor uw schaalsets met Azure CLI:

> [!div class="checklist"]
> * Een galerie met gedeelde afbeeldingen maken
> * Een gespecialiseerde afbeeldings definitie maken
> * Een versie van een installatie kopie maken
> * Een schaalset maken op basis van een gespecialiseerde afbeelding
> * Een galerie met installatie kopieën delen

Ga door naar de volgende zelfstudie voor informatie over het implementeren van toepassingen naar uw schaalset.

> [!div class="nextstepaction"]
> [Toepassingen in uw schaalsets implementeren](tutorial-install-apps-cli.md)
