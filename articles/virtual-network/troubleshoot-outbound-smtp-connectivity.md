---
title: Outbound SMTP-connectiviteit in Azure oplossen | Microsoft Documenten
description: Meer informatie over het oplossen van problemen met uitgaande SMTP-connectiviteit in Azure.
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: b8acb50978c5932fe6e6838be86b65c12a0984ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058937"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>Outbound SMTP-connectiviteitsproblemen in Azure oplossen

Vanaf 15 november 2017 worden uitgaande e-mailberichten die rechtstreeks naar externe domeinen (zoals outlook.com en gmail.com) worden verzonden vanaf een virtuele machine (VM) alleen beschikbaar gesteld voor bepaalde abonnementstypen in Microsoft Azure. Uitgaande SMTP-verbindingen die TCP-poort 25 gebruiken, zijn geblokkeerd. (Poort 25 wordt voornamelijk gebruikt voor niet-geverifieerde e-mailbezorging.)

Deze gedragswijziging is alleen van toepassing op nieuwe abonnementen en nieuwe implementaties sinds 15 november 2017.

## <a name="recommended-method-of-sending-email"></a>Aanbevolen methode voor het verzenden van e-mail
We raden u aan geverifieerde SMTP-relayservices te gebruiken (die doorgaans verbinding maken via TCP-poort 587 of 443, maar ook andere poorten ondersteunen) om e-mail te verzenden vanuit Azure VM's of vanuit Azure App Services. Deze services worden gebruikt om de ip- of domeinreputatie te behouden om de kans te minimaliseren dat externe e-mailproviders het bericht weigeren. Dergelijke SMTP-relayservices omvatten, maar zijn niet beperkt tot [SendGrid.](https://sendgrid.com/partners/azure/) Het is ook mogelijk dat u een beveiligde SMTP-relayservice hebt die on-premises wordt uitgevoerd en die u gebruiken.

Het gebruik van deze e-mailservices is niet beperkt in Azure, ongeacht het abonnementstype.

## <a name="enterprise-agreement"></a>Enterprise Agreement
Voor Azure-gebruikers met enterpriseovereenkomst is er geen wijziging in de technische mogelijkheid om e-mail te verzenden zonder een geverifieerd relais te gebruiken. Zowel nieuwe als bestaande Enterprise Agreement-gebruikers kunnen uitgaande e-maillevering van Azure VM's rechtstreeks naar externe e-mailproviders proberen zonder beperkingen van het Azure-platform. Hoewel het niet gegarandeerd is dat e-mailproviders binnenkomende e-mail van een bepaalde gebruiker accepteren, worden leveringspogingen niet geblokkeerd door het Azure-platform voor VM's binnen Enterprise Agreement-abonnementen. U moet rechtstreeks samenwerken met e-mailproviders om problemen met het bezorgen van berichten of spam-filtering op te lossen waarbij specifieke providers betrokken zijn.

## <a name="pay-as-you-go"></a>Betalen per gebruik
Als u zich vóór 15 november 2017 hebt aangemeld voor de aanbiedingen voor het abonnement voor betalen per gebruik of van het Microsoft Partner Netwerk, verandert er niets aan de technische mogelijkheden om uitgaande e-mailbezorging uit te proberen. U uitgaande e-mailbezorging van Azure VM's binnen deze abonnementen nog steeds rechtstreeks naar externe e-mailproviders proberen zonder beperkingen van het Azure-platform. Nogmaals, het is niet gegarandeerd dat e-mailproviders binnenkomende e-mail van een bepaalde gebruiker accepteren en dat gebruikers rechtstreeks moeten samenwerken met e-mailproviders om problemen met het bezorgen van berichten of spamfilters op te lossen waarbij specifieke providers betrokken zijn.

Voor Pay-As-You-Go- of Microsoft Partner Network-abonnementen die na 15 november 2017 zijn gemaakt, zijn er technische beperkingen die e-mail blokkeren die rechtstreeks vanuit VM's binnen deze abonnementen wordt verzonden. Als u e-mail van Azure VM's rechtstreeks naar externe e-mailproviders wilt verzenden (geen geverifieerd SMTP-relais) u een verzoek indienen om de beperking te verwijderen. Verzoeken worden beoordeeld en goedgekeurd naar eigen inzicht van Microsoft en worden pas toegekend nadat extra fraudebestrijdingscontroles zijn uitgevoerd. Als u een aanvraag wilt indienen, opent u een ondersteuningsaanvraag met het volgende probleemtype: **Technical** > **Virtual Network** > **Connectivity** > **Cannot send email (SMTP/Port 25)**. Zorg ervoor dat u details toevoegt over waarom uw implementatie e-mail rechtstreeks naar e-mailproviders moet verzenden in plaats van een geverifieerd relay te gebruiken.

Nadat een Pay-As-You-Go- of Microsoft Partner Network-abonnement is vrijgesteld en de VM's zijn 'Gestopt' & 'Gestart' van de Azure-portal, worden alle VM's binnen dat abonnement in de toekomst vrijgesteld. De vrijstelling is alleen van toepassing op het aangevraagde abonnement en geldt alleen voor virtual machine verkeer rechtstreeks naar het internet. Routeringspoort 25-verkeer via Azure PaaS-services zoals [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/) wordt niet ondersteund.

> [!NOTE]
> Microsoft behoudt zich het recht voor om deze vrijstelling in te trekken als wordt vastgesteld dat er een schending van de servicevoorwaarden heeft plaatsgevonden.

## <a name="msdn-azure-pass-azure-in-open-education-bizspark-and-free-trial"></a>MSDN, Azure Pass, Azure in Open, Education, BizSpark en Gratis proefversie
Als u na 15 november 2017 een MSDN-, Azure Pass, Azure in Open, Education, BizSpark, Azure Sponsoring, Azure Student, Free Trial of een Visual Studio-abonnement hebt gemaakt, hebt u technische beperkingen die e-mail blokkeren die vanuit VM's binnen deze abonnementen rechtstreeks op e-mailproviders. De beperkingen worden gedaan om misbruik te voorkomen. Er worden geen verzoeken om deze beperking te verwijderen ingewilligd.

Als u deze abonnementstypen gebruikt, wordt u aangeraden smtp-relayservices te gebruiken, zoals eerder in dit artikel wordt beschreven of uw abonnementstype te wijzigen.

## <a name="cloud-service-provider-csp"></a>Cloud Service Provider (CSP)

Als u Azure-resources via CSP gebruikt, u het CSP vragen om namens u een deblokkerende vrijstellingsaanvraag met Microsoft te maken als een beveiligd SMTP-relay niet kan worden gebruikt.

## <a name="need-help-contact-support"></a>Hebt u hulp nodig? Contact opnemen met ondersteuning

Als u nog steeds hulp nodig hebt, [neemt u contact op met ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om het probleem snel op te lossen met het volgende probleemtype: Type **probleemabonnement:** **Verzoek om e-mailstroom poort 25 in te schakelen.**
