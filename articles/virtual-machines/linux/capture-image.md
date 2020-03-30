---
title: Een afbeelding van een Linux-VM vastleggen met Azure CLI
description: Maak een afbeelding van een Azure VM vast die u gebruiken voor massa-implementaties met behulp van de Azure CLI.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 10/08/2018
ms.author: cynthn
ms.openlocfilehash: 77f6244651551763f5460432655d66267775a256
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250398"
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Een installatiekopie maken van een virtuele machine of VHD

Als u meerdere kopieën van een virtuele machine (VM) wilt maken voor gebruik in Azure, legt u een afbeelding vast van de VM of van het OS VHD. Als u een afbeelding wilt maken voor implementatie, moet u persoonlijke accountgegevens verwijderen. In de volgende stappen deprovisioneert u een bestaande VM, deallocateert u deze en maakt u een afbeelding. U deze afbeelding gebruiken om VM's te maken in elke resourcegroep binnen uw abonnement.

Als u een kopie van uw bestaande Linux-VM wilt maken voor back-up of foutopsporing, of een gespecialiseerde Linux VHD wilt uploaden vanaf een on-premises VM, raadpleegt u [Uploaden en maken van een Linux VM op basis van aangepaste schijfafbeelding.](upload-vhd.md)  

U de Azure **VM Image Builder -service (Public Preview)** gebruiken om uw aangepaste afbeelding te maken, u hoeft geen hulpprogramma's te leren of pijplijnen voor het bouwen van een installatie in te stellen, door simpelweg een afbeeldingsconfiguratie te bieden en de Image Builder maakt de afbeelding. Zie [Aan de slag met Azure VM Image Builder](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview)voor meer informatie.

Je hebt de volgende items nodig voordat je een afbeelding maakt:

* Een Azure VM die is gemaakt in het implementatiemodel ResourceBeheer dat beheerde schijven gebruikt. Als u nog geen Linux-vm hebt gemaakt, u de [portal,](quick-create-portal.md)de [Azure CLI-](quick-create-cli.md)of [Resource Manager-sjablonen](create-ssh-secured-vm-from-template.md)gebruiken. Configureer de VM indien nodig. Voeg bijvoorbeeld [gegevensschijven toe,](add-disk.md)pas updates toe en installeer toepassingen. 

