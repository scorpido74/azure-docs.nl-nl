---
title: Zelf studie-een ontwikkelings pijplijn maken in azure met Jenkins
description: Zelfstudie - In deze zelfstudie leert u hoe u een virtuele Jenkins-machine maakt in Azure die bij elke codedoorvoer gegevens ophaalt vanuit GitHub en een nieuwe Docker-container compileert voor het uitvoeren van uw app.
keywords: Jenkins, azure, devops, pijp lijn, cicd, docker
ms.topic: tutorial
ms.date: 03/27/2017
ms.openlocfilehash: 2560d03282b2b3c8193a0b8c2a7a9f7c4036e75a
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77606445"
---
# <a name="tutorial-create-a-development-infrastructure-on-a-linux-vm-in-azure-with-jenkins-github-and-docker"></a>Zelfstudie: Een infrastructuur voor ontwikkeling maken op een virtuele Linux-machine in Azure met Jenkins, GitHub en Docker

U kunt een CI/CD-pijplijn (continue integratie en implementatie) gebruiken voor het automatiseren van de compilatie- en testfase van de app-ontwikkeling. In deze zelfstudie maakt u een CI/CD-pijplijn op een virtuele machine in Azure. U leert onder andere:

> [!div class="checklist"]
> * Een Jenkins-VM maken
> * Jenkins installeren en configureren
> * Webhook-integratie tussen GitHub en Jenkins maken
> * Jenkins buildtaken maken vanuit GitHub-doorvoeracties
> * Een Docker-kopie van uw app maken
> * Controleren of de GitHub-doorvoeracties een nieuwe Docker-installatiekopie bouwen en de lopende app bijwerken

