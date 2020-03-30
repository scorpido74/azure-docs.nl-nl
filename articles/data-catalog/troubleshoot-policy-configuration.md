---
title: Azure-gegevenscatalogus oplossen
description: In dit artikel worden veelvoorkomende probleemoplossingsproblemen beschreven voor Azure Data Catalog-bronnen.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: 84bd14f8ae18527b4f6e9d8509a12555baec8771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68879541"
---
# <a name="troubleshooting-azure-data-catalog"></a>Problemen met Azure Data Catalog oplossen

In dit artikel worden veelvoorkomende probleemoplossingsproblemen beschreven voor Azure Data Catalog-bronnen. 

## <a name="functionality-limitations"></a>Functionaliteitsbeperkingen

Bij het gebruik van Azure Data Catalog is de volgende functionaliteit beperkt:

- Accounts met het type **Gastrol** worden niet ondersteund. U geen gastaccounts toevoegen als gebruikers van Azure Data [https://www.azuredatacatalog.com](https://www.azuredatacatalog.com)Catalog en gastgebruikers kunnen de portal niet gebruiken op.

- Azure Data Catalog-resources maken met Azure Resource Manager-sjablonen of Azure PowerShell-opdrachten wordt niet ondersteund.

- De azure-gegevenscatalogusbron kan niet worden verplaatst tussen Azure-tenants.

## <a name="azure-active-directory-policy-configuration"></a>Azure Active Directory-beleid configureren

Het kan voorkomen dat u zich wel kunt aanmelden bij de Azure Data Catalog-portal, maar niet bij het hulpprogramma voor gegevensbronregistratie. Er wordt dan een foutmelding weergegeven. Deze fout kan optreden wanneer u zich in het bedrijfsnetwerk bevindt of wanneer u verbinding maakt van buiten het bedrijfsnetwerk.

Het registratiehulpprogramma maakt gebruik van *formulierverificatie* om de gebruikersaanmeldingen te valideren bij Azure Active Directory. Als u zich wilt aanmelden, moet een Azure Active Directory-beheerder formulierverificatie inschakelen in het *algemene verificatiebeleid*.

Met het algemene verificatiebeleid kunt u afzonderlijke verificatie voor intranet- en extranetverbindingen inschakelen, zoals wordt weergegeven in de volgende afbeelding. Aanmeldingsfouten kunnen optreden als verificatie van formulieren niet is ingeschakeld voor het netwerk van waaruit u verbinding maakt.

 ![Het algemene verificatiebeleid van Azure Active Directory](./media/troubleshoot-policy-configuration/global-auth-policy.png)

Zie [Verificatiebeleid configureren](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn486781(v=ws.11)) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Een Azure Data Catalog maken](data-catalog-get-started.md)
