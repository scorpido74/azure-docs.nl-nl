---
title: SaaS SQL-app-video
description: Dit artikel indexeert verschillende tijdpunten in onze 81 minuten video over SaaS DB huurapp ontwerp, van de Ignite conferentie gehouden 11 oktober 2017. U doorgaan naar het deel dat u interesseert. Ten minste 3 patronen worden beschreven. Azure-functies die de ontwikkeling en het beheer vereenvoudigen, worden beschreven.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
ms.date: 12/18/2018
ms.topic: conceptual
ms.openlocfilehash: 1ee8f2fff958045f652b72358ab928f82920fd6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067619"
---
# <a name="video-indexed-and-annotated-for-multi-tenant-saas-app-using-azure-sql-database"></a>Video geïndexeerd en geannoteerd voor saas-app met meerdere tenants met Azure SQL Database

Dit artikel is een geannoteerde index in de tijdlocaties van een 81 minuten durende video over SaaS huurmodellen of -patronen. In dit artikel u achteruit of vooruit overslaan in de video naar welk gedeelte u interesseert. In de video worden de belangrijkste ontwerpopties voor een databasetoepassing met meerdere tenants op Azure SQL Database uitgelegd. De video bevat demo's, walkthroughs van management code, en soms meer detail geïnformeerd door ervaring dan zou kunnen worden in onze schriftelijke documentatie.

De video versterkt informatie in onze schriftelijke documentatie, te vinden op: 
- *Conceptueel:* [Huurpatronen voor SaaS-database met meerdere huurders][saas-concept-design-patterns-563e]
- *Tutorials:* [De Wingtip Tickets SaaS applicatie][saas-how-welcome-wingtip-app-679t]

De video en de artikelen beschrijven de vele fasen van het maken van een multi-tenant toepassing op Azure SQL Database in de cloud. Speciale functies van Azure SQL Database maken het eenvoudiger om multi-tenant apps te ontwikkelen en te implementeren die zowel gemakkelijker te beheren als betrouwbaar performant zijn.

We werken onze schriftelijke documentatie regelmatig bij. De video is niet bewerkt of bijgewerkt, dus uiteindelijk meer van de details kunnen worden verouderd.



## <a name="sequence-of-38-time-indexed-screenshots"></a>Reeks van 38 tijdgeïndexeerde schermafbeeldingen

In deze sectie wordt de tijdslocatie voor 38 discussies in de video van 81 minuten geïndexeerd. Elke keer index wordt geannoteerd met een screenshot van de video, en soms met extra informatie.

Elke keer index is in de vorm van *h:mm:ss*. De tweede geïndexeerde tijdlocatie, met het label **Sessiedoelstellingen,** begint bijvoorbeeld op de geschatte tijdslocatie van **0:03:11**.


### <a name="compact-links-to-video-indexed-time-locations"></a>Compacte koppelingen naar videogeïndexeerde tijdlocaties

De volgende titels zijn links naar hun overeenkomstige geannoteerde secties later in dit artikel:

