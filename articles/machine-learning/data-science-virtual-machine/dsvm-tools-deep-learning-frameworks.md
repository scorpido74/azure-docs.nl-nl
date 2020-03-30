---
title: Deep Learning & AI-frameworks
titleSuffix: Azure Data Science Virtual Machine
description: Beschikbare deep learning frameworks en tools op Azure Data Science Virtual Machine.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d8a5cf428f41b130e6faf68ac87a075c15211099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270067"
---
# <a name="deep-learning-and-ai-frameworks-for-the-azure-data-science-vm"></a>Deep learning- en AI-frameworks voor de Azure Data Science VM
Deep learning frameworks op de DSVM staan hieronder vermeld.

## <a name="caffe"></a>[Caffe](https://github.com/BVLC/caffe)

|    |           |
| ------------- | ------------- |
| Ondersteunde versie(s) | |
| Ondersteunde DSVM-edities      | Linux (Ubuntu)     |
| Hoe is het geconfigureerd / geïnstalleerd op de DSVM?  | Caffe is `/opt/caffe`geïnstalleerd in .   Monsters zijn `/opt/caffe/examples`in .|
| Hoe het uit te voeren      | gebruik X2Go om u aan te melden bij uw vm en start vervolgens een nieuwe terminal en voer het volgende in:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Er wordt een nieuw browservenster geopend met voorbeeldnotitieblokken. Binaries zijn geïnstalleerd in / opt / caffe / bouwen / installeren / bin.<br/><br/>De geïnstalleerde versie van Caffe vereist Python 2.7 en werkt niet met Python 3.5, die standaard wordt geactiveerd. Als u wilt overschakelen `source activate root` naar Python 2.7, schakelt u over naar de Anaconda-omgeving.|    

## <a name="caffe2"></a>[Caffe2](https://github.com/caffe2/caffe2)

