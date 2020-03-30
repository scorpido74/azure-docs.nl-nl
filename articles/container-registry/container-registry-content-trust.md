---
title: Ondertekende afbeeldingen beheren
description: Meer informatie over het inschakelen van contentvertrouwensrelatie voor uw Azure-containerregister en het pushen en trekken van ondertekende afbeeldingen.
ms.topic: article
ms.date: 09/06/2019
ms.openlocfilehash: ce1e9e5cce0de58703e69df8db14cfbf3ecf04f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78249929"
---
# <a name="content-trust-in-azure-container-registry"></a>Inhoud vertrouwen in Azure Container Registry

Azure Container Registry implementeert het [content trustmodel][docker-content-trust] van Docker, waardoor ondertekende afbeeldingen kunnen worden gepusht en getrokken. In dit artikel u aan de slag met het inschakelen van inhoudsvertrouwen in uw containerregisters.

> [!NOTE]
> Contentvertrouwensrelatie is een functie van de [Premium SKU](container-registry-skus.md) van Azure Container Registry.

## <a name="how-content-trust-works"></a>Hoe inhoud vertrouwen werkt

Het is belangrijk voor de beveiliging van een gedistribueerd systeem dat zowel de *bron* als de *integriteit* van de gegevensinvoer in het systeem kan worden gecontroleerd. Consumenten van de gegevens moeten de uitgever (bron) van de gegevens kunnen verifiëren en kunnen nagaan of de gegevens niet zijn gewijzigd nadat ze zijn gepubliceerd (integriteit). 

Als uitgever van installatiekopieën kunt u inhoud vertrouwen gebruiken voor het **ondertekenen** van de installatiekopieën die u naar uw register pusht. Consumenten van uw installatiekopieën (personen of systemen die installatiekopieën ophalen uit het register) kunnen hun clients zo configureren dat *alleen* ondertekenende installatiekopieën worden opgehaald. Wanneer een consument een ondertekende installatiekopie ophaalt, wordt de integriteit van de installatiekopie door hun Docker-client gecontroleerd. Zo weten consumenten zeker dat de ondertekende installatiekopieën in uw register inderdaad door u zijn gepubliceerd, en dat ze sindsdien niet zijn gewijzigd.

### <a name="trusted-images"></a>Vertrouwde installatiekopieën

Inhoud vertrouwen werkt op basis van de **tags** in een opslagplaats. Installatiekopie-opslagplaatsen kunnen zowel installatiekopieën met ondertekende als niet-ondertekende tags bevatten. Mogelijk ondertekent u bijvoorbeeld alleen de installatiekopieën `myimage:stable` en `myimage:latest`, maar niet `myimage:dev`.

### <a name="signing-keys"></a>Ondertekeningssleutels

