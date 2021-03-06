---
title: Sicherheitsüberlegungen in ASP.net Core SignalR
author: bradygaster
description: Erfahren Sie, wie Sie die Authentifizierung und Autorisierung in ASP.net Core-SignalRverwenden.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- SignalR
uid: signalr/security
ms.openlocfilehash: f92b56132d0fa55665568416d0760430cb698f8b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655153"
---
# <a name="security-considerations-in-aspnet-core-opno-locsignalr"></a>Sicherheitsüberlegungen in ASP.net Core SignalR

Von [Andrew Stanton-Nurse](https://twitter.com/anurse)

Dieser Artikel enthält Informationen zum Sichern von SignalR.

## <a name="cross-origin-resource-sharing"></a>Cross-Origin Resource Sharing

[Cross-Origin Resource Sharing (cors)](https://www.w3.org/TR/cors/) kann verwendet werden, um Ursprungs übergreifende SignalR Verbindungen im Browser zuzulassen. Wenn JavaScript-Code in einer anderen Domäne als der SignalR-App gehostet wird, muss [cors-Middleware](xref:security/cors) aktiviert werden, damit JavaScript eine Verbindung mit der SignalR-App herstellen kann. Lassen Sie Ursprungs übergreifende Anforderungen nur von vertrauenswürdigen Domänen zu. Beispiel:

* Ihre Website wird auf `http://www.example.com` gehostet
* Ihre SignalR-APP wird auf `http://signalr.example.com` gehostet

Cors sollte in der SignalR-APP so konfiguriert werden, dass nur der Ursprung `www.example.com`zulässig ist.

Weitere Informationen zum Konfigurieren von cors finden Sie unter [Aktivieren von Cross-Origin-Anforderungen (cors)](xref:security/cors). SignalR **erfordert** die folgenden cors-Richtlinien:

* Hiermit werden bestimmte erwartete Ursprünge zugelassen. Es ist möglich, einen beliebigen Ursprung zuzulassen, aber er ist **nicht** sicher oder wird empfohlen.
* HTTP-Methoden `GET` und `POST` müssen zulässig sein.
* Anmelde Informationen müssen zulässig sein, damit cookiebasierte persistente Sitzungen ordnungsgemäß funktionieren. Sie müssen auch dann aktiviert werden, wenn die Authentifizierung nicht verwendet wird.

<!--
::: moniker range=">= aspnetcore-5.0"  // Moniker here just to make sure this doesn't get missed in the 5.0 version update.
However, in 5.0 we have provided an option in the TypeScript client to not use credentials.
The not to use credentials option should only be used when you know 100% that credentials like Cookies are not needed in your app (cookies are used by azure app service when using multiple servers)

For more info, see https://github.com/dotnet/AspNetCore.Docs/issues/16003
.-->

Mit der folgenden cors-Richtlinie kann z. b. ein SignalR Browser-Client auf `https://example.com` auf die SignalR-App zugreifen, die auf `https://signalr.example.com`gehostet wird:

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // ... other middleware ...

    // Make sure the CORS middleware is ahead of SignalR.
    app.UseCors(builder =>
    {
        builder.WithOrigins("https://example.com")
            .AllowAnyHeader()
            .WithMethods("GET", "POST")
            .AllowCredentials();
    });

    // ... other middleware ...
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chatHub");
    });

    // ... other middleware ...
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

::: moniker-end

> [!NOTE]
> SignalR ist nicht kompatibel mit der integrierten cors-Funktion in Azure App Service.

## <a name="websocket-origin-restriction"></a>WebSocket-Ursprungs Einschränkung

::: moniker range=">= aspnetcore-2.2"

