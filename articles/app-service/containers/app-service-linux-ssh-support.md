---
title: SSH-toegang voor Linux-containers
description: U een SSH-sessie openen voor een Linux-container in Azure App Service. Aangepaste Linux containers worden ondersteund met enkele wijzigingen in uw aangepaste afbeelding.
keywords: azure app service, web app, linux, oss
author: msangapu-msft
ms.assetid: 66f9988f-8ffa-414a-9137-3a9b15a5573c
ms.topic: article
ms.date: 02/25/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: dab13f222b441c7415a8d09d0d91ab3af5aaf836
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280181"
---
# <a name="ssh-support-for-azure-app-service-on-linux"></a>SSH-ondersteuning voor Azure App Service onder Linux

[Secure Shell (SSH)](https://wikipedia.org/wiki/Secure_Shell) wordt vaak gebruikt om administratieve opdrachten op afstand uit te voeren vanaf een command-line terminal. App Service op Linux biedt SSH-ondersteuning in de app-container. 

![Linux App Service SSH](./media/app-service-linux-ssh-support/app-service-linux-ssh.png)

U ook rechtstreeks vanuit uw lokale ontwikkelingsmachine verbinding maken met de container via SSH en SFTP.

## <a name="open-ssh-session-in-browser"></a>SSH-sessie openen in de browser

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-no-h.md)]

## <a name="use-ssh-support-with-custom-docker-images"></a>SSH-ondersteuning gebruiken met aangepaste Docker-afbeeldingen

Zie [SSH configureren in een aangepaste container](configure-custom-container.md#enable-ssh).

## <a name="open-ssh-session-from-remote-shell"></a>SSH-sessie openen vanuit externe shell

> [!NOTE]
> Deze functie bevindt zich momenteel in Preview.
>

Met TCP-tunneling u via een geverifieerde WebSocket-verbinding een netwerkverbinding tussen uw ontwikkelmachine en Web App for Containers maken. Hiermee u een SSH-sessie openen met uw container die wordt uitgevoerd in App Service van de klant van uw keuze.

Om aan de slag te gaan, moet u [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)installeren. Open [Azure Cloud Shell](../../cloud-shell/overview.md)om te zien hoe het werkt zonder Azure CLI te installeren. 

Open een externe verbinding met uw app via de opdracht afstandsbediening van [az webapp.](/cli/azure/ext/webapp/webapp/remote-connection?view=azure-cli-latest#ext-webapp-az-webapp-remote-connection-create) Geef _ \<>abonnement-id _op, \_ \< _ \<groepsnaam>_ en app-naam>_ voor uw app.

```azurecli-interactive
az webapp create-remote-connection --subscription <subscription-id> --resource-group <resource-group-name> -n <app-name> &
```

> [!TIP]
> `&`aan het einde van de opdracht is gewoon voor het gemak als u Cloud Shell gebruikt. Het proces wordt op de achtergrond uitgevoerd, zodat u de volgende opdracht in dezelfde shell uitvoeren.

De opdrachtuitvoer geeft u de informatie die u nodig hebt om een SSH-sessie te openen.

```output
Port 21382 is open
SSH is available { username: root, password: Docker! }
Start your favorite client and connect to port 21382
```

Open een SSH-sessie met uw container met de klant van uw keuze, met behulp van de lokale poort. In het volgende voorbeeld wordt de [standaardssh-opdracht](https://ss64.com/bash/ssh.html) gebruikt:

```bash
ssh root@127.0.0.1 -p <port>
```

Wanneer u wordt `yes` gevraagd, typt u om verbinding te blijven maken. U wordt dan gevraagd om het wachtwoord. Gebruik `Docker!`, dat eerder aan u werd getoond.

```output
Warning: Permanently added '[127.0.0.1]:21382' (ECDSA) to the list of known hosts.
root@127.0.0.1's password:
```

Zodra u bent geverifieerd, ziet u het welkomstscherm van de sessie.

```output
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

Probeer de bovenste opdracht uit [te](https://ss64.com/bash/top.html) voeren. U moet het proces van uw app kunnen zien in de proceslijst. In het voorbeeld uitvoer hieronder, het `PID 263`is degene met .

```output
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

U vragen en zorgen plaatsen op het [Azure-forum.](https://docs.microsoft.com/answers/topics/azure-webapps.html)

Zie voor meer informatie over Web App for Containers:

* [Het op afstand debuggen van Node.js-apps introduceren op Azure App Service van VS Code](https://medium.com/@auchenberg/introducing-remote-debugging-of-node-js-apps-on-azure-app-service-from-vs-code-in-public-preview-9b8d83a6e1f0)
* [Een aangepaste Docker-installatiekopie uitvoeren voor Web App for Containers](quickstart-docker-go.md)
* [.NET Core gebruiken in Azure App Service onder Linux](quickstart-dotnetcore.md)
* [Ruby gebruiken in Azure App Service onder Linux](quickstart-ruby.md)
* [Web App for Containers van Azure App Service: veelgestelde vragen](app-service-linux-faq.md)
