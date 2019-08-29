---
title: SSH-ondersteuning voor App Service op Linux-Azure | Microsoft Docs
description: Meer informatie over het gebruik van SSH met Azure App Service op Linux.
keywords: Azure app service, Web-app, Linux, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: fef8a17de4539a1427c269cdc512063d07df195c
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066868"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>SSH-ondersteuning voor Azure App Service in Linux

[SSH (Secure Shell)](https://wikipedia.org/wiki/Secure_Shell) wordt vaak gebruikt om beheer opdrachten op afstand uit te voeren vanaf een opdracht regel Terminal. App Service op Linux biedt SSH-ondersteuning in de app-container met elk van de ingebouwde docker-installatie kopieën die worden gebruikt voor de runtime stack van nieuwe web-apps. 

![Runtime stacks](./media/app-service-linux-ssh-support/app-service-linux-runtime-stack.png)

Voor aangepaste docker-installatie kopieën kunt u de SSH-server configureren in uw aangepaste installatie kopie.

U kunt ook rechtstreeks vanuit uw lokale ontwikkel computer verbinding maken met de container via SSH en SFTP.

## <a name="open-ssh-session-in-browser"></a>SSH-sessie openen in browser

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-no-h.md)]

## <a name="use-ssh-support-with-custom-docker-images"></a>SSH-ondersteuning gebruiken met aangepaste docker-installatie kopieën

Zie [SSH configureren in een aangepaste container](configure-custom-container.md#enable-ssh).

## <a name="open-ssh-session-from-remote-shell"></a>SSH-sessie openen vanuit externe shell

> [!NOTE]
> Deze functie is momenteel beschikbaar als preview-versie.
>

Met TCP-tunneling kunt u een netwerk verbinding maken tussen uw ontwikkel computer en Web App for Containers via een geverifieerde WebSocket-verbinding. U kunt hiermee een SSH-sessie openen met uw container in App Service van de client van uw keuze.

Om aan de slag te gaan, moet u [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest)installeren. Open [Azure Cloud shell](../../cloud-shell/overview.md)om te zien hoe het werkt zonder Azure CLI te installeren. 

Open een externe verbinding met uw app met behulp van de opdracht [AZ webapp Remote-Connection Create](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create) . \<Geef \_  _\<het abonnement-id >_ ,  _\<de groeps naam >_ en de app-naam > _ op voor uw app.

```azurecli-interactive
az webapp create-remote-connection --subscription <subscription-id> --resource-group <resource-group-name> -n <app-name> &
```

> [!TIP]
> `&`aan het einde van de opdracht is alleen bedoeld voor het gemak als u Cloud Shell gebruikt. Het proces wordt op de achtergrond uitgevoerd, zodat u de volgende opdracht in dezelfde shell kunt uitvoeren.

De uitvoer van de opdracht geeft u de informatie die u nodig hebt om een SSH-sessie te openen.

```
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Open een SSH-sessie met uw container met de client van uw keuze, met behulp van de lokale poort. In het volgende voor beeld wordt de standaard [SSH](https://ss64.com/bash/ssh.html) -opdracht gebruikt:

```azurecli-interactive
ssh root@127.0.0.1 -p <port>
```

Wanneer u hierom wordt gevraagd `yes` , typt u om door te gaan met verbinding maken. U wordt vervolgens gevraagd om het wacht woord. Gebruik `Docker!`, dat eerder is weer gegeven.

```
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
```

Zodra u bent geverifieerd, wordt het welkomst scherm van de sessie weer gegeven.

```
  _____
  /  _  \ __________ _________   ____
 /  /_\  \___   /  |  \_  __ \_/ __ \
/    |    \/    /|  |  /|  | \/\  ___/
\____|__  /_____ \____/ |__|    \___  >
        \/      \/                  \/
A P P   S E R V I C E   O N   L I N U X

0e690efa93e2:~#
```

U bent nu verbonden met uw connector.  

Voer de opdracht [boven](https://ss64.com/bash/top.html) uit. U zou het proces van uw app moeten kunnen zien in de lijst proces. In het voor beeld hieronder ziet u de `PID 263`uitvoer.

```
Mem: 1578756K used, 127032K free, 8744K shrd, 201592K buff, 341348K cached
CPU:   3% usr   3% sys   0% nic  92% idle   0% io   0% irq   0% sirq
Load average: 0.07 0.04 0.08 4/765 45738
  PID  PPID USER     STAT   VSZ %VSZ CPU %CPU COMMAND
    1     0 root     S     1528   0%   0   0% /sbin/init
  235     1 root     S     632m  38%   0   0% PM2 v2.10.3: God Daemon (/root/.pm2)
  263   235 root     S     630m  38%   0   0% node /home/site/wwwroot/app.js
  482   291 root     S     7368   0%   0   0% sshd: root@pts/0
45513   291 root     S     7356   0%   0   0% sshd: root@pts/1
  291     1 root     S     7324   0%   0   0% /usr/sbin/sshd
  490   482 root     S     1540   0%   0   0% -ash
45539 45513 root     S     1540   0%   0   0% -ash
45678 45539 root     R     1536   0%   0   0% top
45733     1 root     Z        0   0%   0   0% [init]
45734     1 root     Z        0   0%   0   0% [init]
45735     1 root     Z        0   0%   0   0% [init]
45736     1 root     Z        0   0%   0   0% [init]
45737     1 root     Z        0   0%   0   0% [init]
45738     1 root     Z        0   0%   0   0% [init]
```

## <a name="next-steps"></a>Volgende stappen

U kunt vragen en problemen in het [Azure-forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)plaatsen.

Zie voor meer informatie over Web App for Containers:

* [Inleiding tot externe fout opsporing van node. js-apps op Azure App Service van VS code](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Een aangepaste Docker-installatiekopie uitvoeren voor Web App for Containers](quickstart-docker-go.md)
* [.NET Core gebruiken in Azure App Service onder Linux](quickstart-dotnetcore.md)
* [Ruby gebruiken in Azure App Service onder Linux](quickstart-ruby.md)
* [Web App for Containers van Azure App Service: veelgestelde vragen](app-service-linux-faq.md)
