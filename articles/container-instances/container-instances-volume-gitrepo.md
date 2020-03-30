---
title: GitRepo-volume monteren op containergroep
description: Meer informatie over het monteren van een gitRepo-volume om een Git-opslagplaats in uw containerexemplaren te klonen
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 405cacd7a1649f95640a8dabf476729e101d03f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252087"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Een gitRepo-volume monteren in Azure Container Instances

Meer informatie over het monteren van een *gitRepo-volume* om een Git-opslagplaats in uw containerexemplaren te klonen.

> [!NOTE]
> Het monteren van een *gitRepo* volume is momenteel beperkt tot Linux containers. Terwijl we werken aan het brengen van alle functies naar Windows-containers, u actuele platformverschillen vinden in het [overzicht.](container-instances-overview.md#linux-and-windows-containers)

## <a name="gitrepo-volume"></a>gitRepo volume

Het *gitRepo-volume* monteert een map en kloont de opgegeven Git-opslagplaats erin bij het opstarten van containers. Door een *gitRepo-volume* in uw containerexemplaren te gebruiken, u voorkomen dat de code hiervoor in uw toepassingen wordt toegevoegd.

Wanneer u een *gitRepo-volume* monteert, u drie eigenschappen instellen om het volume te configureren:

| Eigenschap | Vereist | Beschrijving |
| -------- | -------- | ----------- |
| `repository` | Ja | De volledige URL, inclusief `http://` of `https://`, van de Git repository te gekloond.|
| `directory` | Nee | Directory waarin de repository moet worden gekloond. Het pad mag niet bevatten`..`of beginnen met " ".  Als u`.`" opgeeft ", wordt de opslagplaats gekloond in de map van het volume. Anders wordt de Git-repository gekloond in een submap van de voornaam in de volumemap. |
| `revision` | Nee | De commit hash van de revisie die moet worden gekloond. Als deze niet `HEAD` is opgegeven, wordt de revisie gekloond. |

## <a name="mount-gitrepo-volume-azure-cli"></a>GitRepo-volume monteren: Azure CLI

Als u een gitRepo-volume wilt monteren wanneer u `--gitrepo-url` `--gitrepo-mount-path` containerexemplaren implementeert met de [Azure CLI,](/cli/azure)levert u de parameters en parameters aan de opdracht [az-containermaken.][az-container-create] U optioneel de map opgeven binnen`--gitrepo-dir`het volume dat u wilt klonen in`--gitrepo-revision`( ) en de commit hash van de te klonen revisie ( ).

In dit voorbeeld wordt de voorbeeldtoepassing van `/mnt/aci-helloworld` Microsoft [aci-helloworld][aci-helloworld] in de containerinstantie gekert:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name hellogitrepo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --dns-name-label aci-demo \
    --ports 80 \
    --gitrepo-url https://github.com/Azure-Samples/aci-helloworld \
    --gitrepo-mount-path /mnt/aci-helloworld
```

Om te controleren of het gitRepo-volume is gemonteerd, start u een shell in de container met [az-containerexec][az-container-exec] en vermeldt u de map:

```azurecli
az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
```

```output
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>GitRepo-volume monteren: Resource Manager

Als u een gitRepo-volume wilt monteren wanneer u containerexemplaren `volumes` implementeert met `properties` een Azure [Resource Manager-sjabloon,](/azure/templates/microsoft.containerinstance/containergroups)vult u eerst de array in het gedeelte containergroep van de sjabloon. Vul vervolgens voor elke container in de containergroep waarin u het *gitRepo-volume* wilt monteren, de `volumeMounts` array in het `properties` gedeelte van de containerdefinitie.

Met de volgende sjabloon Resourcemanager wordt bijvoorbeeld een containergroep gemaakt die bestaat uit één container. De container kloont twee GitHub-repositories die zijn opgegeven door de *gitRepo-volumeblokken.* Het tweede deel bevat extra eigenschappen die een map opgeven om naar te klonen en de commit hash van een specifieke revisie om te klonen.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

De resulterende mapstructuur van de twee gekloonde repo's die in de voorgaande sjabloon zijn gedefinieerd, is:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Zie [Meerdere containergroepen implementeren in Azure Container Instances](container-instances-multi-container-group.md)als u een voorbeeld wilt zien van de implementatie van containerexemplaren met een Azure Resource Manager-sjabloon.

## <a name="private-git-repo-authentication"></a>Privé Git repo-verificatie

Als u een gitRepo-volume wilt monteren voor een privé-Git-opslagplaats, geeft u referenties op in de URL van de opslagplaats. Referenties zijn meestal in de vorm van een gebruikersnaam en een persoonlijk toegangstoken (PAT) dat scoped toegang tot de repository verleent.

De parameter Azure `--gitrepo-url` CLI voor een private GitHub-repository lijkt bijvoorbeeld op de volgende (waarbij 'gituser' de GitHub-gebruikersnaam is en 'abcdef1234fdsa4321abcdef' het persoonlijke toegangstoken van de gebruiker):

```console
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

Geef voor een Azure Repos Git-repository elke gebruikersnaam op (u 'azurereposuser' gebruiken zoals in het volgende voorbeeld) in combinatie met een geldige PAT:

```console
--gitrepo-url https://azurereposuser:abcdef1234fdsa4321abcdef@dev.azure.com/your-org/_git/some-private-repository
```

Zie het volgende voor meer informatie over persoonlijke toegangstokens voor GitHub en Azure Repos:

GitHub: [Een persoonlijk toegangstoken maken voor de opdrachtregel][pat-github]

Azure Repos: [persoonlijke toegangstokens maken om toegang te verifiëren][pat-repos]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het monteren van andere volumetypen in Azure Container Instances:

* [Een Azure-bestandsshare koppelen in Azure Container Instances](container-instances-volume-azure-files.md)
* [Een leegDir-volume monteren in Azure Container Instances](container-instances-volume-emptydir.md)
* [Een geheim volume in Azure Container Instances monteren](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld
[pat-github]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[pat-repos]: https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
