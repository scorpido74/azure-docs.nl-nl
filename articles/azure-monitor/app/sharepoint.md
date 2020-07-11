---
title: Een SharePoint-site met Application Insights bewaken
description: Een nieuwe toepassing bewaken met een nieuwe instrumentatiesleutel
ms.topic: conceptual
ms.date: 07/11/2018
ms.openlocfilehash: b9b31a715b19d6c06cbddb91c0bd9279088c4447
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86221048"
---
# <a name="monitor-a-sharepoint-site-with-application-insights"></a>Een SharePoint-site met Application Insights bewaken
Azure Application Insights bewaakt de beschikbaarheid, de prestaties en het gebruik van uw apps. Hier wordt uitgelegd hoe u dit kunt instellen voor een SharePoint-site.

## <a name="create-an-application-insights-resource"></a>Een Application Insights-resource maken
Maak in de [Azure Portal](https://portal.azure.com) een nieuwe Application Insights-resource. Kies ASP.NET als het toepassingstype.

![Op Eigenschappen klikken, de sleutel selecteren en op Ctrl + C drukken](./media/sharepoint/001.png)

In het venster dat wordt geopend, ziet u prestatie- en gebruiksgegevens over uw app. Als u deze weer wilt openen wanneer u zich de volgende keer aanmeldt bij Azure, moet u de tegel hiervoor zoeken in het beginscherm. U kunt ook op Bladeren klikken om te zoeken.

## <a name="add-the-script-to-your-web-pages"></a>Het script toevoegen aan uw webpagina's

```HTML
<!-- 
To collect user behavior analytics tools about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(n){var o={config:n,initialize:!0},t=document,e=window,i="script";setTimeout(function(){var e=t.createElement(i);e.src=n.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",t.getElementsByTagName(i)[0].parentNode.appendChild(e)});try{o.cookie=t.cookie}catch(e){}function a(n){o[n]=function(){var e=arguments;o.queue.push(function(){o[n].apply(o,e)})}}o.queue=[],o.version=2;for(var s=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];s.length;)a("track"+s.pop());var r="Track",c=r+"Page";a("start"+c),a("stop"+c);var u=r+"Event";if(a("start"+u),a("stop"+u),a("addTelemetryInitializer"),a("setAuthenticatedUserContext"),a("clearAuthenticatedUserContext"),a("flush"),o.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===n.disableExceptionTracking||n.extensionConfig&&n.extensionConfig.ApplicationInsightsAnalytics&&!0===n.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){a("_"+(s="onerror"));var p=e[s];e[s]=function(e,n,t,i,a){var r=p&&p(e,n,t,i,a);return!0!==r&&o["_"+s]({message:e,url:n,lineNumber:t,columnNumber:i,error:a}),r},n.autoExceptionInstrumented=!0}return o}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);(window[aiName]=aisdk).queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

Voeg het script vlak vóór de &lt; /Head- &gt; tag van elke pagina die u wilt bijhouden. Als uw website een basis pagina heeft, kunt u het script daar plaatsen. In een ASP.NET MVC-project plaatst u deze bijvoorbeeld in View\Shared\_Layout.cshtml

Het script bevat de instrumentatiesleutel die de telemetrie naar uw Application Insights-resource stuurt.

### <a name="add-the-code-to-your-site-pages"></a>De code aan de pagina's toevoegen
#### <a name="on-the-master-page"></a>Op de basispagina
Als u de basispagina van de site kunt bewerken, biedt dit bewaking voor elke pagina op de site.

Bekijk de basispagina en bewerk deze met SharePoint Designer of een andere editor.

![Scherm afbeelding waarin wordt weer gegeven hoe u de basis pagina bewerkt met behulp van Sharepoing Designer of een andere editor.](./media/sharepoint/03-master.png)

Voeg de code toe vlak voor de </head> Tag. 

![Scherm afbeelding die laat zien waar u de code aan uw site pagina kunt toevoegen.](./media/sharepoint/04-code.png)

#### <a name="or-on-individual-pages"></a>Of op afzonderlijke pagina’s
Als u een beperkt aantal pagina's wilt bewaken, voegt u het script afzonderlijk toe aan elke pagina. 

Voeg een webonderdeel in en sluit het codefragment hierin in.

![Scherm afbeelding van het toevoegen van het script voor het bewaken van een beperkt aantal pagina's.](./media/sharepoint/05-page.png)

## <a name="view-data-about-your-app"></a>Gegevens over uw app weergeven
Implementeer uw app opnieuw.

Ga terug naar uw toepassingsblade in de [Azure Portal](https://portal.azure.com).

De eerste gebeurtenissen worden weergegeven bij Zoeken. 

![Scherm opname waarin de nieuwe gegevens worden weer gegeven die u in de app kunt weer geven.](./media/sharepoint/09-search.png)

Klik na een paar seconden op Vernieuwen als u meer gegevens verwacht.

## <a name="capturing-user-id"></a>Gebruikers-id vastleggen
Met het standaardcodefragment van de webpagina wordt niet de gebruikers-id van SharePoint vastgelegd, maar u kunt dit doen met een kleine wijziging.

1. Kopieer de instrumentatiesleutel van uw app van de vervolgkeuzelijst Essentials in Application Insights. 

    ![Scherm opname van het kopiëren van de app-instrumentatie uit de vervolg keuzelijst Essentials in Application Insights.](./media/sharepoint/02-props.png)

1. Vervang de instrumentatiesleutel door XXXX in het onderstaande fragment. 
2. Sluit dit script in in uw SharePoint-app, in plaats van het fragment dat u via de portal krijgt.

```


<SharePoint:ScriptLink ID="ScriptLink1" name="SP.js" runat="server" localizable="false" loadafterui="true" /> 
<SharePoint:ScriptLink ID="ScriptLink2" name="SP.UserProfiles.js" runat="server" localizable="false" loadafterui="true" /> 

<script type="text/javascript"> 
var personProperties; 

// Ensure that the SP.UserProfiles.js file is loaded before the custom code runs. 
SP.SOD.executeOrDelayUntilScriptLoaded(getUserProperties, 'SP.UserProfiles.js'); 

function getUserProperties() { 
    // Get the current client context and PeopleManager instance. 
    var clientContext = new SP.ClientContext.get_current(); 
    var peopleManager = new SP.UserProfiles.PeopleManager(clientContext); 

    // Get user properties for the target user. 
    // To get the PersonProperties object for the current user, use the 
    // getMyProperties method. 

    personProperties = peopleManager.getMyProperties(); 

    // Load the PersonProperties object and send the request. 
    clientContext.load(personProperties); 
    clientContext.executeQueryAsync(onRequestSuccess, onRequestFail); 
} 

// This function runs if the executeQueryAsync call succeeds. 
function onRequestSuccess() { 
var appInsights=window.appInsights||function(config){
function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
    }({
        instrumentationKey:"XXXX"
    });
    window.appInsights=appInsights;
    appInsights.trackPageView(document.title,window.location.href, {User: personProperties.get_displayName()});
} 

// This function runs if the executeQueryAsync call fails. 
function onRequestFail(sender, args) { 
} 
</script> 


```



## <a name="next-steps"></a>Volgende stappen
* [Webtests](../../azure-monitor/app/monitor-web-app-availability.md) om de beschikbaarheid van de site te bewaken.
* [Application Insights](../../azure-monitor/app/app-insights-overview.md) voor andere typen app.

<!--Link references-->


