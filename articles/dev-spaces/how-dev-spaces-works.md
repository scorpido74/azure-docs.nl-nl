---
title: Hoe Azure Dev Spaces werkt
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Beschrijft de processen die Azure Dev Spaces van stroom voorstaan
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: 99b0b3309d115b450bfca94871b6defd885349fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234962"
---
# <a name="how-azure-dev-spaces-works"></a>Hoe Azure Dev Spaces werkt

Het ontwikkelen van een Kubernetes applicatie kan een uitdaging zijn. U hebt Docker- en Kubernetes-configuratiebestanden nodig. U moet uitzoeken hoe u uw toepassing lokaal testen en communiceren met andere afhankelijke services. Mogelijk moet u meerdere services tegelijk ontwikkelen en testen en met een team van ontwikkelaars.

Azure Dev Spaces biedt u meerdere manieren om Kubernetes-toepassingen snel te herhalen en te debuggen en samen te werken met uw team. In dit artikel wordt beschreven wat Azure Dev Spaces kan doen en hoe het werkt.

## <a name="rapidly-iterate-and-debug-your-kubernetes-application"></a>Snel herhalen en debuggen uw Kubernetes-toepassing

Azure Dev Spaces vermindert de inspanning om uw Kubernetes-toepassing te ontwikkelen, te testen en te herhalen in de context van uw AKS-cluster. Deze vermindering van de inspanning stelt ontwikkelaars in staat om zich te concentreren op de bedrijfslogica van hun toepassingen en niet hun services te configureren om in Kubernetes te draaien.

### <a name="connect-your-development-machine-to-aks"></a>Verbind uw ontwikkelmachine met AKS

Met Azure Dev Spaces u uw ontwikkelcomputer verbinden met uw AKS-cluster, zodat u code op uw ontwikkelcomputer uitvoeren en debuggen alsof deze op het cluster wordt uitgevoerd. Azure Dev Spaces leidt het verkeer tussen uw verbonden AKS-cluster om door een pod op uw cluster uit te voeren die fungeert als een externe agent om verkeer tussen uw ontwikkelingsmachine en het cluster om te leiden. Met deze verkeersomleiding kunnen code op uw ontwikkelingscomputer en services die in uw AKS-cluster worden uitgevoerd, communiceren alsof ze zich in hetzelfde AKS-cluster bevinden. Zie [Hoe het aansluiten van uw ontwikkelcomputer op uw AKS werkt][how-it-works-connect]voor meer informatie over het verbinden van uw ontwikkelmachine met AKS.

### <a name="run-your-code-in-aks"></a>Uw code uitvoeren in AKS

Naast het omleiden van verkeer tussen uw ontwikkelcomputer en uw AKS-cluster, u met Azure Dev Spaces uw code direct in AKS configureren en snel uitvoeren. Met Visual Studio, Visual Studio Code of de Azure Dev Spaces CLI uploaden Azure Dev-spaties uw code naar het cluster en bouwen en uitvoeren deze vervolgens. Azure Dev-spaties kunnen ook op intelligente wijze codewijzigingen synchroniseren en uw service opnieuw starten om wijzigingen weer te geven als dat nodig is. Tijdens het uitvoeren van uw code worden logboeken en HTTP-sporen naar uw client gestreamd, zodat u de voortgang controleren en eventuele problemen diagnosticeren. U Azure Dev Spaces ook gebruiken om de foutopsporing in Visual Studio en Visual Studio Code toe te voegen aan Java,Node.js en .NET Core-services. Zie [Hoe het voorbereiden van een project voor Azure Dev Spaces werkt][how-it-works-prep], Hoe het uitvoeren van uw code met Azure [Dev Spaces werkt][how-it-works-up]en hoe het [afluisteren van uw code op afstand met Azure Dev Spaces werkt][how-it-works-remote-debugging]voor meer informatie.

## <a name="team-development"></a>Teamontwikkeling

Azure Dev Spaces helpt teams productief aan hun toepassing te werken op hetzelfde AKS-cluster zonder storend te zijn.

### <a name="intelligent-routing-between-dev-spaces"></a>Intelligente routering tussen dev-ruimten

Met Azure Dev Spaces kan een team één AKS-cluster delen waarop een cloud-native toepassing wordt uitgevoerd en geïsoleerde ontwikkelruimten maken waar het team kan ontwikkelen, testen en debuggen zonder de andere dev-ruimten te verstoren. Een basislijnversie van de toepassing wordt uitgevoerd in een root-dev-ruimte. Teamleden maken vervolgens onafhankelijke onderliggende dev-ruimten op basis van de hoofdruimte voor wijzigingen in de toepassing voor ontwikkeling, testen en foutopsporing. Via de routeringsmogelijkheden in Dev Spaces kunnen onderliggende dev-ruimten aanvragen routeren tussen services die worden uitgevoerd in de onderliggende dev-ruimte en de bovenliggende dev-ruimte. Met deze routering kunnen ontwikkelaars hun eigen versie van een service uitvoeren terwijl ze afhankelijke services van de bovenliggende ruimte opnieuw gebruiken. Elke onderliggende ruimte heeft zijn eigen unieke URL, die door anderen kan worden gedeeld en geopend voor samenwerking. Zie [Hoe routering werkt met Azure Dev Spaces][how-it-works-routing]voor meer informatie over hoe routering werkt in Azure Dev Spaces.

### <a name="live-testing-an-open-pull-request"></a>Live testen van een open pull-aanvraag

U GitHub Actions met Azure Dev Spaces ook gebruiken om wijzigingen in uw toepassing te testen in een pull-aanvraag rechtstreeks in uw cluster voordat u deze samenvoegt. Azure Dev Spaces kan automatisch een revisieversie van de toepassing implementeren in uw cluster, zodat de auteur en andere teamleden de wijzigingen in de context van de hele toepassing kunnen bekijken. Met behulp van de routeringsmogelijkheden van Azure Dev Spaces wordt deze revisieversie van de toepassing ook geïmplementeerd in uw cluster zonder dat dit gevolgen heeft voor andere dev-ruimten. Met al deze mogelijkheden u pull-aanvragen met vertrouwen goedkeuren en samenvoegen. Zie [GitHub Actions & Azure Kubernetes Service][pr-flow]voor een voorbeeld van GitHub Actions en Azure Dev Spaces.

## <a name="next-steps"></a>Volgende stappen

Zie [Uw ontwikkelmachine verbinden met een AKS-cluster om][connect]aan de slag te gaan met het aansluiten van uw lokale ontwikkelingsmachine op uw AKS-cluster.

Zie de [teamontwikkeling in Azure Dev Spaces][quickstart-team] snel van start om aan de slag te gaan met Azure Dev Spaces voor teamontwikkeling.

[connect]: how-to/connect.md
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[routing]: #team-development