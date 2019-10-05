---
title: Diep gaande leren & AI-Frameworks
titleSuffix: Azure Data Science Virtual Machine
description: Beschik bare uitgebreide lessen en hulpprogram ma's voor Azure Data Science Virtual Machine.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: conceptual
ms.date: 10/1/2019
ms.openlocfilehash: db95788b0f2c041157bdc16000d0328c042e86d5
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973690"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Diep gaande lessen en AI-frameworks voor Azure Data Science VM
Uitgebreide lessen op het DSVM worden hieronder weer gegeven.

## <a name="caffehttpsgithubcombvlccaffe"></a>[Caffe](https://github.com/BVLC/caffe)

|    |           |
| ------------- | ------------- |
| Ondersteunde versies | |
| Ondersteunde DSVM-edities      | Linux (Ubuntu)     |
| Hoe wordt deze geconfigureerd / geïnstalleerd op de DSVM?  | Caffe is geïnstalleerd in `/opt/caffe`.   Voor beelden zijn `/opt/caffe/examples`.|
| Het uitvoeren van het programma      | gebruik X2Go om u aan te melden bij uw virtuele machine, start een nieuwe terminal en voer het volgende in:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Er wordt een nieuw browservenster geopend met voorbeeldnotitieblokken. Binaire bestanden zijn in /opt/caffe/build/install/bin geïnstalleerd.<br/><br/>De geïnstalleerde versie van Caffe vereist python 2,7 en werkt niet met python 3,5, die standaard wordt geactiveerd. Als u wilt overschakelen naar python 2,7, voert u `source activate root` uit om over te scha kelen naar de Anaconda-omgeving.|    

## <a name="caffe2httpsgithubcomcaffe2caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