In deze zelf studie wordt gebruikgemaakt van de CLI binnen de [Azure Cloud shell](https://docs.microsoft.com/azure/cloud-shell/overview), die voortdurend wordt bijgewerkt naar de nieuwste versie. Als u de Cloud Shell wilt openen, selecteert u **deze** in het begin van een wille keurig code blok.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.0.30 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-jenkins-instance"></a>Een Jenkins-instantie maken
In een vorige zelfstudie over [Het aanpassen van een virtuele Linux-machine bij de eerste keer opstarten](../virtual-machines/linux/tutorial-automate-vm-deployment.md), hebt u geleerd hoe u de aanpassing van de virtuele machine met cloud-init moet automatiseren. Deze zelfstudie gebruikt een cloud-init-bestand om Jenkins en een Docker te installeren op een virtuele machine. Jenkins, een populaire open-source automatiseringsserver, integreert naadloos met Azure, wat continue integratie (CI) en continue levering (CD) mogelijk maakt. Voor meer zelfstudies over het gebruik van Jenkins bekijkt u [Jenkins in Azure hub](https://docs.microsoft.com/azure/jenkins/).

Maak in uw huidige shell een bestand met de naam *cloud-init-jenkins.txt* en plak de volgende configuratie. Maak bijvoorbeeld het bestand in de Cloud Shell, niet op uw lokale computer. Voer `sensible-editor cloud-init-jenkins.txt` in voor het maken van het bestand en om een overzicht van beschikbare editors te zien. Controleer of het hele cloud-init-bestand correct is gekopieerd, met name de eerste regel:

```yaml
#cloud-config
package_upgrade: true
write_files:
  - path: /etc/systemd/system/docker.service.d/docker.conf
    content: |
      [Service]
        ExecStart=
        ExecStart=/usr/bin/dockerd
  - path: /etc/docker/daemon.json
    content: |
      {
        "hosts": ["fd://","tcp://127.0.0.1:2375"]
      }
runcmd:
  - apt install openjdk-8-jre-headless -y
  - wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -
  - sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
  - apt-get update && apt-get install jenkins -y
  - curl -sSL https://get.docker.com/ | sh
  - usermod -aG docker azureuser
  - usermod -aG docker jenkins
  - service jenkins restart
```

Voordat u een virtuele machine kunt maken, moet u eerst een resourcegroep maken met [az-groep maken](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroupJenkins* gemaakt op de locatie *eastus*:

```azurecli-interactive 
az group create --name myResourceGroupJenkins --location eastus
```

Maak een virtuele machine met [az vm create](/cli/azure/vm). Gebruik de `--custom-data`-parameter om door te geven in uw cloud-init-configuratiebestand. Geef het volledige pad naar *cloud-init-jenkins.txt* op als u het bestand buiten uw werkmap hebt opgeslagen.

```azurecli-interactive 
az vm create --resource-group myResourceGroupJenkins \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-jenkins.txt
```

Het duurt enkele minuten voordat de virtuele machine wordt gemaakt en geconfigureerd.

Zorg ervoor dat webverkeer uw virtuele machine kan bereiken kan door [az vm open-port](/cli/azure/vm) te gebruiken om poort *8080* te openen voor Jenkins-verkeer en poort *1337* voor de Node.js-app die wordt gebruikt voor het uitvoeren van een voorbeeld-app:

```azurecli-interactive 
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 8080 --priority 1001
az vm open-port --resource-group myResourceGroupJenkins --name myVM --port 1337 --priority 1002
```


## <a name="configure-jenkins"></a>Jenkins configureren
Voor toegang tot uw Jenkins-exemplaar moet u het openbare IP-adres van uw virtuele machine verkrijgen:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Uit veiligheidsoverwegingen moet u eerst het in initiële beheerderswachtwoord invoeren dat is opgeslagen in een tekstbestand op de virtuele machine, om met de Jenkins-installatie te beginnen. Gebruik het openbare IP-adres dat is verkregen in de vorige stap om SSH te verbinden met uw virtuele machine:

```bash
ssh azureuser@<publicIps>
```

Controleer of Jenkins wordt uitgevoerd met behulp van de opdracht `service`:

```bash
$ service jenkins status
● jenkins.service - LSB: Start Jenkins at boot time
   Loaded: loaded (/etc/init.d/jenkins; generated)
   Active: active (exited) since Tue 2019-02-12 16:16:11 UTC; 55s ago
     Docs: man:systemd-sysv-generator(8)
    Tasks: 0 (limit: 4103)
   CGroup: /system.slice/jenkins.service

Feb 12 16:16:10 myVM systemd[1]: Starting LSB: Start Jenkins at boot time...
...
```

Bekijk het `initialAdminPassword` voor uw Jenkins-installatie en kopieer het:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Als het bestand nog niet beschikbaar is, wacht u een paar minuten op de cloud-init om de Jenkins- en Docker-installatie te voltooien.

Open nu een webbrowser en ga naar `http://<publicIps>:8080`. Voltooi de eerste Jenkins-installatie als volgt:

- Kies **Select plugins to install**
- Zoek naar *GitHub* in het tekstvak aan de bovenkant. Schakel het selectievakje voor *GitHub* in en selecteer **Install**
- Maak de eerste gebruiker met beheerdersrechten. Voer een gebruikersnaam in, zoals **admin**, en geef een veilig wachtwoord op. Als laatste typt u een volledige naam en e-mailadres.
- Selecteer **Save and Finish**
- Zodra Jenkins gereed is, selecteert u **Start using Jenkins**
  - Als uw webbrowser een lege pagina weergeeft wanneer u Jenkins gaat gebruiken, moet u de Jenkins-service opnieuw starten. Typ vanuit uw SSH-sessie `sudo service jenkins restart`, en vernieuw vervolgens uw webbrowser.
- Meld u aan bij Jenkins met de gebruikersnaam die en het wachtwoord dat u hebt gemaakt.


## <a name="create-github-webhook"></a>Een GitHub-webhook maken
Voor het configureren van de integratie met GitHub opent u de [voorbeeld-app Node.js Hallo wereld](https://github.com/Azure-Samples/nodejs-docs-hello-world) uit de opslagplaats met Azure-voorbeelden. Om de opslagplaats naar uw eigen GitHub-account te vertakken, selecteert u de knop **Fork** in de rechterbovenhoek.

Maak een webhook binnen de fork die u hebt gemaakt:

- Selecteer **Instellingen**, selecteer daarna **Webhooks** aan de linkerkant.
- Kies **Webhook toevoegen**, voer dan *Jenkins* in het filtervak in.
- Voer voor de **Payload-URL**`http://<publicIps>:8080/github-webhook/` in. Zorg ervoor dat u de afsluitende / toevoegt
- Selecteer voor **Inhoudstype***application/x-www-form-urlencoded*.
- Voor **Met welke gebeurtenissen wilt u deze webhook activeren?** selecteert u *Alleen de push-gebeurtenis.*
- Stel **Actief** in op ingeschakeld.
- Klik op **Webhook toevoegen**.

![Voeg de GitHub-webhook toe aan uw gevorkte opslagplaats](media/tutorial-jenkins-github-docker-cicd/github-webhook.png)


## <a name="create-jenkins-job"></a>Jenkins-taak maken
Om Jenkins te laten reageren op een gebeurtenis in GitHub zoals het vastleggen van code, kunt u een Jenkins-taak maken. Gebruik de URL's voor uw eigen GitHub-fork.

Selecteer in uw Jenkins-website **Nieuwe taken maken** vanaf de startpagina:

- Voer *HelloWorld* in als de taaknaam. Kies **Freestyle-project**, selecteer daarna **OK**.
- Selecteer onder de sectie **Algemeen** de optie **GitHub-project** en voer de URL van uw gevorkte opslagplaats in, zoals *https://github.com/cynthn/nodejs-docs-hello-world*
- Selecteer onder de sectie **Broncodebeheer** de optie **Git** en voer de *.git*-URL van uw gevorkte opslagplaats in, zoals *https://github.com/cynthn/nodejs-docs-hello-world.git*
- Onder de sectie **Triggers compileren** selecteert u **GitHub hook-trigger voor GITscm-polling**.
- Onder de sectie **Build** selecteert u **Build-stap toevoegen**. Selecteer **Shell uitvoeren**, voer dan `echo "Test"` in het opdrachtvenster in.
- Selecteer **Opslaan** aan de onderkant van het venster met taken.


## <a name="test-github-integration"></a>GitHub-integratie testen
Als u de GitHub-integratie met Jenkins wilt testen, moet een wijziging in uw fork worden doorgevoerd. 

In de GitHub-webgebruikersinterface selecteert u uw gevorkte opslagplaats en vervolgens het bestand **index.js**. Selecteer het potloodpictogram om dit bestand te bewerken, zodat in regel 6 staat:

```javascript
response.end("Hello World!");
```

Om uw wijzigingen door te voeren, selecteert u de knop **Wijzigingen doorvoeren** onderaan.

Een nieuwe build begint in Jenkins, onder de sectie **Build-geschiedenis** in de linkerbenedenhoek van de pagina met taken. Kies de link voor het build-nummer en selecteer **Console-uitvoer** aan de linkerkant. U kunt de stappen bekijken die Jenkins neemt als uw code wordt opgehaald uit GitHub en de build-actie het bericht `Test` naar de console uitvoert. Telkens wanneer een doorvoer wordt gemaakt in GitHub, zoekt de webhook Jenkins op en activeert op deze manier een nieuwe build.


## <a name="define-docker-build-image"></a>Afbeelding van Docker-build definiëren
Om de uitvoering van de Node.js-app op basis van uw GitHub-doorvoeracties te zien, maken we een Docker-kopie om de app uit te voeren. De installatiekopie wordt gecompileerd uit een Docker-bestand dat het configureren van de container definieert, waarin de app wordt uitgevoerd. 

Wijzig de Jenkins-werkruimtemap naar de naam van de taak die u in een vorige stap hebt gemaakt van de SSH-verbinding met uw virtuele machine. In dit voorbeeld was de naam *HelloWorld*.

```bash
cd /var/lib/jenkins/workspace/HelloWorld
```

Maak een bestand in deze werkruimtemap met `sudo sensible-editor Dockerfile` en plak de volgende inhoud. Controleer of het hele Docker-bestand correct is gekopieerd, met name de eerste regel:

```yaml
FROM node:alpine

EXPOSE 1337

WORKDIR /var/www
COPY package.json /var/www/
RUN npm install
COPY index.js /var/www/
```

Dit Docker-bestand maakt gebruik van de Node.js basiskopie met Alpine Linux, maakt poort 1337 zichtbaar waarop de Hallo wereld-app wordt uitgevoerd, kopieert vervolgens de app-bestanden en initialiseert ze.


## <a name="create-jenkins-build-rules"></a>Jenkins-build-regels maken
In een vorige stap heeft u een eenvoudige Jenkins-build-regel gemaakt die een bericht naar de console uitvoert. We maken de build-stap om ons Docker-bestand te gebruiken en de app uit te voeren.

In uw Jenkins-instantie selecteert u de taak die u in een vorige stap hebt gemaakt. Selecteer **Configureren** aan de linkerkant en blader omlaag naar de sectie **Build**:

- Verwijder uw bestaande `echo "Test"`-build-stap. Selecteer de rode x in de rechterbovenhoek van het bestaande vak voor de build-stap.
- Kies **Build-stap toevoegen**, selecteer daarna **Shell uitvoeren**
- In het vak **Opdracht** voert u de volgende Docker-opdrachten in en selecteert u **Opslaan**:

  ```bash
  docker build --tag helloworld:$BUILD_NUMBER .
  docker stop helloworld && docker rm helloworld
  docker run --name helloworld -p 1337:1337 helloworld:$BUILD_NUMBER node /var/www/index.js &
  ```

De build-stappen van Docker maken een installatiekopie en labelen deze met het Jenkins build-nummer, zodat u een geschiedenis van installatiekopieën kunt onderhouden. Eventuele bestaande containers die de app uitvoeren, worden gestopt en daarna verwijderd. Een nieuwe container wordt vervolgens opgestart met behulp van de installatiekopie en voert uw Node.js-app uit op basis van de meest recente doorvoeringen in GitHub.


## <a name="test-your-pipeline"></a>Test uw pijplijn
Om de hele pijplijn in actie te bekijken, bewerk opnieuw het bestand *index.js* in uw gevorkte GitHub-repo en selecteer **Wijziging doorvoeren**. Een nieuwe taak wordt gestart in Jenkins op basis van de webhook voor GitHub. Het duurt een paar seconden om de Docker-kopie te maken en uw app in een nieuwe container op te starten.

Indien nodig, moet u opnieuw het openbare IP-adres van uw virtuele machine verkrijgen:

```azurecli-interactive 
az vm show --resource-group myResourceGroupJenkins --name myVM -d --query [publicIps] --o tsv
```

Open een webbrowser en voer `http://<publicIps>:1337` in. Uw Node.js-app wordt weergegeven en geeft de meest recente doorvoeringen in uw GitHub-fork als volgt weer:

![Node.js-app uitvoeren](media/tutorial-jenkins-github-docker-cicd/running-nodejs-app.png)

Maak nu een andere bewerking van het bestand *index.js* in GitHub en voer de wijziging door. Wacht een paar seconden tot de taak is voltooid in Jenkins en vernieuw vervolgens uw webbrowser. U ziet nu hoe de bijgewerkte versie van uw app wordt uitgevoerd in een nieuwe container:

![Node.js-app wordt uitgevoerd na nogmaals doorvoeren van GitHub](media/tutorial-jenkins-github-docker-cicd/another-running-nodejs-app.png)


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie configureerde u GitHub om een Jenkins build-taak uit te voeren op elke doorvoercode en vervolgens een Docker-container te implementeren om uw app te testen. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een Jenkins-VM maken
> * Jenkins installeren en configureren
> * Webhook-integratie tussen GitHub en Jenkins maken
> * Jenkins buildtaken maken vanuit GitHub-doorvoeracties
> * Een Docker-kopie van uw app maken
> * Controleren of de GitHub-doorvoeracties een nieuwe Docker-installatiekopie bouwen en de lopende app bijwerken

Ga naar de volgende zelfstudie voor meer informatie over het integreren van Jenkins met Azure DevOps Services.

> [!div class="nextstepaction"]
> [Apps implementeren met Jenkins en Azure DevOps Services](tutorial-build-deploy-jenkins.md)