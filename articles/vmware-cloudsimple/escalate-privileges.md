---
title: Azure VMware-oplossing door CloudSimple - CloudSimple-bevoegdheden escaleren
description: Beschrijft hoe u CloudSimple-machtigingen escaleren om administratieve functies uit te voeren in het VCenter private cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 36c6969ed89d0bb9222f52aa81de0d4128b9e533
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025330"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>CloudSimple-bevoegdheden escaleren om administratieve functies uit te voeren in Private Cloud vCenter

De CloudSimple-privilegesbenadering is ontworpen om vCenter-gebruikers de bevoegdheden te geven die ze nodig hebben om normale bewerkingen uit te voeren. In sommige gevallen heeft een gebruiker mogelijk extra bevoegdheden nodig om een bepaalde taak uit te voeren.  U de bevoegdheden van een vCenter SSO-gebruiker gedurende een beperkte periode escaleren.

Redenen voor het escaleren van bevoegdheden kunnen het volgende zijn:

* Configuratie van identiteitsbronnen
* Gebruikersbeheer
* Verwijdering van gedistribueerde poortgroep
* VCenter-oplossingen installeren (zoals back-up-apps)
* Serviceaccounts maken

> [!WARNING]
> Acties die in de geëscaleerde bevoorrechte status worden uitgevoerd, kunnen een negatieve invloed hebben op uw systeem en kunnen ertoe leiden dat uw systeem niet meer beschikbaar is. Voer alleen de benodigde acties uit tijdens de escalatieperiode.

Vanuit de CloudSimple-portal u de bevoegdheden voor de lokale cloudgebruiker cloudeigenaar op de vCenter SSO [escaleren.](escalate-private-cloud-privileges.md)  U de bevoegdheden van externe gebruikers alleen escaleren als er een extra identiteitsprovider is geconfigureerd op vCenter.  Escalatie van bevoegdheden omvat het toevoegen van de geselecteerde gebruiker aan de ingebouwde groep vSphere-beheerders.  Slechts één gebruiker kan bevoegdheden hebben geëscaleerd.  Als u de bevoegdheden van een andere gebruiker moet escaleren, de-escaleert u eerst de bevoegdheden van de huidige gebruikers.

Gebruikers uit aanvullende identiteitsbronnen moeten worden toegevoegd als leden van de CloudOwner-groep.

> [!CAUTION]
> Nieuwe gebruikers mogen alleen worden toegevoegd aan *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group*, *Cloud-Global-Network-Admin-Group* of *Cloud-Global-VM-Admin-Group*.  Gebruikers die zijn toegevoegd aan de groep *Administrators,* worden automatisch verwijderd.  Alleen serviceaccounts mogen worden toegevoegd aan de groep *Administrators* en serviceaccounts mogen niet worden gebruikt om zich aan te melden bij de vSphere-webgebruikersinterface.

Tijdens de escalatieperiode gebruikt CloudSimple geautomatiseerde bewaking met bijbehorende waarschuwingsmeldingen om onbedoelde wijzigingen in de omgeving te identificeren.
