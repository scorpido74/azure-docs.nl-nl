---
title: Containerinstantie met GPU-ingeschakeld implementeren
description: Meer informatie over het implementeren van Azure-containerexemplaren voor het uitvoeren van computerintensieve container-apps met GPU-resources.
ms.topic: article
ms.date: 02/19/2020
ms.openlocfilehash: 0f1d21c62be5d7ae099faa2c6fcc440829bb451f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525283"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>Containerexemplaren implementeren die GPU-bronnen gebruiken

Als u bepaalde computerintensieve workloads wilt uitvoeren op Azure Container Instances, implementeert u uw [containergroepen](container-instances-container-groups.md) met *GPU-resources.* De containerexemplaren in de groep hebben toegang tot een of meer NVIDIA Tesla GPU's tijdens het uitvoeren van containerworkloads zoals CUDA en deep learning-toepassingen.

In dit artikel ziet u hoe u GPU-resources toevoegt wanneer u een containergroep implementeert met behulp van een [YAML-bestand](container-instances-multi-container-yaml.md) of [Resource Manager-sjabloon](container-instances-multi-container-group.md). U ook GPU-resources opgeven wanneer u een containerinstantie implementeert met behulp van de Azure-portal.

> [!IMPORTANT]
> Deze functie is momenteel in preview en er zijn enkele [beperkingen van toepassing.](#preview-limitations) Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="preview-limitations"></a>Preview-beperkingen

In preview gelden de volgende beperkingen bij het gebruik van GPU-resources in containergroepen. 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

In de loop van de tijd wordt ondersteuning toegevoegd voor extra regio's.

**Ondersteunde besturingssysteemtypen**: alleen Linux

**Aanvullende beperkingen:** GPU-bronnen kunnen niet worden gebruikt bij het implementeren van een containergroep in een [virtueel netwerk.](container-instances-vnet.md)

## <a name="about-gpu-resources"></a>Informatie over GPU-bronnen

> [!IMPORTANT]
> GPU-bronnen zijn alleen beschikbaar op aanvraag. Als u toegang wilt vragen tot GPU-bronnen, dient u een [Azure-ondersteuningsaanvraag][azure-support]in.

### <a name="count-and-sku"></a>Telling en SKU

Als u GPU's in een containerinstantie wilt gebruiken, geeft u een *GPU-bron* op met de volgende informatie:

* **Aantal** - Het aantal GPU's: **1,** **2**of **4**.
* **SKU** - De GPU SKU: **K80,** **P100**of **V100**. Elke SKU brengt kaarten uit aan de NVIDIA Tesla GPU in een van de volgende VM-families met Azure GPU:Each SKU maps to the NVIDIA Tesla GPU in one the following Azure GPU-enabled VM families:

  | SKU | VM-familie |
  | --- | --- |
  | K80 K80 | [NC](../virtual-machines/nc-series.md) |
  | P100 | [NCv2 (NCv2)](../virtual-machines/ncv2-series.md) |
  | V100 | [NCv3 (NCv3)](../virtual-machines/ncv3-series.md) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

Stel bij het implementeren van GPU-resources CPU- en geheugenbronnen de juiste CPU- en geheugenbronnen in voor de werkbelasting, tot de maximumwaarden die in de vorige tabel worden weergegeven. Deze waarden zijn momenteel groter dan de CPU- en geheugenbronnen die beschikbaar zijn in containergroepen zonder GPU-resources.  

### <a name="things-to-know"></a>Dingen die u moet weten

* **Implementatietijd** - Het maken van een containergroep met GPU-resources duurt maximaal **8-10 minuten.** Dit is te wijten aan de extra tijd voor het inrichten en configureren van een GPU-vm in Azure. 

* **Prijzen** - Vergelijkbaar met containergroepen zonder GPU-resources, azure rekeningen voor resources verbruikt gedurende de *duur* van een containergroep met GPU-resources. De duur wordt berekend vanaf het moment dat de afbeelding van uw eerste container moet worden opgehaald totdat de containergroep is beëindigd. Het omvat niet de tijd om de containergroep te implementeren.

  Zie [prijsdetails](https://azure.microsoft.com/pricing/details/container-instances/).

* **CUDA-stuurprogramma's** - Containerexemplaren met GPU-resources zijn vooraf ingericht met NVIDIA CUDA-stuurprogramma's en containerruntimes, zodat u containerafbeeldingen gebruiken die zijn ontwikkeld voor CUDA-workloads.

  Wij steunen CUDA 9.0 in dit stadium. U bijvoorbeeld volgende basisafbeeldingen gebruiken voor uw Docker-bestand:
  * [nvidia/cuda:9.0-base-ubuntu16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [tensorflow/tensorflow: 1.12.0-gpu-py3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## <a name="yaml-example"></a>YAML voorbeeld

Een manier om GPU-resources toe te voegen, is door een containergroep te implementeren met behulp van een [YAML-bestand.](container-instances-multi-container-yaml.md) Kopieer de volgende YAML naar een nieuw bestand met de naam *gpu-deploy-aci.yaml*en sla het bestand op. Deze YAML maakt een containergroep met de naam *gpucontainergroep* die een containerinstantie met een K80 GPU opgeeft. In de instantie wordt een voorbeeld cuda-vectortoevoegingstoepassing uitgevoerd. De resourceaanvragen zijn voldoende om de werkbelasting uit te voeren.

```YAML
additional_properties: {}
apiVersion: '2018-10-01'
name: gpucontainergroup
properties:
  containers:
  - name: gpucontainer
    properties:
      image: k8s-gcrio.azureedge.net/cuda-vector-add:v0.1
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
          gpu:
            count: 1
            sku: K80
  osType: Linux
  restartPolicy: OnFailure
```

Implementeer de containergroep met de opdracht [AZ-container maken][az-container-create] en `--file` geef de YAML-bestandsnaam voor de parameter op. U moet de naam van een resourcegroep en een locatie voor de containergroep opgeven, zoals *eastus* die GPU-bronnen ondersteunt.  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

Het duurt enkele minuten om de implementatie te voltooien. Vervolgens start en voert de container een CUDA-vectortoevoeging uit. Voer de opdracht [az-containerlogboeken uit][az-container-logs] om de logboekuitvoer weer te geven:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergroup --container-name gpucontainer
```

Uitvoer:

```Console
[Vector addition of 50000 elements]
Copy input data from the host memory to the CUDA device
CUDA kernel launch with 196 blocks of 256 threads
Copy output data from the CUDA device to the host memory
Test PASSED
Done
```

## <a name="resource-manager-template-example"></a>Voorbeeld van resourcemanager-sjabloon

Een andere manier om een containergroep met GPU-bronnen te implementeren, is door een [resourcemanagersjabloon te gebruiken.](container-instances-multi-container-group.md) Begin met het `gpudeploy.json`maken van een bestand met de naam en kopieer vervolgens de volgende JSON erin. In dit voorbeeld wordt een containerinstantie geïmplementeerd met een V100-GPU waarmee een [TensorFlow-trainingstaak](https://www.tensorflow.org/) wordt uitgevoerd ten opzichte van de MNIST-gegevensset. De resourceaanvragen zijn voldoende om de werkbelasting uit te voeren.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "gpucontainergrouprm",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "variables": {
      "containername": "gpucontainer",
      "containerimage": "microsoft/samples-tf-mnist-demo:gpu"
    },
    "resources": [
      {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2018-10-01",
        "location": "[resourceGroup().location]",
        "properties": {
            "containers": [
            {
              "name": "[variables('containername')]",
              "properties": {
                "image": "[variables('containerimage')]",
                "resources": {
                  "requests": {
                    "cpu": 4.0,
                    "memoryInGb": 12.0,
                    "gpu": {
                        "count": 1,
                        "sku": "V100"
                  }
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "restartPolicy": "OnFailure"
        }
      }
    ]
}
```

Implementeer de sjabloon met de [opdracht Implementatie van az-groep.][az-group-deployment-create] U moet de naam opgeven van een resourcegroep die is gemaakt in een regio zoals *eastus* die GPU-bronnen ondersteunt.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file gpudeploy.json
```

Het duurt enkele minuten om de implementatie te voltooien. Vervolgens start de container en voert de tensorflow-taak uit. Voer de opdracht [az-containerlogboeken uit][az-container-logs] om de logboekuitvoer weer te geven:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergrouprm --container-name gpucontainer
```

Uitvoer:

```Console
2018-10-25 18:31:10.155010: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2018-10-25 18:31:10.305937: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: ccb6:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2018-10-25 18:31:10.305981: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: ccb6:00:00.0, compute capability: 3.7)
2018-10-25 18:31:14.941723: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
...
Accuracy at step 990: 0.969
Adding run metadata for 999
```

## <a name="clean-up-resources"></a>Resources opschonen

Omdat het gebruik van GPU-resources duur kan zijn, moet u ervoor zorgen dat uw containers niet voor langere tijd onverwacht worden uitgevoerd. Controleer uw containers in de Azure-portal of controleer de status van een containergroep met de opdracht [AZ-containershow.][az-container-show] Bijvoorbeeld:

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

Wanneer u klaar bent met het werken met de containerinstanties die u hebt gemaakt, verwijdert u deze met de volgende opdrachten:

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het implementeren van een containergroep met behulp van een [YAML-bestand](container-instances-multi-container-yaml.md) of [Resource Manager-sjabloon](container-instances-multi-container-group.md).
* Meer informatie over [GPU-geoptimaliseerde VM-formaten](../virtual-machines/linux/sizes-gpu.md) in Azure.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
