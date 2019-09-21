---
title: Diep gaande leren & AI-Frameworks
titleSuffix: Azure Data Science Virtual Machine
description: Beschik bare uitgebreide lessen en hulpprogram ma's voor Azure Data Science Virtual Machine, waaronder tensor flow, PyTorch, Keras, Caffe, MXNet, Horovod, Theano, Chainer en meer.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 08/11/2019
ms.openlocfilehash: d8d351659bcfdcdddcc09831b5f95f580bf38f1e
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170863"
---
# <a name="deep-learning-and-ai-frameworks-for-azure-data-science-vm"></a>Diep gaande lessen en AI-frameworks voor Azure Data Science VM
De [Data Science virtual machine](https://aka.ms/dsvm) (DSVM) biedt ondersteuning voor een aantal diep gaande frameworks voor het bouwen van AI-toepassingen (kunst matige intelligentie) met Predictive Analytics en cognitieve mogelijkheden zoals een inzicht in de installatie kopie en taal.

Uitgebreide frameworks die beschikbaar zijn via DSVM zijn:

+ TensorFlow
+ PyTorch
+ Keras
+ Caffe
+ Caffe2
+ Torch
+ MXNet Model-Server
+ MXNet
+ Horovod
+ Theano
+ Chainer
+ Diep Water
+ NVIDIA CIJFERS
+ NVIDIA-smi
+ TensorFlow-servers
+ TensorRT
+ Microsoft Cognitive Toolkit

|Program ma's&nbsp;&nbsp;voor uitgebreide training&nbsp;op DSVM|Windows|Linux|Gebruiks&nbsp;notities|
|---------|-------------------|------------------|-----|
|[TensorFlow](https://www.tensorflow.org/) | Ja (Windows 2016) | Ja |Geïnstalleerd in Python 3,5 onder [Linux en Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) en python 3,6 op [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Voor beelden van Jupyter-notebooks zijn opgenomen in DSVM.<br/><br/>**Om het uit te voeren**:<br/>Terminal Activeer de juiste omgeving en voer python uit. <br/> Jupyter Maak verbinding met [Jupyter](provision-vm.md) of [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)en open vervolgens de map tensor flow voor voor beelden.  |
|[PyTorch](https://pytorch.org/)| Nee | Ja |Geïnstalleerd in [Python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). Voor beelden van Jupyter-notebooks zijn opgenomen.    <br/><br/>**Om het uit te voeren**:<br/>Terminal Activeer de juiste omgeving en voer python uit.<br/>* [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine): Maak verbinding en open de PyTorch-map voor voor beelden.  |
|[Keras](https://keras.io/)| Ja | Ja |API is geïnstalleerd in Python 3,5 op [Linux en Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) en in Python 3,6 op [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). [Zie voor beelden](https://github.com/fchollet/keras/tree/master/examples).<br/><br/>**Om het uit te voeren**:<br/>Terminal Activeer de juiste omgeving en voer python uit. <br/> Jupyter Down load de voor beelden van de GitHub-locatie, maak verbinding met [Jupyter](provision-vm.md) of [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)en open vervolgens de map voor beeld. |
|[Caffe](https://github.com/caffe2/caffe2) | Nee |Ja (Ubuntu)|Caffe is geïnstalleerd in `/opt/caffe`.   Voor beelden zijn `/opt/caffe/examples`in. <br/><br/>**Als u dit wilt uitvoeren**, gebruikt u X2Go om u aan te melden bij uw virtuele machine, start u een nieuwe terminal en voert u het volgende in:<br/>`cd /opt/caffe/examples`<br/>`source activate root`<br/>`jupyter notebook`<br/><br/>Er wordt een nieuw browservenster geopend met voorbeeldnotitieblokken. Binaire bestanden zijn in /opt/caffe/build/install/bin geïnstalleerd.<br/><br/>De geïnstalleerde versie van Caffe vereist python 2,7 en werkt niet met python 3,5, die standaard wordt geactiveerd. Als u wilt overschakelen naar python 2,7 `source activate root` , voert u uit om over te scha kelen naar de Anaconda-omgeving.|
|[Caffe2](https://github.com/caffe2/caffe2) | Nee |Ja (Ubuntu)|Caffe2 is geïnstalleerd in de [conda-omgeving voor Python 2.7 (root)](dsvm-languages.md#python-linux-and-windows-server-2012-edition). De bron is in `/opt/caffe2`.<br/>Voorbeeldnotitieblokken zijn opgenomen in JupyterHub.<br/><br/>**Om het uit te voeren**:<br/>Terminal Activeer de [hoofd-python-omgeving](dsvm-languages.md#python-linux-and-windows-server-2012-edition), start python en importeer Caffe2. <br/> * JupyterHub: [Maak verbinding met JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)en ga vervolgens naar de map Caffe2 om voorbeeld notitieblokken te vinden. Sommige laptops vereisen de hoofdmap Caffe2 moet worden ingesteld in de Python-code; Voer /opt/caffe2. |
|[Torch](http://torch.ch/) | Nee |Ja (Ubuntu)|Torch is geïnstalleerd in `/dsvm/tools/torch`. PyTorch in Python 2.7 is geïnstalleerd (_hoofdmap_), evenals Python 3.5 (_py35_) omgeving. Er zijn Torch- `/dsvm/samples/torch` voor beelden in en PyTorch `/dsvm/samples/pytorch`-voor beelden zijn in. |
|[MXNet](https://mxnet.io/) | Ja (Windows 2016) | Ja|MXNet is geïnstalleerd in `C:\dsvm\tools\mxnet` op Windows en `/dsvm/tools/mxnet` op Linux. Python-bindingen in Python 3.5 zijn geïnstalleerd op [Linux en Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) en Python 3.6 op [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). R-bindingen worden ook geïnstalleerd op Ubuntu.<br/><br/>Voorbeeld van Jupyter notebooks zijn opgenomen. <br/><br/>**Om het uit te voeren**:<br/>Terminal Activeer de juiste omgeving en voer python uit. <br/> Jupyter Maak verbinding met [Jupyter](provision-vm.md) of [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)en open vervolgens de map mxnet voor voor beelden.|
|[MXNet-model server](https://github.com/awslabs/mxnet-model-server) | Nee | Ja |Een server voor het maken van HTTP-eind punten voor MXNet-en ONNX-modellen. _Mxnet-model-server_ is beschikbaar op de Terminal. Voor beelden op de [pagina MXNet-model server](https://github.com/awslabs/mxnet-model-server).|
|[Horovod](https://github.com/uber/horovod) | Nee | Ja (Ubuntu) |Gedistribueerd diep-Learning Framework voor tensor flow. Horovod in Python 3.5 is geïnstalleerd op [Ubuntu](dsvm-languages.md#python-linux-and-windows-server-2012-edition).  [Zie voor beelden](https://github.com/uber/horovod/tree/master/examples).<br/><br/>**Als u deze wilt uitvoeren**, activeert u de juiste omgeving bij de Terminal en voert u python uit. |
|[Theano](https://github.com/Theano/Theano) | Nee | Ja (Ubuntu) |Theano is geïnstalleerd in Python 2,7 (_root_) en in de_py35_-omgeving (python 3,5).<br/><br/>**Om het uit te voeren**: <br/>Terminal Activeer de python-versie die u wilt (root of py35), voer python uit en importeer vervolgens Theano.<br/>Jupyter Selecteer de python 2,7-of 3,5-kernel en importeer vervolgens Theano.  <br/>Als u een recente MKL-fout (math kernel Library) wilt omzeilen, moet u eerst de MKL-threading Layer als volgt instellen:<br/><br/>`export MKL_THREADING_LAYER=GNU`|
|[Chainer](https://chainer.org/) |Nee | Ja |Chainer is geïnstalleerd in [Python 3.5](dsvm-languages.md#python-linux-and-windows-server-2012-edition). ChainerRL en ChainerCV opdrachtprompts ook geïnstalleerd. <br/><br/>Voorbeeldnotitieblokken zijn opgenomen in JupyterHub.<br/><br/>**Om het uit te voeren**: <br/>Terminal Activeer de [Python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) -omgeving, Voer _python_uit en importeer Chainer. <br/> * JupyterHub: [Maak verbinding met JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)en ga vervolgens naar de map Chainer om voorbeeld notitieblokken te vinden.|
|[NVidia-cijfers](https://github.com/NVIDIA/DIGITS) | Nee | Ja (Ubuntu) |Diep-learning systeem van NVIDIA voor het snel trainen van diep Learning-modellen. Cijfers is geïnstalleerd in `/dsvm/tools/DIGITS` en is beschikbaar als een service met de naam _cijfers_.  <br/><br/>**Om het uit te voeren**: <br/>Meld u aan bij de virtuele machine met X2Go. Start op een Terminal de service door uit te ```sudo systemctl start digits```voeren. <br/><br/>De service duurt ongeveer een minuut om te starten. Open een webbrowser en ga `http://localhost:5000`naar. Houd er rekening mee dat cijfers biedt een beveiligde aanmelding en niet buiten de virtuele machine moet worden blootgesteld.|
|[CUDA, cuDNN, NVIDIA-stuur programma](https://developer.nvidia.com/cuda-toolkit) |Ja | Ja | |
|NVIDIA-smi|Ja | Ja |NVIDIA-hulp programma voor het uitvoeren van query's op GPU-activiteiten; _NVIDIA-SMI_ is beschikbaar op het systeempad. <br/><br/>Open een opdracht prompt (in Windows) of een Terminal (op Linux) en voer _NVIDIA-SMI_uit.|
|[Tensor flow voor](https://www.tensorflow.org/serving/) | Nee | Ja |Een server die op een tensor flow-model moet ingrijpen; tensorflow_model_server is beschikbaar op de Terminal. Voorbeelden zijn beschikbaar [online](https://www.tensorflow.org/serving/).|
|[TensorRT](https://developer.nvidia.com/tensorrt) |  Nee | Ja (Ubuntu) |Een diep gaande leer server van NVIDIA. TensorRT wordt geïnstalleerd als een _apt_ pakket. Voorbeelden zijn beschikbaar [online](https://docs.nvidia.com/deeplearning/sdk/tensorrt-developer-guide/index.html#samples).|
|[Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/)|Ja | Ja | Geïnstalleerd in Python 3,5 onder [Linux en Windows 2012](dsvm-languages.md#python-linux-and-windows-server-2012-edition) en python 3,6 op [Windows 2016](dsvm-languages.md#python-windows-server-2016-edition). Voor beelden van Jupyter-notebooks zijn opgenomen in DSVM. <br/><br/>**Om het uit te voeren**: <br/>Terminal Activeer de juiste omgeving en voer python uit. <br/>Jupyter Maak verbinding met [Jupyter](provision-vm.md) of [JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)en open vervolgens de map CNTK voor voor beelden. |
|Diep Water|Nee | Ja (Ubuntu) |Diep-Learning Framework voor H2O, diep water is geïnstalleerd in [Python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) en is ook beschikbaar in `/dsvm/tools/deep_water`. Voorbeeldnotitieblokken zijn opgenomen in JupyterHub. CUDA 8 moet diep Water waarbij cuDNN 5.1. Dit bevindt zich standaard niet in het bibliotheekpad, want andere lessen in het kader van CUDA 9 en cuDNN 7 gebruiken. Als u wilt gebruiken, installeert u CUDA 8 + cuDNN 5,1 voor diepe water:<br/><br/>```export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:${LD_LIBRARY_PATH}```<br/>```export CUDA_ROOT=/usr/local/cuda-8.0```<br/><br/>Diep Water gebruiken:<br/>Terminal Activeer de [Python 3,5](dsvm-languages.md#python-linux-and-windows-server-2012-edition) -omgeving en voer _python_uit. <br/>* JupyterHub: [Maak verbinding met JupyterHub](dsvm-ubuntu-intro.md#how-to-access-the-ubuntu-data-science-virtual-machine)en ga vervolgens naar de map deep_water om voorbeeld notitieblokken te vinden.|
