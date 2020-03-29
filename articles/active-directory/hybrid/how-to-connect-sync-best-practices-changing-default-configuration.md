---
title: 'Azure AD Connect-synchronisatie: de standaardconfiguratie wijzigen | Microsoft Documenten'
description: Biedt aanbevolen procedures voor het wijzigen van de standaardconfiguratie van Azure AD Connect-synchronisatie.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 7638a031-1635-4942-94c3-fce8f09eed5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/29/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 940a35d89996b1eb9600fe4214863d2b5304750e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60242124"
---
# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Azure AD Connect-synchronisatie: aanbevolen procedures voor het wijzigen van de standaardconfiguratie
Het doel van dit onderwerp is om ondersteunde en niet-ondersteunde wijzigingen in Azure AD Connect-synchronisatie te beschrijven.

De configuratie die is gemaakt door Azure AD Connect werkt "zoals het is" voor de meeste omgevingen die on-premises Active Directory synchroniseren met Azure AD. In sommige gevallen is het echter noodzakelijk om een aantal wijzigingen toe te passen op een configuratie om aan een bepaalde behoefte of vereiste te voldoen.

## <a name="changes-to-the-service-account"></a>Wijzigingen in het serviceaccount
Azure AD Connect-synchronisatie wordt uitgevoerd onder een serviceaccount dat is gemaakt door de wizard installatie. Dit serviceaccount bevat de versleutelingssleutels van de database die wordt gebruikt door synchronisatie. Het is gemaakt met een 127 tekens lang wachtwoord en het wachtwoord is ingesteld om niet te verlopen.

* Het wordt **niet ondersteund** om het wachtwoord van het serviceaccount te wijzigen of opnieuw in te stellen. Hierdoor worden de versleutelingssleutels vernietigd en kan de service geen toegang krijgen tot de database en kan deze niet worden gestart.

## <a name="changes-to-the-scheduler"></a>Wijzigingen in de planner
Vanaf de releases van build 1.1 (februari 2016) u de [planner](how-to-connect-sync-feature-scheduler.md) configureren op een andere synchronisatiecyclus dan de standaard30 minuten.

## <a name="changes-to-synchronization-rules"></a>Wijzigingen in synchronisatieregels
De wizard Installatie biedt een configuratie die moet werken voor de meest voorkomende scenario's. Als u wijzigingen in de configuratie moet aanbrengen, moet u deze regels volgen om nog steeds een ondersteunde configuratie te hebben.

> [!WARNING]
> Als u wijzigingen aanbrengt in de standaardsynchronisatieregels, worden deze wijzigingen overschreven wanneer Azure AD Connect de volgende keer wordt bijgewerkt, wat resulteert in onverwachte en waarschijnlijk ongewenste synchronisatieresultaten.

* U [kenmerkstromen wijzigen](how-to-connect-sync-change-the-configuration.md#other-common-attribute-flow-changes) als de standaarddirecte kenmerkstromen niet geschikt zijn voor uw organisatie.
* Als u geen kenmerk wilt [doorstromen](how-to-connect-sync-change-the-configuration.md#do-not-flow-an-attribute) en bestaande kenmerkwaarden in Azure AD wilt verwijderen, moet u een regel voor dit scenario maken.
* [Schakel een ongewenste synchronisatieregel uit](#disable-an-unwanted-sync-rule) in plaats van deze te verwijderen. Een verwijderde regel wordt opnieuw gemaakt tijdens een upgrade.
* Als [u een regel out-of-box](#change-an-out-of-box-rule)wilt wijzigen, moet u een kopie van de oorspronkelijke regel maken en de regel out-of-box uitschakelen. De synchronisatieregeleditor vraagt en helpt u.
* Exporteer uw aangepaste synchronisatieregels met de editor synchronisatieregels. De editor biedt u een PowerShell-script dat u gebruiken om ze eenvoudig opnieuw te maken in een scenario voor noodherstel.

> [!WARNING]
> De out-of-box synchronisatieregels hebben een duimafdruk. Als u deze regels wijzigt, komt de duimafdruk niet meer overeen. U in de toekomst problemen ondervinden wanneer u een nieuwe versie van Azure AD Connect probeert toe te passen. Breng alleen wijzigingen aan zoals het in dit artikel wordt beschreven.

### <a name="disable-an-unwanted-sync-rule"></a>Een ongewenste synchronisatieregel uitschakelen
Verwijder geen out-of-box synchronisatieregel. Het wordt opnieuw gemaakt tijdens de volgende upgrade.

In sommige gevallen heeft de wizard installatie een configuratie geproduceerd die niet werkt voor uw topologie. Als u bijvoorbeeld een foresttopologie voor accountresources hebt, maar u het schema in het accountforest hebt uitgebreid met het Exchange-schema, worden regels voor Exchange gemaakt voor het accountforest en het resourceforest. In dit geval moet u de synchronisatieregel voor Exchange uitschakelen.

![Uitgeschakelde synchronisatieregel](./media/how-to-connect-sync-best-practices-changing-default-configuration/exchangedisabledrule.png)

In de afbeelding hierboven heeft de installatiewizard een oud Exchange 2003-schema gevonden in het accountforest. Deze schema-extensie is toegevoegd voordat het bronbos werd geïntroduceerd in de omgeving van Fabrikam. Om ervoor te zorgen dat er geen kenmerken van de oude Exchange-implementatie worden gesynchroniseerd, moet de synchronisatieregel worden uitgeschakeld zoals weergegeven.

### <a name="change-an-out-of-box-rule"></a>Een regel wijzigen die niet in de doos is
De enige keer dat u een out-of-box regel moet wijzigen, is wanneer u de join-regel moet wijzigen. Als u een kenmerkstroom moet wijzigen, moet u een synchronisatieregel maken met een hogere prioriteit dan de regels voor out-of-box. De enige regel die je praktisch moet klonen is de regel **in van AD - User Join**. U alle andere regels overschrijven met een hogere voorrangsregel.

Als u wijzigingen moet aanbrengen in een regel die niet in de doos is, moet u een kopie maken van de regel die niet in het kader van de regel is en de oorspronkelijke regel uitschakelen. Breng vervolgens de wijzigingen aan in de gekloonde regel. De Synchronisatieregeleditor helpt u met deze stappen. Wanneer u een regel out-of-box opent, wordt u in het dialoogvenster weergegeven:  
![Regel out-of-box waarschuwen](./media/how-to-connect-sync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Selecteer **Ja** om een kopie van de regel te maken. De gekloonde regel wordt vervolgens geopend.  
![Gekloonde regel](./media/how-to-connect-sync-best-practices-changing-default-configuration/clonedrule.png)

Breng op deze gekloonde regel de nodige wijzigingen aan in het bereik, de join en transformaties.

## <a name="next-steps"></a>Volgende stappen
**Overzichtsonderwerpen**

* [Synchronisatie van Azure AD Connect: synchronisatie begrijpen en aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