- [1. **(Start)** Welkomstdia, 0:00:03](#anchor-image-wtip-min00001)
- [2. Sessiedoelstellingen, 0:03:11](#anchor-image-wtip-min00311)
- [3. Agenda, 0:04:17](#anchor-image-wtip-min00417)
- [4. Multi-tenant web-app, 0:05:05](#anchor-image-wtip-min00505)
- [5. App-webformulier in actie, 0:05:55](#anchor-image-wtip-min00555)
- [6. Kosten per huurder (schaal, isolatie, herstel), 0:09:31](#anchor-image-wtip-min00931)
- [7. Databasemodellen voor multi-tenant: voor- en nadelen, 0:11:59](#anchor-image-wtip-min01159)
- [8. Hybride model combineert voordelen van MT/ST, 0:13:01](#anchor-image-wtip-min01301)
- [9. Single-tenant vs multi-tenant: voor- en nadelen, 0:16:44](#anchor-image-wtip-min01644)
- [10. Pools zijn kosteneffectief voor onvoorspelbare workloads, 0:19:36](#anchor-image-wtip-min01936)
- [11. Demo van database-per-tenant en hybride ST/MT, 0:20:08](#anchor-image-wtip-min02008)
- [12. Live app-formulier met Dojo, 0:20:29](#anchor-image-wtip-min02029)
- [13. MYOB en niet een DBA in zicht, 0:28:54](#anchor-image-wtip-min02854)
- [14. MYOB elastisch zwembadgebruik voorbeeld, 0:29:40](#anchor-image-wtip-min02940)
- [15. Leren van MYOB en andere ISV's, 0:31:36](#anchor-image-wtip-min03136)
- [16. Patronen vormen een E2E SaaS-scenario, 0:43:15](#anchor-image-wtip-min04315)
- [17. Canonieke hybride multi-tenant SaaS-app, 0:47:33](#anchor-image-wtip-min04733)
- [18. Wingtip SaaS-voorbeeld-app, 0:48:10](#anchor-image-wtip-min04810)
- [19. Scenario's en patronen onderzocht in de tutorials, 0:49:10](#anchor-image-wtip-min04910)
- [20. Demo van tutorials en GitHub repository, 0:50:18](#anchor-image-wtip-min05018)
- [21. GitHub repo Microsoft/WingtipSaaS, 0:50:38](#anchor-image-wtip-min05038)
- [22. Het verkennen van de patronen, 0:56:20](#anchor-image-wtip-min05620)
- [23. Huurders voorzien en onboarding, 0:57:44](#anchor-image-wtip-min05744)
- [24. Huurders voorziening en toepassingsaansluiting, 0:58:58](#anchor-image-wtip-min05858)
- [25. Demo van beheerscripts voor het inrichten van één tenant, 0:59:43](#anchor-image-wtip-min05943)
- [26. PowerShell naar oplevering en catalogus, 1:00:02](#anchor-image-wtip-min10002)
- [27. T-SQL SELECT * FROM TenantsExtended, 1:03:30](#anchor-image-wtip-min10330)
- [28. Omgaan met onvoorspelbare tenantworkloads, 1:04:36](#anchor-image-wtip-min10436)
- [29. Elastische poolbewaking, 1:06:39](#anchor-image-wtip-min10639)
- [30. Bewaking van het genereren van belasting en prestaties, 1:09:42](#anchor-image-wtip-min10942)
- [31. Schemabeheer op schaal, 1:10:33](#anchor-image-wtip-min11033)
- [32. Gedistribueerde query over tenantdatabases, 1:12:21](#anchor-image-wtip-min11221)
- [33. Demo van het genereren van tickets, 1:12:32](#anchor-image-wtip-min11232)
- [34. SSMS adhoc analytics, 1:12:46](#anchor-image-wtip-min11246)
- [35. Tenantgegevens extraheren in SQL DW, 1:16:32](#anchor-image-wtip-min11632)
- [36. Grafiek van de dagelijkse verkoopdistributie, 1:16:48](#anchor-image-wtip-min11648)
- [37. Wrap up en call to action, 1:19:52](#anchor-image-wtip-min11952)
- [38. Bronnen voor meer informatie, 1:20:42](#anchor-image-wtip-min12042)


&nbsp;

### <a name="annotated-index-time-locations-in-the-video"></a>Geannoteerde indextijdlocaties in de video

Als u op een schermafbeelding klikt, gaat u naar de exacte tijdslocatie in de video.


&nbsp; <a name="anchor-image-wtip-min00001"/>
#### <a name="1-start-welcome-slide-00001"></a>1. *(Start)* Welkomstdia, 0:00:01

*Leren van MYOB: Ontwerppatronen voor SaaS-toepassingen op Azure SQL Database - BRK3120*

[![Welkomstdia][image-wtip-min00003-brk3120-whole-welcome]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1)

- Titel: Learning from MYOB: Design patterns for SaaS applications on Azure SQL Database Titel: Learning from MYOB: Design patterns for SaaS applications on Azure SQL Database
- Bill.Gibson@microsoft.com
- Principal Program Manager, Azure SQL Database
- Microsoft Ignite sessie BRK3120, Orlando, FL USA, oktober/11 2017


&nbsp; <a name="anchor-image-wtip-min00311"/>
#### <a name="2-session-objectives-00153"></a>2. Sessiedoelstellingen, 0:01:53
[![Sessiedoelstellingen][image-wtip-min00311-session]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=113)

- Alternatieve modellen voor multi-tenant apps, met voor- en nadelen.
- SaaS-patronen om de ontwikkelings-, beheer- en resourcekosten te verlagen.
- Een voorbeeld-app + scripts.
- PaaS-functies + SaaS-patronen maken SQL Database tot een zeer schaalbaar, kostenefficiënt dataplatform voor multi-tenant SaaS.


&nbsp; <a name="anchor-image-wtip-min00417"/>
#### <a name="3-agenda-00409"></a>3. Agenda, 0:04:09
[![Agenda][image-wtip-min00417-agenda]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=249)


&nbsp; <a name="anchor-image-wtip-min00505"/>
#### <a name="4-multi-tenant-web-app-00500"></a>4. Multi-tenant web-app, 0:05:00
[![Wingtip SaaS-app: multi-tenant web-app][image-wtip-min00505-web-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=300)


&nbsp; <a name="anchor-image-wtip-min00555"/>
#### <a name="5-app-web-form-in-action-00539"></a>5. App-webformulier in actie, 0:05:39
[![Webformulier app in actie][image-wtip-min00555-app-web-form]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=339)


&nbsp; <a name="anchor-image-wtip-min00931"/>
#### <a name="6-per-tenant-cost-scale-isolation-recovery-00658"></a>6. Kosten per huurder (schaal, isolatie, herstel), 0:06:58
[![Kosten per huurder, schaal, isolatie, herstel][image-wtip-min00931-per-tenant-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=418)


&nbsp; <a name="anchor-image-wtip-min01159"/>
#### <a name="7-database-models-for-multi-tenant-pros-and-cons-00952"></a>7. Databasemodellen voor multi-tenant: voor- en nadelen, 0:09:52
[![Databasemodellen voor multi-tenant: voor- en nadelen][image-wtip-min01159-db-models-pros-cons]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=592)


&nbsp; <a name="anchor-image-wtip-min01301"/>
#### <a name="8-hybrid-model-blends-benefits-of-mtst-01229"></a>8. Hybride model combineert voordelen van MT/ST, 0:12:29
[![Hybride model combineert voordelen van MT/ST][image-wtip-min01301-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=749)


&nbsp; <a name="anchor-image-wtip-min01644"/>
#### <a name="9-single-tenant-vs-multi-tenant-pros-and-cons-01311"></a>9. Single-tenant vs multi-tenant: voor- en nadelen, 0:13:11
[![Single-tenant vs multi-tenant: voor- en nadelen][image-wtip-min01644-st-vs-mt]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=791)


&nbsp; <a name="anchor-image-wtip-min01936"/>
#### <a name="10-pools-are-cost-effective-for-unpredictable-workloads-01749"></a>10. Pools zijn kosteneffectief voor onvoorspelbare workloads, 0:17:49
[![Pools zijn kosteneffectief voor onvoorspelbare workloads][image-wtip-min01936-pools-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1069)


&nbsp; <a name="anchor-image-wtip-min02008"/>
#### <a name="11-demo-of-database-per-tenant-and-hybrid-stmt-01959"></a>11. Demo van database-per-tenant en hybride ST/MT, 0:19:59
[![Demo van database-per-tenant en hybride ST/MT][image-wtip-min02008-demo-st-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1199)


&nbsp; <a name="anchor-image-wtip-min02029"/>
#### <a name="12-live-app-form-showing-dojo-02010"></a>12. Live app-formulier met Dojo, 0:20:10
[![Live app-formulier met Dojo][image-wtip-min02029-live-app-form-dojo]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1210)

&nbsp; <a name="anchor-image-wtip-min02854"/>
#### <a name="13-myob-and-not-a-dba-in-sight-02506"></a>13. MYOB en geen DBA in zicht, 0:25:06
[![MYOB en niet een DBA in zicht][image-wtip-min02854-myob-no-dba]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1506)


&nbsp; <a name="anchor-image-wtip-min02940"/>
#### <a name="14-myob-elastic-pool-usage-example-02930"></a>14. MYOB elastisch zwembadgebruik voorbeeld, 0:29:30
[![MyOB elastisch zwembad gebruik voorbeeld][image-wtip-min02940-myob-elastic]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1770)


&nbsp; <a name="anchor-image-wtip-min03136"/>
#### <a name="15-learning-from-myob-and-other-isvs-03125"></a>15. Leren van MYOB en andere ISV's, 0:31:25
[![Leren van MYOB en andere ISV's][image-wtip-min03136-learning-isvs]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1885)


&nbsp; <a name="anchor-image-wtip-min04315"/>
#### <a name="16-patterns-compose-into-e2e-saas-scenario-03142"></a>16. Patronen vormen een E2E SaaS-scenario, 0:31:42
[![Patronen vormen in E2E SaaS-scenario][image-wtip-min04315-patterns-compose]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1902)


&nbsp; <a name="anchor-image-wtip-min04733"/>
#### <a name="17-canonical-hybrid-multi-tenant-saas-app-04604"></a>17. Canonieke hybride multi-tenant SaaS-app, 0:46:04
[![Canonical hybride multi-tenant SaaS app][image-wtip-min04733-canonical-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2764)


&nbsp; <a name="anchor-image-wtip-min04810"/>
#### <a name="18-wingtip-saas-sample-app-04801"></a>18. Wingtip SaaS-voorbeeld-app, 0:48:01
[![Wingtip SaaS voorbeeld-app][image-wtip-min04810-wingtip-saas-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2881)


&nbsp; <a name="anchor-image-wtip-min04910"/>
#### <a name="19-scenarios-and-patterns-explored-in-the-tutorials-04900"></a>19. Scenario's en patronen onderzocht in de tutorials, 0:49:00
[![Scenario's en patronen onderzocht in de tutorials][image-wtip-min04910-scenarios-tutorials]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2940)


&nbsp; <a name="anchor-image-wtip-min05018"/>
#### <a name="20-demo-of-tutorials-and-github-repository-05012"></a>20. Demo van tutorials en GitHub repository, 0:50:12
[![Demo tutorials en GitHub repo][image-wtip-min05018-demo-tutorials-github]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3012)


&nbsp; <a name="anchor-image-wtip-min05038"/>
#### <a name="21-github-repo-microsoftwingtipsaas-05032"></a>21. GitHub repo Microsoft/WingtipSaaS, 0:50:32
[![GitHub repo Microsoft/WingtipSaaS][image-wtip-min05038-github-wingtipsaas]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3032)


&nbsp; <a name="anchor-image-wtip-min05620"/>
#### <a name="22-exploring-the-patterns-05615"></a>22. Het verkennen van de patronen, 0:56:15
[![Het verkennen van de patronen][image-wtip-min05620-exploring-patterns]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3375)


&nbsp; <a name="anchor-image-wtip-min05744"/>
#### <a name="23-provisioning-tenants-and-onboarding-05619"></a>23. Huurders voorzien en onboarding, 0:56:19
[![Inrichten van huurders en onboarding][image-wtip-min05744-provisioning-tenants-onboarding-1]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3379)


&nbsp; <a name="anchor-image-wtip-min05858"/>
#### <a name="24-provisioning-tenants-and-application-connection-05752"></a>24. Huurders voorziening en toepassingsaansluiting, 0:57:52
[![Inrichten van tenants en toepassingsaansluiting][image-wtip-min05858-provisioning-tenants-app-connection-2]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3472)


&nbsp; <a name="anchor-image-wtip-min05943"/>
#### <a name="25-demo-of-management-scripts-provisioning-a-single-tenant-05936"></a>25. Demo van beheerscripts voor het inrichten van één tenant, 0:59:36
[![Demo van beheerscripts die één tenant inrichten][image-wtip-min05943-demo-management-scripts-st]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3576)


&nbsp; <a name="anchor-image-wtip-min10002"/>
#### <a name="26-powershell-to-provision-and-catalog-05956"></a>26. PowerShell naar inrichting en catalogus, 0:59:56
[![PowerShell naar inlevering en catalogus][image-wtip-min10002-powershell-provision-catalog]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3596)


&nbsp; <a name="anchor-image-wtip-min10330"/>
#### <a name="27-t-sql-select--from-tenantsextended-10325"></a>27. T-SQL SELECT * FROM TenantsExtended, 1:03:25
[![T-SQL SELECT * VAN TenantsExtended][image-wtip-min10330-sql-select-tenantsextended]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3805)


&nbsp; <a name="anchor-image-wtip-min10436"/>
#### <a name="28-managing-unpredictable-tenant-workloads-10334"></a>28. Omgaan met onvoorspelbare tenantworkloads, 1:03:34
[![Onvoorspelbare tenantworkloads beheren][image-wtip-min10436-managing-unpredictable-workloads]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3814)


&nbsp; <a name="anchor-image-wtip-min10639"/>
#### <a name="29-elastic-pool-monitoring-10632"></a>29. Elastische poolbewaking, 1:06:32
[![Bewaking van elastische zwembaden][image-wtip-min10639-elastic-pool-monitoring]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3992)


&nbsp; <a name="anchor-image-wtip-min10942"/>
#### <a name="30-load-generation-and-performance-monitoring-10937"></a>30. Bewaking van het genereren van belasting en prestaties, 1:09:37
[![Bewaking van het genereren van laden en prestaties][image-wtip-min10942-load-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4117)


&nbsp; <a name="anchor-image-wtip-min11033"/>
#### <a name="31-schema-management-at-scale-10940"></a>31. Schemabeheer op schaal, 1:09:40
[![Schemabeheer op schaal][image-wtip-min11033-schema-management-scale]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=34120)


&nbsp; <a name="anchor-image-wtip-min11221"/>
#### <a name="32-distributed-query-across-tenant-databases-11118"></a>32. Gedistribueerde query over tenantdatabases, 1:11:18
[![Gedistribueerde query over tenantdatabases][image-wtip-min11221-distributed-query]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4278)


&nbsp; <a name="anchor-image-wtip-min11232"/>
#### <a name="33-demo-of-ticket-generation-11228"></a>33. Demo van het genereren van tickets, 1:12:28
[![Demo van het genereren van tickets][image-wtip-min11232-demo-ticket-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4348)


&nbsp; <a name="anchor-image-wtip-min11246"/>
#### <a name="34-ssms-adhoc-analytics-11235"></a>34. SSMS adhoc analytics, 1:12:35
[![SSMS adhoc analytics][image-wtip-min11246-ssms-adhoc-analytics]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4355)


&nbsp; <a name="anchor-image-wtip-min11632"/>
#### <a name="35-extract-tenant-data-into-sql-dw-11546"></a>35. Tenantgegevens extraheren in SQL DW, 1:15:46
[![Tenantgegevens extraheren in SQL DW][image-wtip-min11632-extract-tenant-data-sql-dw]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4546)


&nbsp; <a name="anchor-image-wtip-min11648"/>
#### <a name="36-graph-of-daily-sale-distribution-11638"></a>36. Grafiek van de dagelijkse verkoopdistributie, 1:16:38
[![Grafiek van de dagelijkse verkoopdistributie][image-wtip-min11648-graph-daily-sale-distribution]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4598)


&nbsp; <a name="anchor-image-wtip-min11952"/>
#### <a name="37-wrap-up-and-call-to-action-11743"></a>37. Wrap up en call to action, 1:17:43
[![Afronden en call-to-action][image-wtip-min11952-wrap-up-call-action]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4663)


&nbsp; <a name="anchor-image-wtip-min12042"/>
#### <a name="38-resources-for-more-information-12035"></a>38. Bronnen voor meer informatie, 1:20:35
[![Bronnen voor meer informatie][image-wtip-min12042-resources-more-info]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4835)

- [Blogpost, 22 mei 2017][resource-blog-saas-patterns-app-dev-sql-db-768h]

- *Conceptueel:* [Huurpatronen voor SaaS-database met meerdere huurders][saas-concept-design-patterns-563e]

- *Tutorials:* [De Wingtip Tickets SaaS applicatie][saas-how-welcome-wingtip-app-679t]

- GitHub repositories voor smaken van de Wingtip Tickets SaaS huurapplicatie:
    - [GitHub repo voor - Standalone applicatiemodel][github-wingtip-standaloneapp].
    - [GitHub repo voor - DB Per Tenant-model][github-wingtip-dbpertenant].
    - [GitHub repo voor - Multi-Tenant DB-model][github-wingtip-multitenantdb].





## <a name="next-steps"></a>Volgende stappen

- [Eerste zelfstudieartikel][saas-how-welcome-wingtip-app-679t]




<!-- Image link reference IDs. -->

[image-wtip-min00003-brk3120-whole-welcome]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00003-brk3120-welcome-myob-design-saas-app-sql-db.png "Welkomstdia"

[image-wtip-min00311-session]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00311-session-objectives-takeaway.png "Sessiedoelstellingen."

[image-wtip-min00417-agenda]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00417-agenda-app-management-models-patterns.png "Agenda."

[image-wtip-min00505-web-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00505-wingtip-saas-app-mt-web.png "Wingtip SaaS-app: multi-tenant web-app"

[image-wtip-min00555-app-web-form]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00555-app-form-contoso-concert-hall-night-opera.png "Webformulier app in actie"

[image-wtip-min00931-per-tenant-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00931-saas-data-management-concerns.png "Kosten per huurder, schaal, isolatie, herstel"

[image-wtip-min01159-db-models-pros-cons]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01159-db-models-multi-tenant-saas-apps.png "Databasemodellen voor multi-tenant: voor- en nadelen"

[image-wtip-min01301-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01301-hybrib-model-blends-benefits-mt-st.png "Hybride model combineert voordelen van MT/ST"

[image-wtip-min01644-st-vs-mt]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01644-st-mt-pros-cons.png "Single-tenant vs multi-tenant: voor- en nadelen"

[image-wtip-min01936-pools-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01936-pools-cost-effective-unpredictable-workloads.png "Pools zijn kosteneffectief voor onvoorspelbare workloads"

[image-wtip-min02008-demo-st-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02008-demo-st-hybrid.png "Demo van database-per-tenant en hybride ST/MT"

[image-wtip-min02029-live-app-form-dojo]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02029-app-form-dogwwod-dojo.png "Live app-formulier met Dojo"

[image-wtip-min02854-myob-no-dba]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02854-myob-no-dba.png "MYOB en niet een DBA in zicht"

[image-wtip-min02940-myob-elastic]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02940-myob-elastic-pool-usage-example.png "MyOB elastisch zwembad gebruik voorbeeld"

[image-wtip-min03136-learning-isvs]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min03136-myob-isv-saas-patterns-design-scale.png "Leren van MYOB en andere ISV's"

[image-wtip-min04315-patterns-compose]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04315-patterns-compose-into-e2e-saas-scenario-st-mt.png "Patronen vormen in E2E SaaS-scenario"

[image-wtip-min04733-canonical-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04733-canonical-hybrid-mt-saas-app.png "Canonical hybride multi-tenant SaaS app"

[image-wtip-min04810-wingtip-saas-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04810-saas-sample-app-descr-of-modules-links.png "Wingtip SaaS voorbeeld-app"

[image-wtip-min04910-scenarios-tutorials]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04910-scenarios-patterns-explored-tutorials.png "Scenario's en patronen onderzocht in de tutorials"

[image-wtip-min05018-demo-tutorials-github]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05018-demo-saas-tutorials-github-repo.png "Demo van tutorials en GitHub repo"

[image-wtip-min05038-github-wingtipsaas]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05038-github-repo-wingtipsaas.png "GitHub repo Microsoft/WingtipSaaS"

[image-wtip-min05620-exploring-patterns]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05620-exploring-patterns-tutorials.png "Het verkennen van de patronen"

[image-wtip-min05744-provisioning-tenants-onboarding-1]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05744-provisioning-tenants-connecting-run-time-1.png "Inrichten van huurders en onboarding"

[image-wtip-min05858-provisioning-tenants-app-connection-2]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05858-provisioning-tenants-connecting-run-time-2.png "Inrichten van tenants en toepassingsaansluiting"

[image-wtip-min05943-demo-management-scripts-st]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05943-demo-management-scripts-provisioning-st.png "Demo van beheerscripts die één tenant inrichten"

[image-wtip-min10002-powershell-provision-catalog]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10002-powershell-code.png "PowerShell naar inlevering en catalogus"

[image-wtip-min10330-sql-select-tenantsextended]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10330-ssms-tenantcatalog.png "T-SQL SELECT * VAN TenantsExtended"

[image-wtip-min10436-managing-unpredictable-workloads]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10436-managing-unpredictable-tenant-workloads.png "Onvoorspelbare tenantworkloads beheren"

[image-wtip-min10639-elastic-pool-monitoring]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10639-elastic-pool-monitoring.png "Bewaking van elastische zwembaden"

[image-wtip-min10942-load-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10942-schema-management-scale.png "Bewaking van het genereren van laden en prestaties"

[image-wtip-min11033-schema-management-scale]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11033-schema-manage-1000s-dbs-one.png "Schemabeheer op schaal"

[image-wtip-min11221-distributed-query]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11221-distributed-query-all-tenants-asif-single-db.png "Gedistribueerde query over tenantdatabases"

[image-wtip-min11232-demo-ticket-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11232-demo-ticket-generation-distributed-query.png "Demo van het genereren van tickets"

[image-wtip-min11246-ssms-adhoc-analytics]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11246-tsql-adhoc-analystics-db-elastic-query.png "SSMS adhoc analytics"

[image-wtip-min11632-extract-tenant-data-sql-dw]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11632-extract-tenant-data-analytics-db-dw.png "Tenantgegevens extraheren in SQL DW"

[image-wtip-min11648-graph-daily-sale-distribution]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11648-graph-daily-sale-contoso-concert-hall.png "Grafiek van de dagelijkse verkoopdistributie"

[image-wtip-min11952-wrap-up-call-action]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11952-wrap-call-action-saasfeedback.png "Afronden en call-to-action"

[image-wtip-min12042-resources-more-info]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min12042-resources-blog-github-tutorials-get-started.png "Bronnen voor meer informatie"




<!-- Article link reference IDs. -->

[saas-concept-design-patterns-563e]: saas-tenancy-app-design-patterns.md

[saas-how-welcome-wingtip-app-679t]: saas-tenancy-welcome-wingtip-tickets-app.md


[video-on-youtube-com-478y]: https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1

[video-on-channel9-479c]: https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3120


[resource-blog-saas-patterns-app-dev-sql-db-768h]: https://azure.microsoft.com/blog/saas-patterns-accelerate-saas-application-development-on-sql-database/


[github-wingtip-standaloneapp]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp/

[github-wingtip-dbpertenant]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/

[github-wingtip-multitenantdb]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/