* De nieuwste [Azure CLI](/cli/azure/install-az-cli2) is geïnstalleerd en wordt aangemeld bij een Azure-account met [az-aanmelding](/cli/azure/reference-index#az-login).

## <a name="prefer-a-tutorial-instead"></a>Liever een tutorial in plaats daarvan?

Zie [Een aangepaste afbeelding van een Azure VM maken met de CLI](tutorial-custom-images.md)voor een vereenvoudigde versie van dit artikel en voor het testen, evalueren of leren over VM's in Azure.  Anders, blijf hier lezen om het volledige beeld te krijgen.


## <a name="step-1-deprovision-the-vm"></a>Stap 1: Deprovision erin de VM
Eerst deprovisioneert u de VM met behulp van de Azure VM-agent om machinespecifieke bestanden en gegevens te verwijderen. Gebruik `waagent` de opdracht `-deprovision+user` met de parameter op uw bron Linux VM. Zie de [Gebruikershandleiding voor Azure Linux Agent](../extensions/agent-linux.md) voor meer informatie.

1. Maak verbinding met uw Linux-VM met een SSH-client.
2. Voer in het SSH-venster de volgende opdracht in:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Voer deze opdracht alleen uit op een vm die u als afbeelding vastlegt. Deze opdracht garandeert niet dat het beeld wordt gewist van alle gevoelige informatie of geschikt is voor herverdeling. De `+user` parameter verwijdert ook het laatst ingerichte gebruikersaccount. Als u gebruikersaccountreferenties in de `-deprovision`virtuele machine wilt bewaren, gebruikt u alleen .
 
3. Voer **y** in om door te gaan. U `-force` de parameter toevoegen om deze bevestigingsstap te voorkomen.
4. Nadat de opdracht is voltooid, voert u **de uitgang** in om de SSH-client te sluiten.  De VM wordt nog steeds uitgevoerd op dit punt.

## <a name="step-2-create-vm-image"></a>Stap 2: VM-afbeelding maken
Gebruik de Azure CLI om de VM als gegeneraliseerd te markeren en de afbeelding vast te leggen. Vervang in de volgende voorbeelden voorbeeldparameternamen door uw eigen waarden. Voorbeelden van parameternamen zijn *myResourceGroup,* *myVnet*en *myVM*.

1. Deallocate de VM die u hebt gedeprovisioneerd met [az vm deallocate](/cli/azure/vm). In het volgende voorbeeld wordt de VM met de naam *myVM* toegewezen in de resourcegroep met de naam *myResourceGroup*.  
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```
    
    Wacht tot de VM volledig is verwijderd voordat u verder gaat. Dit kan enkele minuten duren.  De VM wordt afgesloten tijdens de toewijzing.

2. Markeer de VM als gegeneraliseerd met [az vm generaliseren](/cli/azure/vm). In het volgende voorbeeld wordt de VM met de naam *myVM* markeert in de resourcegroep met de naam *myResourceGroup* als algemeen.
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

    Een gegeneraliseerde VM kan niet meer opnieuw worden gestart.

3. Maak een afbeelding van de VM-bron met [az-afbeelding maken](/cli/azure/image#az-image-create). In het volgende voorbeeld wordt een afbeelding met de naam *myImage* gemaakt in de resourcegroep *myResourceGroup* met de VM-bron met de naam *myVM*.
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > De afbeelding wordt gemaakt in dezelfde brongroep als de bron-VM. U vm's maken in elke resourcegroep binnen uw abonnement vanuit deze afbeelding. Vanuit een managementperspectief u een specifieke resourcegroep maken voor uw VM-resources en -afbeeldingen.
   >
   > Als u uw afbeelding wilt opslaan in zonebestendige opslag, moet u deze maken in `--zone-resilient true` een regio die [beschikbaarheidszones](../../availability-zones/az-overview.md) ondersteunt en de parameter opneemt.
   
Met deze opdracht retourneert JSON die de VM-afbeelding beschrijft. Sla deze uitvoer op voor latere referentie.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Stap 3: Een vm maken op basis van de vastgelegde afbeelding
Maak een VM met behulp van de afbeelding die u hebt gemaakt met [az vm maken](/cli/azure/vm). In het volgende voorbeeld wordt een VM met de naam *myVMDeployed gemaakt* op basis van de afbeelding met de naam *myImage*.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>De VM maken in een andere resourcegroep 

U VM's maken op basis van een afbeelding in een resourcegroep binnen uw abonnement. Als u een vm wilt maken in een andere resourcegroep dan de afbeelding, geeft u de volledige resource-id op voor uw afbeelding. Gebruik [de az-afbeeldingslijst](/cli/azure/image#az-image-list) om een lijst met afbeeldingen weer te geven. De uitvoer lijkt op die in het volgende voorbeeld.

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

In het volgende voorbeeld wordt [az vm gemaakt](/cli/azure/vm#az-vm-create) om een VM te maken in een andere resourcegroep dan de bronafbeelding, door de image resource ID op te geven.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Stap 4: De implementatie verifiëren

SSH in de virtuele machine die u hebt gemaakt om de implementatie te verifiëren en de nieuwe VM te gebruiken. Om verbinding te maken via SSH, vind je het IP-adres of FQDN van je VM met [az vm show.](/cli/azure/vm#az-vm-show)

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Volgende stappen
U meerdere VM's maken op de vm-afbeelding van uw bron. Ga als instellen voor wijzigingen in uw afbeelding: 

- Maak een VM op basis van uw afbeelding.
- Breng updates of configuratiewijzigingen aan.
- Volg de stappen opnieuw om een afbeelding te deprovisioneren, detoewijzen, generaliseren en maken.
- Gebruik deze nieuwe afbeelding voor toekomstige implementaties. U de oorspronkelijke afbeelding verwijderen.

Zie [Azure CLI](/cli/azure)voor meer informatie over het beheren van uw VM's met de CLI.
