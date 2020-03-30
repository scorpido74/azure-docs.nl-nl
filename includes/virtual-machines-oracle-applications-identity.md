---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: c1cca99a04e78c40cdd1061e52e49f45a73c73eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361555"
---
### <a name="identity-tier"></a>Identiteitslaag 

Met de samenwerking tussen Microsoft en Oracle u een uniforme identiteit instellen voor Azure, OCI en uw Oracle-toepassing. Voor JD Edwards EnterpriseOne- of PeopleSoft-toepassingssuite is een exemplaar van de Oracle HTTP Server (OHS) nodig om eenmalige aanmelding tussen Azure AD en Oracle IDCS in te stellen.

OHS fungeert als een omgekeerde proxy voor de toepassingslaag, wat betekent dat alle aanvragen voor de eindtoepassingen erdoorheen gaan. Oracle Access Manager WebGate is een OHS-webserverplug-plug-in die elk verzoek naar de eindtoepassing onderschept. Als een bron die wordt geopend, is beveiligd (vereist een geverifieerde sessie), initieert de WebGate de OIDC-verificatiestroom met Identity Cloud Service via de browser van de gebruiker. Zie de [Documentatie van Oracle Access Manager](https://docs.oracle.com/en/middleware/idm/access-manager/12.2.1.3/aiaag/integrating-webgate-oidc-server.html)voor meer informatie over de stromen die worden ondersteund door de WebGate van OpenID Connect.

Met deze instelling kan een gebruiker die al is aangemeld bij Azure AD naar de JD Edwards EnterpriseOne- of PeopleSoft-toepassing navigeren zonder opnieuw in te loggen, via Oracle Identity Cloud Service. Klanten die deze oplossing implementeren, profiteren van één malige aanmelding, waaronder één set referenties, een verbeterde aanmeldingservaring, verbeterde beveiliging en lagere kosten voor helpdesks.

Zie de bijbehorende [Oracle-whitepaper](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf)voor meer informatie over het instellen van één aanmelding voor JD Edwards EnterpriseOne of PeopleSoft met Azure AD.