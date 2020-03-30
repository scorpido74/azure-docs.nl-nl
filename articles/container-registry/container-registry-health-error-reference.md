---
title: Foutverwijzing voor statuscontroles
description: Foutcodes en mogelijke oplossingen voor problemen die worden gevonden door de diagnostische opdracht az acr-check-health uit te voeren in Azure Container Registry
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: 971b28b2bf8d9ac22cec0efe979837886762cf17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289138"
---
# <a name="health-check-error-reference"></a>Foutverwijzing voor statuscontrole

Hieronder volgen details over foutcodes die zijn geretourneerd door de [az acr check-health][az-acr-check-health] opdracht. Voor elke fout worden mogelijke oplossingen vermeld.

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

Deze fout betekent dat dockerclient voor CLI niet kan worden gevonden. Als gevolg hiervan worden de volgende extra controles niet uitgevoerd: docker-versie zoeken, de docker-daemonstatus evalueren en een docker-pull-opdracht uitvoeren.

*Mogelijke oplossingen*: Docker client installeren; Dockerpad toevoegen aan de systeemvariabelen.

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

Deze fout betekent dat de Docker daemon-status niet beschikbaar is of dat deze niet kan worden bereikt met de CLI. Als gevolg hiervan zijn Docker-bewerkingen (zoals `docker login` en `docker pull`) niet beschikbaar via de CLI.

*Mogelijke oplossingen*: Herstart Docker daemon of valideer of deze goed is geïnstalleerd.

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

Deze fout betekent dat CLI de `docker --version`opdracht niet heeft kunnen uitvoeren.

*Mogelijke oplossingen:* Probeer de opdracht handmatig uit te voeren, zorg ervoor dat u de nieuwste CLI-versie hebt en onderzoek de foutmelding.

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

Deze fout betekent dat de CLI niet in staat was om een voorbeeldafbeelding naar uw omgeving te trekken.

*Mogelijke oplossingen*: Valideer dat alle componenten die nodig zijn om een afbeelding te trekken, correct worden uitgevoerd.

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

Deze fout betekent dat de Helm-client niet kon worden gevonden door de CLI, waardoor andere Helm-bewerkingen worden uitgesloten.

*Mogelijke oplossingen*: Controleer of de Helm-client is geïnstalleerd en of het pad wordt toegevoegd aan de variabelen van de systeemomgeving.

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

Deze fout betekent dat de CLI niet in staat was om de Helm-versie te bepalen die is geïnstalleerd. Dit kan gebeuren als de Azure CLI-versie (of als de Helm-versie) wordt gebruikt, verouderd is.

*Mogelijke oplossingen:* Update naar de nieuwste Azure CLI-versie of naar de aanbevolen Helm-versie; voer de opdracht handmatig uit en onderzoek het foutbericht.

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

Deze fout betekent dat de DNS voor de opgegeven registerloginserver is gepingd, maar niet heeft gereageerd, wat betekent dat deze niet beschikbaar is. Dit kan duiden op problemen met de connectiviteit. Als alternatief bestaat het register mogelijk niet, heeft de gebruiker mogelijk niet de machtigingen in het register (om de inlogserver goed op te halen) of bevindt het doelregister zich in een andere cloud dan het register dat wordt gebruikt in de Azure CLI.

*Mogelijke oplossingen*: Validerconnectiviteit; controleer de spelling van het register en dat register bestaat; controleren of de gebruiker de juiste machtigingen heeft en of de cloud van het register dezelfde is die wordt gebruikt in de Azure CLI.

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

Deze fout betekent dat het eindpunt voor de uitdaging voor het opgegeven register heeft gereageerd met een 403 Verboden HTTP-status. Deze fout betekent dat gebruikers geen toegang hebben tot het register, waarschijnlijk vanwege een virtuele netwerkconfiguratie. Voer de momenteel geconfigureerde firewallregels uit om de momenteel geconfigureerde firewallregels uit te voeren. `az acr show --query networkRuleSet --name <registry>`

*Mogelijke oplossingen:* Verwijder virtuele netwerkregels of voeg het huidige CLIENT-IP-adres toe aan de toegestane lijst.

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

Deze fout betekent dat het eindpunt van de uitdaging van het doelregister geen uitdaging heeft veroorzaakt.

*Mogelijke oplossingen*: Probeer het na verloop van tijd opnieuw. Als de fout blijft bestaan, https://aka.ms/acr/issuesopent u een probleem op .

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Deze fout betekent dat het eindpunt van de uitdaging van het doelregister een uitdaging heeft uitgegeven, maar dat het register geen Azure Active Directory-verificatie ondersteunt.

*Mogelijke oplossingen:* probeer een andere manier om te verifiëren, bijvoorbeeld met beheerdersreferenties. Als gebruikers moeten verifiëren met Azure Active https://aka.ms/acr/issuesDirectory, opent u een probleem op .

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Deze fout betekent dat de registerloginserver niet heeft gereageerd met een vernieuwingstoken, dus de toegang tot het doelregister is geweigerd. Deze fout kan optreden als de gebruiker niet over de juiste machtigingen in het register beschikt of als de gebruikersreferenties voor de Azure CLI verouderd zijn.

*Mogelijke oplossingen:* controleer of de gebruiker de juiste machtigingen in het register heeft; uitvoeren `az login` om machtigingen, tokens en referenties te vernieuwen.

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Deze fout betekent dat de registerinlogserver niet heeft gereageerd met een toegangstoken, zodat de toegang tot het doelregister is geweigerd. Deze fout kan optreden als de gebruiker niet over de juiste machtigingen in het register beschikt of als de gebruikersreferenties voor de Azure CLI verouderd zijn.

*Mogelijke oplossingen:* controleer of de gebruiker de juiste machtigingen in het register heeft; uitvoeren `az login` om machtigingen, tokens en referenties te vernieuwen.

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

Deze fout betekent dat de client geen beveiligde verbinding met het containerregister kan maken. Deze fout treedt over het algemeen op als u een proxyserver uitvoert of gebruikt.

*Mogelijke oplossingen*: Meer informatie over het werken achter een proxy vindt [u hier.](https://github.com/Azure/azure-cli/blob/master/doc/use_cli_effectively.md#working-behind-a-proxy)

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

Deze fout betekent dat de CLI de inlogserver van het opgegeven register niet kon vinden en er geen standaardachtervoegsel is gevonden voor de huidige cloud. Deze fout kan optreden als het register niet bestaat, als de gebruiker niet over de juiste machtigingen in het register beschikt, als de cloud van het register en de huidige Azure CLI-cloud niet overeenkomen of als de Azure CLI-versie verouderd is.

*Mogelijke oplossingen*: Controleer of de spelling correct is en of het register bestaat; controleren of de gebruiker de juiste machtigingen in het register heeft en of de clouds van het register en de CLI-omgeving overeenkomen; Azure CLI bijwerken naar de nieuwste versie.

## <a name="notary_version_error"></a>NOTARY_VERSION_ERROR

Deze fout betekent dat de CLI niet compatibel is met de momenteel geïnstalleerde versie van Docker/Notaris. Probeer uw notaris.exe-versie te downgraden naar een versie die eerder is dan 0.6.0 door de notarisclient van uw Docker-installatie handmatig te vervangen om dit probleem op te lossen.

## <a name="next-steps"></a>Volgende stappen

Zie [De status van een Azure-containerregister controleren](container-registry-check-health.md)voor opties om de status van een register te controleren.

Zie de [veelgestelde vragen](container-registry-faq.md) voor veelgestelde vragen en andere bekende problemen over Azure Container Registry.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
