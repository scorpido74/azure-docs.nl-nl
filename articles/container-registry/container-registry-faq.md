---
title: Veelgestelde vragen
description: Antwoorden op veelgestelde vragen met betrekking tot de Azure Container Registry-service
author: sajayantony
ms.topic: article
ms.date: 07/02/2019
ms.author: sajaya
ms.openlocfilehash: c0d51c9c31e4e6859eaedce371efeafaa5fd4f46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78403222"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Veelgestelde vragen over Azure Container Registry

In dit artikel worden veelgestelde vragen en bekende problemen over Azure Container Registry beantwoord.

## <a name="resource-management"></a>Resourcebeheer

- [Kan ik een Azure-containerregister maken met behulp van een resourcemanagersjabloon?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Is er beveiligingskwetsbaarheid scannen voor afbeeldingen in ACR?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Hoe configureer ik Kubernetes met Azure Container Registry?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Hoe krijg ik beheerdersreferenties voor een containerregister?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Hoe krijg ik beheerdersreferenties in een resourcemanagersjabloon?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [Verwijderen van replicatie mislukt met de verboden status, hoewel de replicatie wordt verwijderd met de Azure CLI of Azure PowerShell](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [Firewallregels worden bijgewerkt, maar worden niet van kracht](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Kan ik een Azure Container Registry maken met een resourcemanagersjabloon?

Ja. Hier is [een sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry) die u gebruiken om een register te maken.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Is er beveiligingskwetsbaarheid scannen voor afbeeldingen in ACR?

Ja. Bekijk de documentatie van [Azure Security Center,](https://docs.microsoft.com/azure/security-center/azure-container-registry-integration) [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) en [Aqua.](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry)

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Hoe configureer ik Kubernetes met Azure Container Registry?

Zie de documentatie voor [Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) en stappen voor [Azure Kubernetes Service](../aks/cluster-container-registry-integration.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Hoe krijg ik beheerdersreferenties voor een containerregister?

> [!IMPORTANT]
> Het beheerdersgebruikersaccount is ontworpen voor één gebruiker om toegang te krijgen tot het register, voornamelijk voor testdoeleinden. We raden u af om de beheerdersaccountreferenties met meerdere gebruikers te delen. Individuele identiteit wordt aanbevolen voor gebruikers en serviceprincipals voor headless scenario's. Zie [Overzicht van verificatie](container-registry-authentication.md).

Voordat u beheerdersreferenties ophaalt, moet u ervoor zorgen dat de beheerdersgebruiker van het register is ingeschakeld.

Ga als een te werk om referenties te krijgen met azure cli:

```azurecli
az acr credential show -n myRegistry
```

Azure Powershell gebruiken:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Hoe krijg ik beheerdersreferenties in een resourcemanagersjabloon?

> [!IMPORTANT]
> Het beheerdersgebruikersaccount is ontworpen voor één gebruiker om toegang te krijgen tot het register, voornamelijk voor testdoeleinden. We raden u af om de beheerdersaccountreferenties met meerdere gebruikers te delen. Individuele identiteit wordt aanbevolen voor gebruikers en serviceprincipals voor headless scenario's. Zie [Overzicht van verificatie](container-registry-authentication.md).

Voordat u beheerdersreferenties ophaalt, moet u ervoor zorgen dat de beheerdersgebruiker van het register is ingeschakeld.

Ga als eerste wachtwoord voor:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

Ga als het instellen van het tweede wachtwoord:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>Verwijderen van replicatie mislukt met de verboden status, hoewel de replicatie wordt verwijderd met de Azure CLI of Azure PowerShell

De fout wordt gezien wanneer de gebruiker machtigingen heeft voor een register, maar geen machtigingen op Reader-niveau op het abonnement heeft. Als u dit probleem wilt oplossen, wijst u Reader-machtigingen voor het abonnement toe aan de gebruiker:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>Firewallregels worden bijgewerkt, maar worden niet van kracht

Het duurt enige tijd om wijzigingen in firewallregels te verspreiden. Nadat u de firewall-instellingen hebt gewijzigd, moet u enkele minuten wachten voordat u deze wijziging verifieert.


## <a name="registry-operations"></a>Registerbewerkingen

- [Hoe krijg ik toegang tot Docker Registry HTTP API V2?](#how-do-i-access-docker-registry-http-api-v2)
- [Hoe verwijder ik alle manifesten waarnaar niet wordt verwezen door een tag in een repository?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Waarom vermindert het gebruik van het registerquotum niet na het verwijderen van afbeeldingen?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Hoe valideer ik wijzigingen in opslagquota?](#how-do-i-validate-storage-quota-changes)
- [Hoe kan ik verifiëren met mijn register bij het uitvoeren van de CLI in een container?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Hoe schakel je TLS 1.2 in?](#how-to-enable-tls-12)
- [Ondersteunt Azure Container Registry Content Trust?](#does-azure-container-registry-support-content-trust)
- [Hoe geef ik toegang tot het trekken of pushen van afbeeldingen zonder toestemming om de registerbron te beheren?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Hoe schakel ik automatische afbeeldingsquarantaine in voor een register](#how-do-i-enable-automatic-image-quarantine-for-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Hoe krijg ik toegang tot Docker Registry HTTP API V2?

ACR ondersteunt Docker Registry HTTP API V2. De API's zijn `https://<your registry login server>/v2/`toegankelijk op . Voorbeeld: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Hoe verwijder ik alle manifesten waarnaar niet wordt verwezen door een tag in een repository?

Als je op bash:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Voor Powershell:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Opmerking: u `-y` kunt de opdracht verwijderen toevoegen om de bevestiging over te slaan.

Zie [Containerafbeeldingen verwijderen in Azure Container Registry](container-registry-delete.md)voor meer informatie.

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Waarom vermindert het gebruik van het registerquotum niet na het verwijderen van afbeeldingen?

Deze situatie kan optreden als de onderliggende lagen nog steeds worden verwezen door andere containerafbeeldingen. Als u een afbeelding zonder verwijzingen verwijdert, wordt het registergebruik binnen enkele minuten bijgewerkt.

### <a name="how-do-i-validate-storage-quota-changes"></a>Hoe valideer ik wijzigingen in opslagquota?

Maak een afbeelding met een laag van 1 GB met het volgende dockerbestand. Dit zorgt ervoor dat de afbeelding een laag heeft die niet wordt gedeeld door een andere afbeelding in het register.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Bouw en duw de afbeelding naar uw register met behulp van de docker CLI.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

U moet kunnen zien dat het opslaggebruik is toegenomen in de Azure-portal, of u het gebruik opvragen met de CLI.

```azurecli
az acr show-usage -n myregistry
```

Verwijder de afbeelding met behulp van de Azure CLI of portal en controleer het bijgewerkte gebruik in een paar minuten.

```azurecli
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Hoe kan ik verifiëren met mijn register bij het uitvoeren van de CLI in een container?

U moet de Azure CLI-container uitvoeren door de Docker-socket te monteren:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

Installeer in `docker`de container :

```bash
apk --update add docker
```

Verifieer vervolgens met uw register:

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>Hoe schakel je TLS 1.2 in?

Schakel TLS 1.2 in met behulp van een recente dockerclient (versie 18.03.0 en hoger). 

> [!IMPORTANT]
> Vanaf 13 januari 2020 vereist Azure Container Registry dat alle beveiligde verbindingen van servers en toepassingen TLS 1.2 gebruiken. Ondersteuning voor TLS 1.0 en 1.1 wordt buiten gebruik gesteld.

### <a name="does-azure-container-registry-support-content-trust"></a>Ondersteunt Azure Container Registry Content Trust?

Ja, u vertrouwde afbeeldingen gebruiken in Azure Container Registry, omdat de [Docker-notaris](https://docs.docker.com/notary/getting_started/) is geïntegreerd en kan worden ingeschakeld. Zie [Contenttrust in Azure Container Registry](container-registry-content-trust.md)voor meer informatie.


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Waar bevindt het bestand voor de duimafdruk zich?

Onder `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`:

* Openbare sleutels en certificaten van alle rollen (behalve `root.json`delegatierollen) worden opgeslagen in de .
* Openbare sleutels en certificaten van de delegatierol worden opgeslagen in het `targets.json` JSON-bestand van de bovenliggende rol (bijvoorbeeld voor de `targets/releases` rol).

Voorgesteld wordt om deze openbare sleutels en certificaten te verifiëren na de algehele TUF-verificatie door de Docker- en notarisclient.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Hoe geef ik toegang tot het trekken of pushen van afbeeldingen zonder toestemming om de registerbron te beheren?

ACR ondersteunt [aangepaste rollen](container-registry-roles.md) die verschillende niveaus van machtigingen bieden. Met `AcrPull` name `AcrPush` en rollen stellen gebruikers in staat om afbeeldingen te trekken en/of te pushen zonder toestemming om de registerbron in Azure te beheren.

* Azure-portal: uw register -> Access Control (IAM) -> toevoegen (Selecteren `AcrPull` of `AcrPush` voor de rol).
* Azure CLI: zoek de bron-id van het register door de volgende opdracht uit te voeren:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Vervolgens u `AcrPull` `AcrPush` de functie of rol toewijzen `AcrPull`aan een gebruiker (in het volgende voorbeeld wordt gebruik gemaakt van):

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
  ```

  Wijs de rol ook toe aan een servicebeginsel dat is vastgesteld door de toepassings-id:

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

De cessionaris kan vervolgens afbeeldingen in het register verifiëren en openen.

* Verificatie naar een register:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Ga als u repositories wilt aanbieden:

  ```azurecli
  az acr repository list -n myRegistry
  ```

* Een afbeelding trekken:

  ```bash
  docker pull myregistry.azurecr.io/hello-world
  ```

Met het gebruik `AcrPull` van `AcrPush` alleen de of rol heeft de cessionaris niet de toestemming om de registerbron in Azure te beheren. Bijvoorbeeld, `az acr list` of `az acr show -n myRegistry` zal niet het register weer geven.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Hoe schakel ik automatische afbeeldingsquarantaine in voor een register?

Afbeeldingsquarantaine is momenteel een voorbeeldfunctie van ACR. U de quarantainemodus van een register inschakelen, zodat alleen de afbeeldingen die de beveiligingsscan hebben doorstaan, zichtbaar zijn voor normale gebruikers. Zie voor meer informatie de [ACR GitHub repo.](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)

## <a name="diagnostics-and-health-checks"></a>Diagnostiek en gezondheidscontroles

- [Controleer de gezondheid met`az acr check-health`](#check-health-with-az-acr-check-health)
- [docker pull mislukt met fout: net/http: aanvraag geannuleerd tijdens het wachten op verbinding (Client.Timeout overschreden tijdens het wachten headers)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [docker push slaagt, maar docker pull mislukt met fout: ongeautoriseerd: authenticatie vereist](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login`slaagt, maar docker commando's mislukt met fout: ongeautoriseerd: authenticatie vereist](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [Schakel de foutopsporingslogboeken van de docker daemon in en ontvang deze](#enable-and-get-the-debug-logs-of-the-docker-daemon) 
- [Nieuwe gebruikersmachtigingen zijn mogelijk niet onmiddellijk na het bijwerken van kracht](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [Verificatiegegevens worden niet in de juiste indeling gegeven voor directe REST API-aanroepen](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Waarom worden in de Azure-portal niet al mijn repositories of tags vermeld?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Waarom haalt de Azure-portal geen opslagplaatsen of tags op?](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [Waarom mislukt mijn aanvraag voor het trekken of pushen met een niet-toegestane bewerking?](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [Hoe verzamel ik http-sporen in Windows?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>Controleer de gezondheid met`az acr check-health`

Zie [De status van een Azure-containerregister controleren](container-registry-check-health.md)als u veelvoorkomende omgevings- en registerproblemen wilt oplossen.

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>docker pull mislukt met fout: net/http: aanvraag geannuleerd tijdens het wachten op verbinding (Client.Timeout overschreden tijdens het wachten headers)

 - Als deze fout een tijdelijk probleem is, wordt opnieuw geprobeerd.
 - Als `docker pull` het continu mislukt, kan er een probleem zijn met de Docker daemon. Het probleem kan over het algemeen worden verholpen door de Docker daemon opnieuw op te starten. 
 - Als u dit probleem blijft zien nadat u Docker daemon opnieuw hebt opgestart, kan het probleem problemen met de netwerkconnectiviteit met de machine zijn. Als u wilt controleren of het algemene netwerk op de machine in orde is, voert u de volgende opdracht uit om de endpoint-connectiviteit te testen. De `az acr` minimale versie die deze opdracht voor verbindingscontrole bevat, is 2.2.9. Upgrade uw Azure CLI als u een oudere versie gebruikt.
 
  ```azurecli
  az acr check-health -n myRegistry
  ```

 - U moet altijd een mechanisme voor nieuwe try hebben voor alle Docker-clientbewerkingen.

### <a name="docker-pull-is-slow"></a>Docker pull is traag
Gebruik [deze](http://www.azurespeed.com/Azure/Download) tool om de downloadsnelheid van uw machinenetwerk te testen. Als het machinenetwerk traag is, u Azure VM gebruiken in dezelfde regio als uw register. Dit geeft u meestal een snellere netwerksnelheid.

### <a name="docker-push-is-slow"></a>Docker push is traag
Gebruik [deze](http://www.azurespeed.com/Azure/Upload) tool om de uploadsnelheid van uw machinenetwerk te testen. Als het machinenetwerk traag is, u Azure VM gebruiken in dezelfde regio als uw register. Dit geeft u meestal een snellere netwerksnelheid.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>Docker push slaagt, maar docker pull mislukt met fout: ongeautoriseerd: authenticatie vereist

Deze fout kan gebeuren met de Red Hat-versie van de Docker-daemon, waar `--signature-verification` standaard is ingeschakeld. U de Docker daemon-opties voor Red Hat Enterprise Linux (RHEL) of Fedora controleren door de volgende opdracht uit te voeren:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Fedora 28 Server heeft bijvoorbeeld de volgende opties voor docker daemon:

`OPTIONS='--selinux-enabled --log-driver=journald --live-restore'`

Met `--signature-verification=false` ontbrekende, `docker pull` mislukt met een fout vergelijkbaar met:

```output
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Ga als een besluit over de fout:
1. Voeg de `--signature-verification=false` optie toe aan `/etc/sysconfig/docker`het configuratiebestand van de Docker-daemon . Bijvoorbeeld:
   
   `OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'`
   
2. Start de Docker daemon-service opnieuw door de volgende opdracht uit te voeren:
   
   ```bash
   sudo systemctl restart docker.service
   ```

Details `--signature-verification` van kan worden `man dockerd`gevonden door het uitvoeren van .

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>az acr login slaagt, maar docker mislukt met fout: onbevoegd: authenticatie vereist

Zorg ervoor dat u bijvoorbeeld een URL `docker push myregistry.azurecr.io/myimage:latest`van de all lowercaseserver gebruikt, zelfs `myRegistry`als de naam van de registerbron hoofdletters of gemengde letters is, zoals .

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Schakel de foutopsporingslogboeken van de Docker daemon in en ontvang deze  

Begin `dockerd` met `debug` de optie. Maak eerst het configuratiebestand docker daemon ( )`/etc/docker/daemon.json`als het `debug` niet bestaat en voeg de optie toe:

```json
{   
    "debug": true   
}
```

Start vervolgens de daemon opnieuw. Bijvoorbeeld met Ubuntu 14.04:

```bash
sudo service docker restart
```

Details zijn te vinden in de [Docker documentatie.](https://docs.docker.com/engine/admin/#enable-debugging) 

 * De logboeken kunnen worden gegenereerd op verschillende locaties, afhankelijk van uw systeem. Bijvoorbeeld, voor Ubuntu 14.04, `/var/log/upstart/docker.log`het is .   
Zie [Docker-documentatie](https://docs.docker.com/engine/admin/#read-the-logs) voor meer informatie.    

 * Voor Docker voor Windows worden de logboeken gegenereerd onder %LOCALAPPDATA%/docker/. Het kan echter niet alle debug informatie nog niet bevatten.   

   Om toegang te krijgen tot het volledige daemon-logboek, heb je mogelijk wat extra stappen nodig:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Nu heb je toegang tot alle `dockerd`bestanden van de VM met draaien . Het logboek `/var/log/docker.log`is op .

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Nieuwe gebruikersmachtigingen zijn mogelijk niet onmiddellijk na het bijwerken van kracht

Wanneer u nieuwe machtigingen (nieuwe rollen) verleent aan een serviceprincipal, wordt de wijziging mogelijk niet onmiddellijk van kracht. Er zijn twee mogelijke redenen:

* Vertraging in Azure Active Directory-roltoewijzing. Normaal gesproken is het snel, maar het kan minuten duren als gevolg van voortplanting vertraging.
* Toestemmingsvertraging op ACR-tokenserver. Dit kan tot 10 minuten duren. Om dit te `docker logout` beperken, u na 1 minuut opnieuw verifiëren met dezelfde gebruiker:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

Momenteel ondersteunt ACR geen verwijdering van thuisreplicatie door de gebruikers. De tijdelijke oplossing moet de het maken van de huisreplicatie in de sjabloon opnemen, maar de creatie ervan overslaan door toe te voegen `"condition": false` zoals hieronder wordt weergegeven:

```json
{
    "name": "[concat(parameters('acrName'), '/', parameters('location'))]",
    "condition": false,
    "type": "Microsoft.ContainerRegistry/registries/replications",
    "apiVersion": "2017-10-01",
    "location": "[parameters('location')]",
    "properties": {},
    "dependsOn": [
        "[concat('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
     ]
},
```

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>Verificatiegegevens worden niet in de juiste indeling gegeven voor directe REST API-aanroepen

U `InvalidAuthenticationInfo` een fout tegenkomen, vooral met behulp van het `curl` gereedschap met de optie `-L`(om `--location` omleidingen te volgen).
Bijvoorbeeld het ophalen van `curl` de `-L` blob met optie en basisverificatie:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

kan tot het volgende antwoord leiden:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

De hoofdoorzaak is `curl` dat sommige implementaties omleidingen volgen met headers van de oorspronkelijke aanvraag.

Om het probleem op te lossen, moet u omleidingen handmatig volgen zonder de headers. Druk de antwoordkoppen `-D -` af `curl` met de optie `Location` van en haal vervolgens uit: de koptekst:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Waarom worden in de Azure-portal niet al mijn repositories of tags vermeld? 

Als u de Microsoft Edge/IE-browser gebruikt, u hoogstens 100 repositories of tags zien. Als uw register meer dan 100 opslagplaatsen of tags bevat, raden we u aan de Firefox- of Chrome-browser te gebruiken om ze allemaal op te sommen.

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>Waarom haalt de Azure-portal geen opslagplaatsen of tags op?

De browser kan het verzoek om opslagplaatsen of tags ophalen mogelijk niet naar de server verzenden. Er kunnen verschillende redenen zijn, zoals:

* Gebrek aan netwerkconnectiviteit
* Firewall
* Adblockers
* DNS-fouten

Neem contact op met uw netwerkbeheerder of controleer uw netwerkconfiguratie en -connectiviteit. Probeer `az acr check-health -n yourRegistry` het uitvoeren van uw Azure CLI om te controleren of uw omgeving verbinding kan maken met het containerregister. Bovendien u ook een incognito- of privésessie in uw browser proberen om verouderde browsercache of cookies te voorkomen.

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>Waarom mislukt mijn aanvraag voor het trekken of pushen met een niet-toegestane bewerking?

Hier zijn enkele scenario's waarin bewerkingen misschien niet toegestaan:
* Klassieke registers worden niet langer ondersteund. Upgrade naar een ondersteunde [SKU's](https://aka.ms/acr/skus) met [az acr-update](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-update) of de Azure-portal.
* De afbeelding of repository misschien vergrendeld, zodat het niet kan worden verwijderd of bijgewerkt. U de opdracht [az acr show repository](https://docs.microsoft.com/azure/container-registry/container-registry-image-lock) gebruiken om de huidige kenmerken weer te geven.
* Sommige bewerkingen zijn niet toegestaan als de afbeelding in quarantaine staat. Meer informatie over [quarantaine](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

### <a name="how-do-i-collect-http-traces-on-windows"></a>Hoe verzamel ik http-sporen in Windows?

#### <a name="prerequisites"></a>Vereisten

- Schakel het ontsleutelen van https in fiddler in:<https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Docker inschakelen om een proxy te gebruiken via de Docker ui:<https://docs.docker.com/docker-for-windows/#proxies>
- Zorg ervoor dat u terugkeert wanneer u klaar bent.  Docker werkt niet met deze ingeschakeld en fiddler niet draait.

#### <a name="windows-containers"></a>Windows-containers

Docker-proxy configureren naar 127.0.0.1:8888

#### <a name="linux-containers"></a>Linux-containers

Zoek het ip van de virtuele docker vm-schakelaar:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

De dockerproxy configureren op uitvoer van de vorige opdracht en de poort 8888 (bijvoorbeeld 10.0.75.1:8888)

## <a name="tasks"></a>Taken

- [Hoe kan ik batch-runs annuleren?](#how-do-i-batch-cancel-runs)
- [Hoe voeg ik de .git-map toe aan de opdracht AZ ACR Build?](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [Ondersteunt Taken GitLab voor Brontriggers?](#does-tasks-support-gitlab-for-source-triggers)
- [Welke git repository management service ondersteunt Tasks?](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>Hoe kan ik batch-runs annuleren?

Met de volgende opdrachten worden alle actieve taken in het opgegeven register geannuleerd.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Hoe voeg ik de .git-map toe aan de opdracht AZ ACR Build?

Als u een lokale bronmap doorgeeft aan de `az acr build` opdracht, wordt de `.git` map standaard uitgesloten van het geüploade pakket. U `.dockerignore` een bestand maken met de volgende instelling. Het vertelt de opdracht om `.git` alle bestanden onder in het geüploade pakket te herstellen. 

`!.git/**`

Deze instelling is `az acr run` ook van toepassing op de opdracht.

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>Ondersteunt Taken GitLab voor Brontriggers?

We ondersteunen momenteel geen GitLab voor Brontriggers.

### <a name="what-git-repository-management-service-does-tasks-support"></a>Welke git repository management service ondersteunt Tasks?

| Git-service | Broncontext | Handmatig bouwen | Automatisch bouwen via commit trigger |
|---|---|---|---|
| GitHub | https://github.com/user/myapp-repo.git#mybranch:myfolder | Ja | Ja |
| Azure-opslagplaatsen | https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder | Ja | Ja |
| GitLab | https://gitlab.com/user/myapp-repo.git#mybranch:myfolder | Ja | Nee |
| BitBucket | https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder | Ja | Nee |

## <a name="run-error-message-troubleshooting"></a>Foutoplossing voor foutberichten uitvoeren

| Foutbericht | Handleiding voor het oplossen van problemen |
|---|---|
|Er is geen toegang geconfigureerd voor de VM, vandaar dat er geen abonnementen zijn gevonden|Dit kan gebeuren als `az login --identity` u in uw ACR-taak gebruikt. Dit is een tijdelijke fout en treedt op wanneer de roltoewijzing van uw beheerde identiteit niet is doorgegeven. Een paar seconden wachten voordat het opnieuw proberen werkt.|

## <a name="cicd-integration"></a>CI/CD-integratie

- [CircleCI (CircleCI)](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub-acties](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie](container-registry-intro.md) over Azure Container Registry.
