---
title: 'Interactieve fout opsporing: VS code & ML Compute instances (preview)'
titleSuffix: Azure Machine Learning
description: U kunt met behulp van Azure Machine Learning uw code op een externe wijze met de code in-en uitzetten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: jmartens
author: j-martens
ms.date: 07/09/2020
ms.openlocfilehash: dfb8dac1b9027acd01b3c13c919d9c3cd8368819
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320116"
---
# <a name="debug-interactively-on-an-azure-machine-learning-compute-instance-with-vs-code-remote-preview"></a>Interactief fouten opsporen in een Azure Machine Learning Compute-exemplaar met een externe versus code (preview-versie)

In dit artikel leert u hoe u de externe Visual Studio code kunt instellen op een Azure Machine Learning Compute-exemplaar, zodat u uw code op een andere manier op een andere manier kan **debuggen** vanuit VS code. 

+ Een [Azure machine learning Compute-exemplaar](concept-compute-instance.md) is een volledig beheerd werk station in de Cloud voor gegevens wetenschappers en biedt beheer-en Enter prise-gereedheids mogelijkheden voor IT-beheerders. 


+ [Visual Studio code Remote](https://code.visualstudio.com/docs/remote/remote-overview) Met ontwikkeling kunt u een container, een externe computer of het Windows-subsysteem voor Linux (WSL) gebruiken als een ontwikkel omgeving met volledige functionaliteit. 

## <a name="prerequisite"></a>Vereiste  

Op Windows-platforms moet u [een met OpenSSH compatibele SSH-client installeren](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) als deze nog niet aanwezig is. 

> [!Note]
> PuTTy wordt niet ondersteund in Windows omdat de SSH-opdracht zich in het pad moet bevinden. 

## <a name="get-ip-and-ssh-port"></a>IP-en SSH-poort ophalen 

1. Ga naar de Azure Machine Learning Studio op https://ml.azure.com/ .

2. Selecteer uw [werk ruimte](concept-workspace.md).
1. Klik op het tabblad **Compute instances** .
1. Klik in de kolom **toepassings-URI** op de **SSH** -koppeling van het reken exemplaar dat u wilt gebruiken als een externe compute. 
1. Noteer het IP-adres en de SSH-poort in het dialoog venster. 
1. Sla uw persoonlijke sleutel op in de map ~/.SSH/op de lokale computer. Open bijvoorbeeld een editor voor een nieuw bestand en plak de sleutel in: 

   **Linux**: 
   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**: 
   ```
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa 
   ```

   De persoonlijke sleutel ziet er ongeveer als volgt uit:
   ```
   -----BEGIN RSA PRIVATE KEY----- 

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD 
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw== 

   -----END RSA PRIVATE KEY----- 
   ```

1. Wijzig de machtigingen voor het bestand om er zeker van te zijn dat alleen u het bestand kunt lezen.  
   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa   
   ```

## <a name="add-instance-as-a-host"></a>Exemplaar als host toevoegen 

Open het bestand `~/.ssh/config` (Linux) of `C:\Users<username>.ssh\config` (Windows) in een editor en voeg een nieuwe vermelding toe die er ongeveer als volgt uitziet:

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa   
```

Hier vindt u enkele details over de velden: 

|Veld|Beschrijving|
|----|---------|
|Host|Een wille keurige steno gebruiken voor het reken exemplaar |
|HostName|Dit is het IP-adres van het reken exemplaar |
|Poort|Dit is de poort die wordt weer gegeven in het bovenstaande SSH-dialoog venster |
|Gebruiker|Dit moet worden `azureuser` |
|IdentityFile|Moet verwijzen naar het bestand waarin u de persoonlijke sleutel hebt opgeslagen |

Nu moet u uw reken exemplaar SSHen met behulp van de steno die u hierboven hebt gebruikt `ssh azmlci1` . 

## <a name="connect-vs-code-to-the-instance"></a>VERSUS code verbinden met het exemplaar 

1. [Installeer Visual Studio code](https://code.visualstudio.com/).

1. [Installeer de externe SSH-extensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh). 

1. Klik op het pictogram extern-SSH aan de linkerkant om uw SSH-configuraties weer te geven.

1. Klik met de rechter muisknop op de SSH-hostconfiguratie die u zojuist hebt gemaakt.

1. Selecteer **verbinding maken met host in het huidige venster**. 

Vanaf hier kunt u zich helemaal op het reken proces bevinden en nu bewerken, fouten opsporen, Git gebruiken, uitbrei dingen, enzovoort, net als bij uw lokale Visual Studio-code. 

## <a name="next-steps"></a>Volgende stappen

Nu u Visual Studio code Remote hebt ingesteld, kunt u een reken instantie gebruiken als externe Compute van Visual Studio code om uw code interactief te debuggen. 

[Zelf studie: uw eerste ml-model trainen](tutorial-1st-experiment-sdk-train.md) laat zien hoe u een reken instantie met een geïntegreerde notebook kunt gebruiken.