---
title: Docker Compose gebruiken
description: Docker installeren en gebruiken en samen stellen op virtuele Linux-machines met de Azure CLI.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 02/14/2019
ms.author: cynthn
ms.openlocfilehash: 434a3ef8c9bc1738252d59a5dca5bec16d85e45e
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970309"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Aan de slag met docker en samen stellen om een toepassing met meerdere containers te definiëren en uit te voeren in azure
Met [opstellen](https://github.com/docker/compose)kunt u een eenvoudig tekst bestand gebruiken om een toepassing te definiëren die bestaat uit meerdere docker-containers. Vervolgens voert u uw toepassing in met één opdracht die alles doet om uw gedefinieerde omgeving te implementeren. Dit artikel laat bijvoorbeeld zien hoe u snel een WordPress-blog kunt instellen met een back-MariaDB SQL database op een Ubuntu-VM. U kunt ook samen stellen gebruiken om complexere toepassingen in te stellen.

Dit artikel is voor het laatst getest op 2/14/2019 met behulp van de [Azure Cloud shell](https://shell.azure.com/bash) en de [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli) -versie 2.0.58.

## <a name="create-docker-host-with-azure-cli"></a>Docker-host maken met Azure CLI
Installeer de nieuwste [Azure cli](/cli/azure/install-az-cli2) en meld u aan bij een Azure-account met de opdracht [AZ login](/cli/azure/reference-index).

Maak eerst een resource groep voor uw docker-omgeving met [AZ Group Create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli-interactive
az group create --name myDockerGroup --location eastus
```

Maak een bestand met de naam *Cloud-init. txt* en plak de volgende configuratie. Voer `sensible-editor cloud-init.txt` in voor het maken van het bestand en om een overzicht van beschikbare editors te zien. 

```yaml
#include https://get.docker.com
```

Maak een virtuele machine met [az vm create](/cli/azure/vm#az-vm-create). Gebruik de `--custom-data`-parameter om door te geven in uw cloud-init-configuratiebestand. Geef het volledige pad naar *cloud-init.txt* op als u het bestand buiten uw huidige werkmap hebt opgeslagen. In het volgende voor beeld wordt een virtuele machine met de naam *myDockerVM* gemaakt en wordt poort 80 op webverkeer geopend.

```azurecli-interactive
az vm create \
    --resource-group myDockerGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
az vm open-port --port 80 \
    --resource-group myDockerGroup \
    --name myDockerVM
```

Het duurt enkele minuten voordat de virtuele machine wordt gemaakt, de pakketten worden geïnstalleerd en de app gestart. Er zijn achtergrondtaken die nog worden uitgevoerd nadat u door de Azure CLI bent teruggeleid naar de prompt. Wanneer de virtuele machine is gemaakt, let op de `publicIpAddress` weergegeven door de Azure CLI. 

                 

## <a name="install-compose"></a>Opstellen installeren


SSH naar uw nieuwe docker-host-VM. Geef uw eigen IP-adres op.

```bash
ssh azureuser@10.10.111.11
```

Installeer opstellen op de VM.

```bash
sudo apt install docker-compose
```


## <a name="create-a-docker-composeyml-configuration-file"></a>Een docker-Compose. yml-configuratie bestand maken
Maak een `docker-compose.yml` configuratie bestand om de docker-containers te definiëren die op de virtuele machine moeten worden uitgevoerd. Het bestand geeft de installatie kopie op die moet worden uitgevoerd op elke container, de vereiste omgevings variabelen en afhankelijkheden, poorten en de koppelingen tussen containers. Zie voor meer informatie over de syntaxis van een yml-bestand de [Naslag informatie](https://docs.docker.com/compose/compose-file/)voor het opstellen van een bestand.

Maak een *docker-Compose. yml* -bestand. Gebruik uw favoriete tekst editor om gegevens toe te voegen aan het bestand. In het volgende voor beeld wordt het bestand gemaakt met de vraag om `sensible-editor` een editor te kiezen die u wilt gebruiken.

```bash
sensible-editor docker-compose.yml
```

Plak het volgende voor beeld in het bestand docker opstellen. Deze configuratie maakt gebruik van installatie kopieën uit het [DockerHub-REGI ster](https://registry.hub.docker.com/_/wordpress/) voor het installeren van WordPress (het open source-blog systeem en het inhouds beheersysteem) en een gekoppelde back-MariaDB SQL database. Voer uw eigen *MYSQL_ROOT_PASSWORD*in.

```yml
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="start-the-containers-with-compose"></a>De containers starten met opstellen
Voer in dezelfde map als uw *docker-Compose. yml* -bestand de volgende opdracht uit (afhankelijk van uw omgeving moet u mogelijk `docker-compose` uitvoeren met `sudo`):

```bash
sudo docker-compose up -d
```

Met deze opdracht start u de docker-containers die zijn opgegeven in *docker-Compose. yml*. Het duurt enkele minuten voordat deze stap is voltooid. De uitvoer ziet er ongeveer als volgt uit:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```


Als u wilt controleren of de containers zijn ingesteld, typt u `sudo docker-compose ps`. Als het goed is, ziet u er ongeveer als volgt uit:

```
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

U kunt nu rechtstreeks verbinding maken met WordPress op de VM op poort 80. Open een webbrowser en voer de IP-adres naam van uw virtuele machine in. U ziet nu het WordPress-start scherm, waar u de installatie kunt volt ooien en aan de slag gaat met de toepassing.

![WordPress-start scherm](./media/docker-compose-quickstart/wordpressstart.png)

## <a name="next-steps"></a>Volgende stappen
* Bekijk de [opdracht regel Naslag informatie](https://docs.docker.com/compose/reference/) en de [Gebruikers handleiding](https://docs.docker.com/compose/) voor meer voor beelden van het maken en implementeren van apps met meerdere containers.
* Gebruik een Azure Resource Manager sjabloon, uw eigen of een bijdrage van de [Community](https://azure.microsoft.com/documentation/templates/), om een Azure VM te implementeren met docker en een toepassing die is ingesteld met opstellen. De [implementatie van een WordPress-blog met docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) -sjabloon maakt bijvoorbeeld gebruik van docker en samen om WordPress snel te implementeren met een MySQL-back-end op een Ubuntu-VM.
* Probeer docker samen te integreren met een docker Swarm-cluster. Zie [opstellen gebruiken met Swarm](https://docs.docker.com/compose/swarm/) voor scenario's.