|    |           |
| ------------- | ------------- |
| Ondersteunde versie(s) | |
| Ondersteunde DSVM-edities      | Linux (Ubuntu)     |
| Hoe is het geconfigureerd / geïnstalleerd op de DSVM?  | Caffe2 is geïnstalleerd in de [Python 2.7 (root) conda omgeving. |
| Hoe het uit te voeren      | Terminal: Start Python en importeer Caffe2. <br/> * JupyterHub: [Maak verbinding met JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)en ga vervolgens naar de Caffe2-map om voorbeeldnotitieblokken te vinden. Voor sommige notitieblokken moet de Caffe2-root worden ingesteld in de Python-code; enter /opt/caffe2. |

## <a name="chainer"></a>[Chainer](https://chainer.org/)

|    |           |
| ------------- | ------------- |
| Ondersteunde versie(s) | 5.2 |
| Ondersteunde DSVM-edities      | Linux (Ubuntu)     |
| Hoe is het geconfigureerd / geïnstalleerd op de DSVM?  | Chainer is geïnstalleerd in Python 3.5. |
| Hoe het uit te voeren      | Terminal: Activeer de Python 3.5-omgeving, voer uit `python`en vervolgens `import chainer`. <br/> * JupyterHub: [Maak verbinding met JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)en ga vervolgens naar de Chainer-map om voorbeeldnotitieblokken te vinden.| 

## <a name="cuda-cudnn-nvidia-driver"></a>[CUDA-, cuDNN-, NVIDIA-stuurprogramma](https://developer.nvidia.com/cuda-toolkit)

|    |           |
| ------------- | ------------- |
| Ondersteunde versie(s) | 10.0.130|
| Ondersteunde DSVM-edities      | Windows en Linux   |
| Hoe is het geconfigureerd / geïnstalleerd op de DSVM?  |_nvidia-smi_ is beschikbaar op het systeempad.  |
| Hoe het uit te voeren      | Open een opdrachtprompt (op Windows) of een terminal (op Linux) en voer vervolgens _nvidia-smi_uit. |


## <a name="horovod"></a>[Horovod](https://github.com/uber/horovod)

|    |           |
| ------------- | ------------- |
| Ondersteunde versie(s) | 0.16.1|
| Ondersteunde DSVM-edities      | Linux (Ubuntu)   |
| Hoe is het geconfigureerd / geïnstalleerd op de DSVM?  | Horovod is geïnstalleerd in Python 3.5 |
| Hoe het uit te voeren      | Activeer de juiste omgeving op de terminal en voer Python uit. |

## <a name="keras"></a>[Keras](https://keras.io/)

|    |           |
| ------------- | ------------- |
| Ondersteunde versie(s) | 2.2.4 |
| Ondersteunde DSVM-edities      | Windows en Linux   |
| Hoe is het geconfigureerd / geïnstalleerd op de DSVM?  | Keras is geïnstalleerd in Python 3.6 op Windows en in Python 3.5 in Linux |
| Hoe het uit te voeren      | Activeer de juiste omgeving op de terminal en voer Python uit. |

## <a name="microsoft-cognitive-toolkit-cntk"></a>[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)

|    |           |
| ------------- | ------------- |
| Ondersteunde versie(s) | 2.5.1 |
| Ondersteunde DSVM-edities      | Windows en Linux   |
| Hoe is het geconfigureerd / geïnstalleerd op de DSVM?  | CNTK is geïnstalleerd in Python 3.6 op [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) en in Python 3.5 op [Linux](./dsvm-tools-languages.md#python-linux-edition)) |
| Hoe het uit te voeren      | Terminal: Activeer de juiste omgeving en voer Python uit. <br/>Jupyter: Maak verbinding met [Jupyter](provision-vm.md) of [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)en open vervolgens de CNTK-map voor voorbeelden. |

## <a name="mxnet"></a>[MXNet](https://mxnet.apache.org/)
|    |           |
| ------------- | ------------- |
| Ondersteunde versie(s) | 1.3.0 |
| Ondersteunde DSVM-edities      | Windows en Linux   |
| Hoe is het geconfigureerd / geïnstalleerd op de DSVM?  | MXNet is `C:\dsvm\tools\mxnet` geïnstalleerd in `/dsvm/tools/mxnet` op Windows en op Ubuntu. Python bindingen zijn geïnstalleerd in Python 3.6 op [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) en in Python 3.5 op [Linux](./dsvm-tools-languages.md#python-linux-edition)) R bindingen zijn ook opgenomen in de Ubuntu DSVM. |
| Hoe het uit te voeren      | Terminal: Activeer de juiste conda-omgeving en voer deze uit. `import mxnet` <br/>Jupyter: Maak verbinding met [Jupyter](provision-vm.md#access-the-dsvm) of [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)en open de `mxnet` map voor voorbeelden. |

## <a name="mxnet-model-server"></a>[MXNet Model Server](https://github.com/awslabs/mxnet-model-server#quick-start)

|    |           |
| ------------- | ------------- |
| Ondersteunde versie(s) | 1.0.1 |
| Ondersteunde DSVM-edities      | Windows en Linux   |
| Hoe is het geconfigureerd / geïnstalleerd op de DSVM?  | MXNet Model Server is geïnstalleerd in Python 3.6 op [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) en in Python 3.5 op [Linux](./dsvm-tools-languages.md#python-linux-edition)) |
| Hoe het uit te voeren      | Terminal: `sudo systemctl stop jupyterhub` Voer uit om eerst de JupyterHub-service te stoppen, omdat beide op dezelfde poort luisteren. Activeer vervolgens de juiste conda-omgeving en voer`mxnet-model-server --start --models squeezenet=https://s3.amazonaws.com/model-server/model_archive_1.0/squeezenet_v1.1.mar` |

## <a name="nvidia-system-management-interface-nvidia-smi"></a>[NVidia System Management Interface (nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface)

|    |           |
| ------------- | ------------- |
| Ondersteunde versie(s) |  |
| Ondersteunde DSVM-edities      | Windows en Linux   |
| Waar is het voor? | NVIDIA-tool voor het opvragen van GPU-activiteit |
| Hoe is het geconfigureerd / geïnstalleerd op de DSVM?  | `nvidia-smi`is op het systeempad. |
| Hoe het uit te voeren      | Open op een virtuele machine **met GPU's**een opdrachtprompt (op Windows) `nvidia-smi`of een terminal (op Linux) en voer deze uit. |

## <a name="pytorch"></a>[PyTorch](https://pytorch.org/)

|    |           |
| ------------- | ------------- |
| Ondersteunde versie(s) | 1.2.0 (Ubuntu 16.04, Windows 2016), 1.4.0 (Ubuntu 18.04, Windows 2019) |
| Ondersteunde DSVM-edities      | Linux |
| Hoe is het geconfigureerd / geïnstalleerd op de DSVM?  | Geïnstalleerd in [Python 3.5](dsvm-tools-languages.md#python-linux-edition). Voorbeeld Jupyter notebooks zijn opgenomen, en monsters zijn in / dsvm / monsters / pytorch. |
| Hoe het uit te voeren      | Terminal: Activeer de juiste omgeving en voer Python uit.<br/>* [JupyterHub:](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)Maak verbinding en open vervolgens de PyTorch-map voor voorbeelden.  |

## <a name="tensorflow"></a>[TensorFlow (TensorFlow)](https://www.tensorflow.org/)

|    |           |
| ------------- | ------------- |
| Ondersteunde versie(s) | 1.13 |
| Ondersteunde DSVM-edities      | Windows, Linux |
| Hoe is het geconfigureerd / geïnstalleerd op de DSVM?  | Geïnstalleerd in Python 3.5 op [Linux](dsvm-tools-languages.md#python-linux-edition) en Python 3.6 op [Windows 2016](dsvm-tools-languages.md#python-windows-server-2016-edition) |
| Hoe het uit te voeren      | Terminal: Activeer de juiste omgeving en voer Python uit. <br/> * Jupyter: Maak verbinding met [Jupyter](provision-vm.md) of [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)en open vervolgens de TensorFlow-map voor voorbeelden.   |

## <a name="tensorflow-serving"></a>[TensorFlow Serving](https://www.tensorflow.org/serving/)

|    |           |
| ------------- | ------------- |
| Ondersteunde versie(s) | 1.12 |
| Ondersteunde DSVM-edities      | Linux |
| Hoe is het geconfigureerd / geïnstalleerd op de DSVM?  | tensorflow_model_server is beschikbaar op de terminal. |
| Hoe het uit te voeren      |  Voorbeelden zijn [online](https://www.tensorflow.org/serving/)beschikbaar.   |


## <a name="theano"></a>[Theano](https://github.com/Theano/Theano)

|    |           |
| ------------- | ------------- |
| Ondersteunde versie(s) | 1.0.3 |
| Ondersteunde DSVM-edities      | Linux |
| Hoe is het geconfigureerd / geïnstalleerd op de DSVM?  |Theano is geïnstalleerd in Python 2.7 _(root)_ en in Python 3.5 _(py35)_ omgeving. |
| Hoe het uit te voeren      |  Terminal: Activeer de Python-versie die u wilt (root of py35), voer Python uit en importeer Theano.<br/>* Jupyter: Selecteer de Python 2.7 of 3.5 kernel en importeer Theano.  <br/>Als u een recente MKL-bug (Math Kernel Library) wilt omzeilen, moet u eerst de MKL-threadinglaag als volgt instellen:<br/><br/>`export MKL_THREADING_LAYER=GNU`  |