---
author: tanmaygore
ms.author: tagore
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.openlocfilehash: 5deba9d8968f71ef1b21517e74e6af4c39aa9271
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279491"
---
> [!WARNING]
> Wanneer u diagnose staken voor een bestaande rol, worden alle extensies die u al hebt ingesteld, uitgeschakeld wanneer het pakket wordt geïmplementeerd. Deze omvatten:
>
> * Microsoft Monitoring Agent Diagnostics
> * Microsoft Azure-beveiligingscontrole
> * Microsoft Antimalware                 
> * Microsoft Monitoring Agent
> * Microsoft Service Profiler-agent      
> * Windows Azure-domeinextensie        
> * Windows Azure Diagnostics-extensie   
> * Windows Azure Remote Desktop Extension
> * Windows Azure-logboekverzamelaar
>
> U uw extensies opnieuw instellen via de Azure-portal of PowerShell nadat u de bijgewerkte rol hebt geïmplementeerd.
>