Inhoud vertrouwen wordt beheerd met behulp van cryptografische ondertekeningssleutels. Deze sleutels zijn gekoppeld aan een specifieke opslagplaats in een register. Er zijn verschillende soorten ondertekeningssleutels die door Docker-clients en het register worden gebruikt bij het beheren van vertrouwensrelaties voor de tags in een opslagplaats. Wanneer u inhoud vertrouwen inschakelt en integreert in de publicatie- en verbruikspijplijn van uw container, moet u deze sleutels zorgvuldig beheren. Zie voor meer informatie [Sleutelbeheer](#key-management) verderop in dit artikel en [Manage keys for content trust][docker-manage-keys] (sleutels voor inhoud vertrouwen beheren) in de documentatie voor Docker.

> [!TIP]
> Dit is een zeer algemeen overzicht van het inhoud vertrouwen-model van Docker. Zie [Content trust in Docker][docker-content-trust] (inhoud vertrouwen in Docker) voor een uitgebreide beschrijving van inhoud vertrouwen.

## <a name="enable-registry-content-trust"></a>Inhoud vertrouwen voor register inschakelen

Uw eerste stap is het inschakelen van inhoud vertrouwen op het niveau van het register. Na het inschakelen kunnen clients (gebruikers of services) ondertekende installatiekopieën pushen naar uw register. Als u inhoud vertrouwen voor uw register inschakelt, wordt het gebruik van het register niet beperkt tot consumenten waarvoor inhoud vertrouwen is ingeschakeld. Ook gebruikers waarvoor inhoud vertrouwen niet is ingeschakeld, kunnen uw register gewoon blijven gebruiken. Gebruikers die inhoud vertrouwen op hun clients hebben ingeschakeld, kunnen echter *alleen* ondertekende installatiekopieën in uw register zien.

Als u inhoud vertrouwen voor uw register wilt inschakelen, navigeert u eerst naar het register in Azure Portal. Selecteer **onder Beleid**de optie **Inhoudsvertrouwensrelatie** > **ingeschakeld** > **opslaan**. U ook de opdracht [voor content-trust-update van AZ acr config][az-acr-config-content-trust-update] gebruiken in de Azure CLI.

![Inhoud vertrouwen voor een register inschakelen in Azure Portal][content-trust-01-portal]

## <a name="enable-client-content-trust"></a>Inhoud vertrouwen voor clients inschakelen

Als u wilt werken met vertrouwde installatiekopieën, moeten zowel uitgevers als consumenten van installatiekopieën inhoud vertrouwen voor de Docker-clients inschakelen. Als uitgever kunt u de installatiekopieën die u pusht naar een register waarvoor inhoud vertrouwen is ingeschakeld, ondertekenen. Wanneer u als consument inhoud vertrouwen inschakelt, ziet u in registers alleen nog ondertekende installatiekopieën. Inhoud vertrouwen is standaard uitgeschakeld voor Docker-clients, maar u kunt dit per shellsessie of opdracht inschakelen.

Als u inhoud vertrouwen voor een shellsessie wilt inschakelen, stelt u de omgevingsvariabele `DOCKER_CONTENT_TRUST` in op **1**. Voorbeeld in de Bash-shell:

```bash
# Enable content trust for shell session
export DOCKER_CONTENT_TRUST=1
```

Als u in plaats daarvan inhoud vertrouwen voor een opdracht wilt uitschakelen, gebruikt u een van de ondersteunde Docker-opdrachten met het argument `--disable-content-trust`. Inhoud vertrouwen uitschakelen voor één opdracht:

```bash
# Enable content trust for single command
docker build --disable-content-trust=false -t myacr.azurecr.io/myimage:v1 .
```

Als u inhoud vertrouwen voor uw shellsessie hebt ingeschakeld en het wilt uitschakelen voor één opdracht, doet u het volgende:

```bash
# Disable content trust for single command
docker build --disable-content-trust -t myacr.azurecr.io/myimage:v1 .
```

## <a name="grant-image-signing-permissions"></a>Verleen machtigingen voor het ondertekenen van installatiekopieën

Alleen de gebruikers of systemen die u daartoe hebt gemachtigd, kunnen vertrouwde installatiekopieën naar uw register pushen. Als u een gebruiker (of een systeem met een service-principal) machtigingen voor het pushen van vertrouwde installatiekopieën wilt verlenen, verleent u de rol `AcrImageSigner` aan hun Azure Active Directory-entiteiten. Dit is in `AcrPush` aanvulling op de (of gelijkwaardige) rol die nodig is voor het duwen van afbeeldingen naar het register. Zie Azure [Container Registry-rollen en machtigingen](container-registry-roles.md)voor meer informatie.

> [!NOTE]
> U geen toestemming verlenen voor het [push-overzicht](container-registry-authentication.md#admin-account) van vertrouwde afbeeldingen aan het beheerdersaccount van een Azure-containerregister.

Detailinformatie over het verlenen van de rol `AcrImageSigner` in Azure Portal en de Azure-opdrachtregelinterface volgt later.

### <a name="azure-portal"></a>Azure Portal

Navigeer naar uw register in de Azure-portal en selecteer **Vervolgens Toegangsbeheer (IAM)** > **Roltoewijzing toevoegen**. Selecteer onder **Roltoewijzing toevoegen** de rol `AcrImageSigner` onder **Rol**. **Selecteer** vervolgens een of meer gebruikers of service-principals en klik op **Opslaan**.

In dit voorbeeld is de rol `AcrImageSigner` toegewezen aan twee entiteiten: een service-principal met de naam 'service-principal' en een gebruiker met de naam 'Azure User'.

![Inhoud vertrouwen voor een register inschakelen in Azure Portal][content-trust-02-portal]

### <a name="azure-cli"></a>Azure-CLI

Als u een gebruiker machtigingen voor ondertekenen wilt verlenen met de Azure-opdrachtregelinterface, wijst u de rol `AcrImageSigner` toe aan de gebruiker voor uw register. De indeling van de opdracht is als volgt:

```azurecli
az role assignment create --scope <registry ID> --role AcrImageSigner --assignee <user name>
```

Als u de rol bijvoorbeeld aan uzelf wilt verlenen, kunt u de volgende opdrachten uitvoeren in een geverifieerde Azure CLI-sessie. Wijzig de waarde `REGISTRY` in de naam van uw Azure-containerregister.

```bash
# Grant signing permissions to authenticated Azure CLI user
REGISTRY=myregistry
USER=$(az account show --query user.name --output tsv)
REGISTRY_ID=$(az acr show --name $REGISTRY --query id --output tsv)
```

```azurecli
az role assignment create --scope $REGISTRY_ID --role AcrImageSigner --assignee $USER
```

U kunt de rechten voor het pushen van vertrouwde installatiekopieën naar uw register ook verlenen aan een [service-principal](container-registry-auth-service-principal.md). Het gebruik van een service-principal is handig voor het bouwen van systemen en andere systemen zonder toezicht waarmee vertrouwde installatiekopieën naar uw register moeten worden gepusht. De indeling is vergelijkbaar met het verlenen van gebruikersmachtigingen, maar in dit geval moet u de waarde `--assignee` wijzigen in de id van een service-principal.

```azurecli
az role assignment create --scope $REGISTRY_ID --role AcrImageSigner --assignee <service principal ID>
```

De `<service principal ID>` kan de **appId**, **objectId** of een van de bijbehorende **servicePrincipalNames** van de service-principal zijn. Zie [Azure Container Registry authentication with service principals](container-registry-auth-service-principal.md) (Azure Container Registry-verificatie met service-principals) voor meer informatie over het werken met service-principals en Azure Container Registry.

> [!IMPORTANT]
> Voer na alle `az acr login` functiewijzigingen het lokale identiteitstoken voor de Azure CLI uit, zodat de nieuwe rollen van kracht kunnen worden. Zie [Toegang tot Azure-resources beheren met RBAC en Azure CLI en](../role-based-access-control/role-assignments-cli.md) Problemen met [RBAC voor Azure-resources voor](../role-based-access-control/troubleshooting.md)informatie over het verifiëren van rollen voor een identiteit.

## <a name="push-a-trusted-image"></a>Een vertrouwde installatiekopie pushen

Als u een tag voor een vertrouwde installatiekopie naar uw containerregister wilt pushen, schakelt u inhoud vertrouwen in en pusht u de installatiekopie naar uw register met `docker push`. De eerste keer dat u een ondertekende tag pusht, wordt u gevraagd een wachtwoordzin te maken voor zowel de hoofdondertekeningssleutel als de ondertekeningssleutel voor de opslagplaats. Beide sleutels worden gegenereerd en lokaal opgeslagen op uw computer.

```console
$ docker push myregistry.azurecr.io/myimage:v1
The push refers to repository [myregistry.azurecr.io/myimage]
ee83fc5847cb: Pushed
v1: digest: sha256:aca41a608e5eb015f1ec6755f490f3be26b48010b178e78c00eac21ffbe246f1 size: 524
Signing and pushing trust metadata
You are about to create a new root signing key passphrase. This passphrase
will be used to protect the most sensitive key in your signing system. Please
choose a long, complex passphrase and be careful to keep the password and the
key file itself secure and backed up. It is highly recommended that you use a
password manager to generate the passphrase and keep it safe. There will be no
way to recover this key. You can find the key in your config directory.
Enter passphrase for new root key with ID 4c6c56a:
Repeat passphrase for new root key with ID 4c6c56a:
Enter passphrase for new repository key with ID bcd6d98:
Repeat passphrase for new repository key with ID bcd6d98:
Finished initializing "myregistry.azurecr.io/myimage"
Successfully signed myregistry.azurecr.io/myimage:v1
```

Na uw eerste `docker push` waarvoor inhoud vertrouwen is ingeschakeld, wordt voor volgende pushes op de Docker-client dezelfde hoofdsleutel gebruikt. Bij elke volgende push naar dezelfde opslagplaats hoeft u alleen de sleutel voor de opslagplaats op te geven. Telkens wanneer u een vertrouwde installatiekopie naar een nieuwe opslagplaats pusht, wordt u gevraagd een wachtwoordzin op te geven voor een nieuwe opslagplaatssleutel.

## <a name="pull-a-trusted-image"></a>Een vertrouwde installatiekopie ophalen

Als u een vertrouwde installatiekopie wilt ophalen, schakelt u inhoud vertrouwen in en voert u de opdracht `docker pull` uit zoals normaal. Om vertrouwde afbeeldingen `AcrPull` te trekken, is de rol voldoende voor normale gebruikers. Er zijn geen `AcrImageSigner` extra rollen zoals een rol vereist. Consumenten waarvoor inhoud vertrouwen is ingeschakeld kunnen alleen installatiekopieën met ondertekende tags ophalen. Voorbeeld van het ophalen van een ondertekende tag:

```console
$ docker pull myregistry.azurecr.io/myimage:signed
Pull (1 of 1): myregistry.azurecr.io/myimage:signed@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b: Pulling from myimage
8e3ba11ec2a2: Pull complete
Digest: sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
Status: Downloaded newer image for myregistry.azurecr.io/myimage@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
Tagging myregistry.azurecr.io/myimage@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b as myregistry.azurecr.io/myimage:signed
```

Als door een client waarvoor inhoud vertrouwen is ingeschakeld, wordt geprobeerd een niet-ondertekende tag op te halen, mislukt de bewerking:

```console
$ docker pull myregistry.azurecr.io/myimage:unsigned
No valid trust data for unsigned
```

### <a name="behind-the-scenes"></a>Achter de schermen

Bij het uitvoeren van `docker pull` maakt de Docker-client gebruik van dezelfde bibliotheek als in de [Notary-opdrachtregelinterface][docker-notary-cli] om de tag-to-SHA-256-digest-toewijzing aan te vragen voor de tag die u wilt ophalen. Na het valideren van de handtekeningen voor de gegevens van de vertrouwensrelatie, laat de client de Docker-engine een 'pull by digest'-opdracht uitvoeren. Tijdens de pull-bewerking wordt de SHA-256-controlesom door de engine gebruikt als inhoudadres voor het aanvragen en valideren van het installatiekopie-manifest van het Azure-containerregister.

## <a name="key-management"></a>Sleutelbeheer

Zoals vermeld in de uitvoer van `docker push`, is de hoofdsleutel uiterst belangrijk bij het pushen van uw eerste vertrouwde installatiekopie. Maak een back-up van uw hoofdsleutel en bewaar deze op een veilige locatie. Standaard worden ondertekeningssleutels opgeslagen in de volgende map van de Docker-client:

```sh
~/.docker/trust/private
```

Een back-up van uw hoofd- en archiefsleutels door ze te comprimeren in een archief en deze op een veilige locatie op te slaan. Bijvoorbeeld, in Bash:

```bash
umask 077; tar -zcvf docker_private_keys_backup.tar.gz ~/.docker/trust/private; umask 022
```

Wanneer u een vertrouwde installatiekopie pusht, worden - naast de lokaal gegenereerde hoofd- en opslagplaatssleutels - door Azure Container Registry nog enkele andere sleutels gegenereerd en opgeslagen. Zie [Manage keys for content trust][docker-manage-keys] (sleutels voor inhoud vertrouwen beheren) in de documentatie voor Docker voor een uitgebreide beschrijving van de verschillende sleutels in de implementatie van inhoud vertrouwen van Docker, inclusief aanvullende richtlijnen voor het beheer.

### <a name="lost-root-key"></a>Verloren hoofdsleutel

Als de hoofdsleutel verloren gaat, hebt u geen toegang meer tot de ondertekende tags in alle opslagplaatsen waarvan de tags zijn ondertekend met die sleutel. De toegang tot tags voor installatiekopieën die zijn ondertekend met een verloren hoofdsleutel, kan in Azure Container Registry niet worden hersteld. Om alle gegevens van de vertrouwensrelatie (handtekeningen) voor het register te verwijderen, moet u inhoud vertrouwen voor het register uitschakelen en vervolgens weer inschakelen.

> [!WARNING]
> Door inhoud vertrouwen voor het register uit te schakelen en opnieuw in te schakelen, **verwijdert u alle gegevens van de vertrouwensrelatie voor alle ondertekende tags in elke opslagplaats in uw register**. Deze actie kan niet ongedaan worden gemaakt. Gegevens van vertrouwensrelaties kunnen nooit worden hersteld in Azure Container Registry. Bij het uitschakelen van inhoud vertrouwen worden de installatiekopieën zelf niet verwijderd.

Als u inhoud vertrouwen voor uw register wilt uitschakelen, navigeert u naar het register in Azure Portal. Selecteer **onder Beleid**de optie **Inhoudsvertrouwensrelatie** > **Die is uitgeschakeld** > **Save**. U wordt gewaarschuwd dat hierdoor alle handtekeningen in het register verloren gaan. Selecteer **OK** om alle handtekeningen in het register permanent te verwijderen.

![Inhoud vertrouwen voor een register uitschakelen in Azure Portal][content-trust-03-portal]

## <a name="next-steps"></a>Volgende stappen

* Zie [Inhoudsvertrouwen in Docker][docker-content-trust] voor meer informatie over het vertrouwen in inhoud. In dit artikel zijn slechts de belangrijkste punten van inhoud vertrouwen behandeld. Een uitgebreide beschrijving van dit onderwerp vindt u in de documentatie voor Docker.

* Zie de Azure [Pipelines-documentatie](/azure/devops/pipelines/build/content-trust) voor een voorbeeld van het gebruik van inhoudsvertrouwensrelatie wanneer u een Docker-afbeelding bouwt en pusht.

<!-- IMAGES> -->
[content-trust-01-portal]: ./media/container-registry-content-trust/content-trust-01-portal.png
[content-trust-02-portal]: ./media/container-registry-content-trust/content-trust-02-portal.png
[content-trust-03-portal]: ./media/container-registry-content-trust/content-trust-03-portal.png

<!-- LINKS - external -->
[docker-content-trust]: https://docs.docker.com/engine/security/trust/content_trust
[docker-manage-keys]: https://docs.docker.com/engine/security/trust/trust_key_mng/
[docker-notary-cli]: https://docs.docker.com/notary/getting_started/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-content-trust-update]: /cli/azure/acr/config/content-trust#az-acr-config-content-trust-update
