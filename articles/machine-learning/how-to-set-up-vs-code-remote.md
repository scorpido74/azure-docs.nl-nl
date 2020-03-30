---
title: 'Interactieve foutopsporing: VS-code & ML-rekeninstanties'
titleSuffix: Azure Machine Learning
description: Stel VS Code Remote in om uw code interactief te debuggen met Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.date: 12/09/2019
ms.openlocfilehash: 1999d29db21f820fbcdbca08f2258b657673be3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77169755"
---
# <a name="debug-interactively-on-an-azure-machine-learning-compute-instance-with-vs-code-remote"></a>Foutopsporing interactief op een Azure Machine Learning Compute Instance met VS Code Remote

In dit artikel leert u hoe u Visual Studio Code Remote instelt op een Azure Machine Learning Compute Instance, zodat u uw code interactief **debuggen** vanuit VS-code. 

+ Een [Azure Machine Learning Compute Instance](concept-compute-instance.md) is een volledig beheerd cloudgebaseerd werkstation voor gegevenswetenschappers en biedt beheer- en bedrijfsgereedheidsmogelijkheden voor IT-beheerders. 


+ [Visual Studio-code op afstand](https://code.visualstudio.com/docs/remote/remote-overview) Met ontwikkeling u een container, externe machine of het Windows-subsysteem voor Linux (WSL) gebruiken als een volledige ontwikkelomgeving. 

## <a name="prerequisite"></a>Vereiste  

Op Windows-platforms moet u [een SSH-client met OpenSSH-compatibele SSH installeren](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) als deze nog niet aanwezig is. 

> [!Note]
> PuTTY wordt niet ondersteund op Windows, omdat de ssh-opdracht op het pad moet zijn. 

## <a name="get-ip-and-ssh-port"></a>Ip- en SSH-poort 

1. Ga naar de Azure https://ml.azure.com/Machine Learning-studio op .

2. Selecteer uw [werkruimte](concept-workspace.md).
1. Klik op het tabblad **Rekeninstanties.**
1. Klik in de kolom **ToepassingsURI** op de **SSH-koppeling** van de rekeninstantie die u als externe compute wilt gebruiken. 
1. Neem in het dialoogvenster nota van het IP-adres en de SSH-poort. 
1. Sla uw privésleutel op in de map ~/.ssh/ op uw lokale computer; Open bijvoorbeeld een editor voor een nieuw bestand en plak de sleutel in: 

   **Linux**: 
   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**: 
   ```
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa 
   ```

   De private key ziet er een beetje als volgt uit:
   ```
   -----BEGIN RSA PRIVATE KEY----- 

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD 
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw== 

   -----END RSA PRIVATE KEY----- 
   ```

1. Wijzig machtigingen in het bestand om ervoor te zorgen dat alleen u het bestand lezen.  
   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa   
   ```

## <a name="add-instance-as-a-host"></a>Instantie toevoegen als host 

Open het `~/.ssh/config` bestand (Linux) of `C:\Users<username>.ssh\config` (Windows) in een editor en voeg een nieuw item toe dat vergelijkbaar is met dit:

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa   
```

Hier enkele details over de velden: 

|Veld|Beschrijving|
|----|---------|
|Host|Gebruik wat steno u wilt voor de compute-instantie |
|HostName|Dit is het IP-adres van de rekeninstantie |
|Poort|Dit is de poort die wordt weergegeven in het bovenstaande SSH-dialoogvenster |
|Gebruiker|Dit moet `azureuser` |
|Identiteitsbestand|Moet naar het bestand wijzen waar u de privésleutel hebt opgeslagen |

Nu moet je in staat zijn om ssh naar uw `ssh azmlci1`rekeninstantie met behulp van de steno die u hierboven gebruikt, . 

## <a name="connect-vs-code-to-the-instance"></a>VS-code verbinden met de instantie 

1. [Visual Studio-code installeren](https://code.visualstudio.com/).

1. [Installeer de Remote SSH-extensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh). 

1. Klik op het pictogram Remote-SSH aan de linkerkant om uw SSH-configuraties weer te geven.

1. Klik met de rechtermuisknop op de SSH-hostconfiguratie die u zojuist hebt gemaakt.

1. Selecteer **Verbinding maken met host in huidig venster**. 

Vanaf nu werk je volledig aan de compute instance en kun je nu bewerken, debuggen, git gebruiken, extensies gebruiken, enz. 

## <a name="next-steps"></a>Volgende stappen

Nu u Visual Studio Code Remote hebt ingesteld, u een rekeninstantie gebruiken als externe compute van Visual Studio Code om uw code interactief te debuggen. 

[Zelfstudie: Train uw eerste ML-model](tutorial-1st-experiment-sdk-train.md) laat zien hoe u een rekeninstantie gebruiken met een geïntegreerd notitieblok.