|    |           |
| ------------- | ------------- |
| Ondersteunde versies | |
| Ondersteunde DSVM-edities      | Linux (Ubuntu)     |
| Hoe wordt deze geconfigureerd / geïnstalleerd op de DSVM?  | Caffe2 wordt in de Conda-omgeving [python 2,7 (root) geïnstalleerd. |
| Het uitvoeren van het programma      | Terminal Start python en importeer Caffe2. <br/> * JupyterHub: [Maak verbinding met JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)en ga vervolgens naar de map Caffe2 om voorbeeld notitieblokken te vinden. Sommige laptops vereisen de hoofdmap Caffe2 moet worden ingesteld in de Python-code; Voer /opt/caffe2. |

## <a name="chainerhttpschainerorg"></a>[Chainer](https://chainer.org/)

|    |           |
| ------------- | ------------- |
| Ondersteunde versies | 5.2 |
| Ondersteunde DSVM-edities      | Linux (Ubuntu)     |
| Hoe wordt deze geconfigureerd / geïnstalleerd op de DSVM?  | Chainer is geïnstalleerd in Python 3,5. |
| Het uitvoeren van het programma      | Terminal Activeer de python 3,5-omgeving, voer `python` uit en klik vervolgens `import chainer`. <br/> * JupyterHub: [Maak verbinding met JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)en ga vervolgens naar de map Chainer om voorbeeld notitieblokken te vinden.| 

## <a name="cuda-cudnn-nvidia-driverhttpsdevelopernvidiacomcuda-toolkit"></a>[CUDA, cuDNN, NVIDIA-stuur programma](https://developer.nvidia.com/cuda-toolkit)

|    |           |
| ------------- | ------------- |
| Ondersteunde versies | 10.0.130|
| Ondersteunde DSVM-edities      | Windows en Linux   |
| Hoe wordt deze geconfigureerd / geïnstalleerd op de DSVM?  |_NVIDIA-smi_ is beschikbaar in het systeempad.  |
| Het uitvoeren van het programma      | Open een opdracht prompt (in Windows) of een Terminal (op Linux) en voer _NVIDIA-SMI_uit. |


## <a name="horovodhttpsgithubcomuberhorovod"></a>[Horovod](https://github.com/uber/horovod)

|    |           |
| ------------- | ------------- |
| Ondersteunde versies | 0.16.1|
| Ondersteunde DSVM-edities      | Linux (Ubuntu)   |
| Hoe wordt deze geconfigureerd / geïnstalleerd op de DSVM?  | Horovod is geïnstalleerd in Python 3,5 |
| Het uitvoeren van het programma      | Activeer de juiste omgeving bij de Terminal en voer python uit. |

## <a name="kerashttpskerasio"></a>[Keras](https://keras.io/)

|    |           |
| ------------- | ------------- |
| Ondersteunde versies | 2.2.4 |
| Ondersteunde DSVM-edities      | Windows en Linux   |
| Hoe wordt deze geconfigureerd / geïnstalleerd op de DSVM?  | Keras is geïnstalleerd in Python 3,6 in Windows en in Python 3,5 in Linux |
| Het uitvoeren van het programma      | Activeer de juiste omgeving bij de Terminal en voer python uit. |

## <a name="microsoft-cognitive-toolkit-cntkhttpsdocsmicrosoftcomcognitive-toolkit"></a>[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)

|    |           |
| ------------- | ------------- |
| Ondersteunde versies | 2.5.1 |
| Ondersteunde DSVM-edities      | Windows en Linux   |
| Hoe wordt deze geconfigureerd / geïnstalleerd op de DSVM?  | CNTK is geïnstalleerd in Python 3,6 op [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) en in Python 3,5 op [Linux](./dsvm-languages.md#python-linux-edition)) |
| Het uitvoeren van het programma      | Terminal Activeer de juiste omgeving en voer python uit. <br/>Jupyter Maak verbinding met [Jupyter](provision-vm.md) of [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)en open vervolgens de map CNTK voor voor beelden. |

## <a name="mxnethttpsmxnetapacheorg"></a>[MXNet](https://mxnet.apache.org/)
|    |           |
| ------------- | ------------- |
| Ondersteunde versies | 1.3.0 |
| Ondersteunde DSVM-edities      | Windows en Linux   |
| Hoe wordt deze geconfigureerd / geïnstalleerd op de DSVM?  | MXNet is geïnstalleerd in `C:\dsvm\tools\mxnet` in Windows en `/dsvm/tools/mxnet` op Ubuntu. Python-bindingen worden geïnstalleerd in Python 3,6 op [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) en in Python 3,5 op [Linux](./dsvm-languages.md#python-linux-edition)) R-bindingen zijn ook opgenomen in de Ubuntu-DSVM. |
| Het uitvoeren van het programma      | Terminal Activeer de juiste Conda-omgeving en voer vervolgens `import mxnet` uit. <br/>Jupyter Maak verbinding met [Jupyter](provision-vm.md#access-the-dsvm) of [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)en open vervolgens de `mxnet`-map voor voor beelden. |

## <a name="mxnet-model-serverhttpsgithubcomawslabsmxnet-model-serverquick-start"></a>[MXNet-model server](https://github.com/awslabs/mxnet-model-server#quick-start)

|    |           |
| ------------- | ------------- |
| Ondersteunde versies | 1.0.1 |
| Ondersteunde DSVM-edities      | Windows en Linux   |
| Hoe wordt deze geconfigureerd / geïnstalleerd op de DSVM?  | MXNet model server is geïnstalleerd in Python 3,6 op [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) en in Python 3,5 op [Linux](./dsvm-languages.md#python-linux-edition)) |
| Het uitvoeren van het programma      | Terminal Voer `sudo systemctl stop jupyterhub` uit om de JupyterHub-service eerst te stoppen, omdat beide hebben geluisterd op dezelfde poort. Activeer vervolgens de juiste Conda-omgeving en voer `mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` uit |

## <a name="nvidia-system-management-interface-nvidia-smihttpsdevelopernvidiacomnvidia-system-management-interface"></a>[NVidia-systeem beheer interface (NVIDIA-SMI)](https://developer.nvidia.com/nvidia-system-management-interface)

|    |           |
| ------------- | ------------- |
| Ondersteunde versies |  |
| Ondersteunde DSVM-edities      | Windows en Linux   |
| Wat is het voor? | NVIDIA-hulpprogramma voor het uitvoeren van query's GPU-activiteit |
| Hoe wordt deze geconfigureerd / geïnstalleerd op de DSVM?  | `nvidia-smi` bevindt zich in het systeempad. |
| Het uitvoeren van het programma      | Open een opdracht prompt (in Windows) of een Terminal (op Linux) op een virtuele machine **met GPU**en voer `nvidia-smi` uit. |

## <a name="pytorchhttpspytorchorg"></a>[PyTorch](https://pytorch.org/)

|    |           |
| ------------- | ------------- |
| Ondersteunde versies | 1.2.0 |
| Ondersteunde DSVM-edities      | Linux |
| Hoe wordt deze geconfigureerd / geïnstalleerd op de DSVM?  | Geïnstalleerd in [Python 3,5](dsvm-languages.md#python-linux-edition). Voor beelden van Jupyter-notebooks zijn opgenomen. |
| Het uitvoeren van het programma      | Terminal Activeer de juiste omgeving en voer python uit.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): Maak verbinding en open de PyTorch-map voor voor beelden.  |

## <a name="tensorflowhttpswwwtensorfloworg"></a>[TensorFlow](https://www.tensorflow.org/)

|    |           |
| ------------- | ------------- |
| Ondersteunde versies | 1,13 |
| Ondersteunde DSVM-edities      | Windows, Linux |
| Hoe wordt deze geconfigureerd / geïnstalleerd op de DSVM?  | Geïnstalleerd in Python 3,5 onder [Linux](dsvm-languages.md#python-linux-edition) en python 3,6 op [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition) |
| Het uitvoeren van het programma      | Terminal Activeer de juiste omgeving en voer python uit. <br/> Jupyter Maak verbinding met [Jupyter](provision-vm.md) of [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)en open vervolgens de map tensor flow voor voor beelden.   |

## <a name="tensorflow-servinghttpswwwtensorfloworgserving"></a>[Tensor flow voor](https://www.tensorflow.org/serving/)

|    |           |
| ------------- | ------------- |
| Ondersteunde versies | 1,12 |
| Ondersteunde DSVM-edities      | Linux |
| Hoe wordt deze geconfigureerd / geïnstalleerd op de DSVM?  | tensorflow_model_server is beschikbaar op de Terminal. |
| Het uitvoeren van het programma      |  Voorbeelden zijn beschikbaar [online](https://www.tensorflow.org/serving/).   |


## <a name="theanohttpsgithubcomtheanotheano"></a>[Theano](https://github.com/Theano/Theano)

|    |           |
| ------------- | ------------- |
| Ondersteunde versies | 1.0.3 |
| Ondersteunde DSVM-edities      | Linux |
| Hoe wordt deze geconfigureerd / geïnstalleerd op de DSVM?  |Theano is geïnstalleerd in Python 2,7 (_root_) en in de_py35_-omgeving (python 3,5). |
| Het uitvoeren van het programma      |  Terminal Activeer de python-versie die u wilt (root of py35), voer python uit en importeer vervolgens Theano.<br/>Jupyter Selecteer de python 2,7-of 3,5-kernel en importeer vervolgens Theano.  <br/>Als u een recente MKL-fout (math kernel Library) wilt omzeilen, moet u eerst de MKL-threading Layer als volgt instellen:<br/><br/>`export MKL_THREADING_LAYER=GNU`  |