Der von CORS erzeugte Schutz gilt nicht für WebSockets. Informationen zur Ursprungs Einschränkung für websockets finden Sie unter [websockets Origin-Einschränkung](xref:fundamentals/websockets#websocket-origin-restriction).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Der von CORS erzeugte Schutz gilt nicht für WebSockets. Für Browser gilt Folgendes **nicht**:

* Ausführen von CORS-Preflightanforderungen
* Berücksichtigen der Einschränkungen, die in den `Access-Control`-Headern bei der Erstellung von WebSocket-Anforderungen angegeben sind

Allerdings senden Browser den `Origin`-Header, wenn die WebSocket-Anforderungen ausgegeben werden. Anwendungen sollten zur Überprüfung dieser Header konfiguriert werden, um sicherzustellen, dass nur WebSockets von den erwarteten Ursprüngen zulässig sind.

In ASP.net Core 2,1 und höher kann die Header Validierung mithilfe einer benutzerdefinierten Middleware **vor `UseSignalR`und der Authentifizierungs Middleware** in `Configure`erreicht werden:

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> Der `Origin`-Header wird vom Client gesteuert und kann wie der `Referer`-Header überlistet werden. Diese Header sollten **nicht** als Authentifizierungsmechanismus verwendet werden.

::: moniker-end

## <a name="connectionid"></a>ConnectionId

Das verfügbar machen von `ConnectionId` kann zu böswilligem Identitätswechsel führen, wenn der SignalR Server oder die Client Version ASP.net Core 2,2 oder früher ist. Wenn die SignalR Server-und Client Version 3,0 oder höher ASP.net Core, muss der `ConnectionToken` anstelle der `ConnectionId` geheim gehalten werden. Der `ConnectionToken` wird absichtlich nicht in einer API verfügbar gemacht.  Es kann schwierig sein, sicherzustellen, dass ältere SignalR Clients keine Verbindung mit dem Server herstellen. auch wenn Ihre SignalR Server Version ASP.net Core 3,0 oder höher ist, sollten die `ConnectionId` nicht verfügbar gemacht werden.

## <a name="access-token-logging"></a>Protokollierung von Zugriffs Token

Wenn websockets oder vom Server gesendete Ereignisse verwendet werden, sendet der Browser Client das Zugriffs Token in der Abfrage Zeichenfolge. Der Empfang des Zugriffs Tokens über eine Abfrage Zeichenfolge ist im Allgemeinen sicher, weil der Standard-`Authorization` Header verwendet wird Verwenden Sie immer HTTPS, um eine sichere End-to-End-Verbindung zwischen dem Client und dem Server sicherzustellen. Viele Webserver protokollieren die URL für jede Anforderung, einschließlich der Abfrage Zeichenfolge. Durch das Protokollieren der URLs kann das Zugriffs Token protokolliert werden. ASP.net Core protokolliert standardmäßig die URL für jede Anforderung, die die Abfrage Zeichenfolge enthält. Beispiel:

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

Wenn Sie Bedenken haben, dass Sie diese Daten mit ihren Serverprotokollen protokollieren möchten, können Sie diese Protokollierung vollständig deaktivieren, indem Sie die `Microsoft.AspNetCore.Hosting` Protokollierung auf `Warning` Ebene oder höher konfigurieren (diese Nachrichten werden auf `Info` Ebene geschrieben). Weitere Informationen finden Sie unter [Protokollfilterung](xref:fundamentals/logging/index#log-filtering) . Wenn Sie trotzdem bestimmte Anforderungs Informationen protokollieren möchten, können Sie [eine Middleware schreiben](xref:fundamentals/middleware/write) , um die benötigten Daten zu protokollieren, und den `access_token` Abfrage Zeichenfolgen-Wert herausfiltern (sofern vorhanden).

## <a name="exceptions"></a>Ausnahmen

Ausnahme Meldungen werden im Allgemeinen als sensible Daten betrachtet, die für einen Client nicht offengelegt werden sollen. Standardmäßig sendet SignalR nicht die Details einer Ausnahme, die von einer Hub-Methode ausgelöst wird, an den Client. Stattdessen empfängt der Client eine generische Meldung, die angibt, dass ein Fehler aufgetreten ist. Die Übermittlung von Ausnahme Meldungen an den Client kann überschrieben werden (z. b. in "Entwicklung" oder "Test") mit [enabledetailederrors](xref:signalr/configuration#configure-server-options). Ausnahme Meldungen sollten in Produktions-apps nicht für den Client verfügbar gemacht werden.

## <a name="buffer-management"></a>Pufferverwaltung

SignalR verwendet Verbindungs Puffer, um eingehende und ausgehende Nachrichten zu verwalten. Standardmäßig beschränkt SignalR diese Puffer auf 32 KB. Die größte Nachricht, die ein Client oder Server senden kann, ist 32 KB. Der maximale Arbeitsspeicher, der von einer Verbindung für Nachrichten belegt wird, beträgt 32 KB. Wenn die Nachrichten immer kleiner als 32 KB sind, können Sie den Grenzwert verringern:

* Verhindert, dass ein Client eine größere Nachricht senden kann.
* Der Server muss niemals große Puffer zuordnen, um Nachrichten zu akzeptieren.

Wenn die Nachrichten größer als 32 KB sind, können Sie den Grenzwert erhöhen. Das Erhöhen dieses Limits bedeutet Folgendes:

* Der-Client kann bewirken, dass der Server große Speicherpuffer zuweist.
* Die Server Zuordnung großer Puffer verringert möglicherweise die Anzahl der gleichzeitigen Verbindungen.

Es gibt Grenzwerte für eingehende und ausgehende Nachrichten. beide können für das [httpconnectiondispatcheroptions](xref:signalr/configuration#configure-server-options) -Objekt konfiguriert werden, das in `MapHub`konfiguriert ist:

* `ApplicationMaxBufferSize` die die maximale Anzahl von Bytes vom Client darstellt, die der Server puffert. Wenn der Client versucht, eine Nachricht zu senden, die über diesen Grenzwert hinausgeht, wird die Verbindung möglicherweise geschlossen.
* `TransportMaxBufferSize` die die maximale Anzahl von Bytes darstellt, die vom Server gesendet werden können. Wenn der Server versucht, eine Nachricht (einschließlich Rückgabewerte von hubmethoden) zu senden, die größer als dieser Grenzwert ist, wird eine Ausnahme ausgelöst.

Wenn Sie das Limit auf `0` festlegen, wird das Limit deaktiviert. Durch das Entfernen des Limits kann ein Client eine beliebige Größe einer Nachricht senden. Böswillige Clients, die große Nachrichten senden, können dazu führen, dass mehr Speicherplatz zugewiesen Durch eine übermäßige Speicherauslastung kann die Anzahl gleichzeitiger Verbindungen erheblich reduziert werden.
