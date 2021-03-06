---
title: ASP.net Core 2,1 MVC SameSite-Cookie-Beispiel
author: rick-anderson
description: ASP.net Core 2,1 MVC SameSite-Cookie-Beispiel
monikerRange: '>= aspnetcore-2.1 < aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
uid: security/samesite/mvc21
ms.openlocfilehash: 14f3d3d27d5740519e1ba57529d5694b4cdeb9ec
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654991"
---
# <a name="aspnet-core-21-mvc-samesite-cookie-sample"></a>ASP.net Core 2,1 MVC SameSite-Cookie-Beispiel

ASP.net Core 2,1 verfügt über eine integrierte Unterstützung für das [SameSite](https://www.owasp.org/index.php/SameSite) -Attribut, wurde aber in den ursprünglichen Standard geschrieben. Das [gepatchte Verhalten](https://github.com/dotnet/aspnetcore/issues/8212) änderte die Bedeutung von `SameSite.None`, um das SameSite-Attribut mit dem Wert `None`auszugeben, anstatt den Wert überhaupt auszugeben. Wenn Sie den Wert nicht ausgeben möchten, können Sie die `SameSite`-Eigenschaft für ein Cookie auf-1 festlegen.

## <a name="sampleCode"></a>Schreiben des SameSite-Attributs

Im folgenden finden Sie ein Beispiel für das Schreiben eines SameSite-Attributs für ein Cookie:

```c#
var cookieOptions = new CookieOptions
{
    // Set the secure flag, which Chrome's changes will require for SameSite none.
    // Note this will also require you to be running on HTTPS
    Secure = true,

    // Set the cookie to HTTP only which is good practice unless you really do need
    // to access it client side in scripts.
    HttpOnly = true,

    // Add the SameSite attribute, this will emit the attribute with a value of none.
    // To not emit the attribute at all set the SameSite property to (SameSiteMode)(-1).
    SameSite = SameSiteMode.None
};

// Add the cookie to the response cookie collection
Response.Cookies.Append(CookieName, "cookieValue", cookieOptions);
```

## <a name="setting-cookie-authentication-and-session-state-cookies"></a>Festlegen von Cookieauthentifizierung und Sitzungs Zustands Cookies

Cookie-Authentifizierung, Sitzungs Status und [verschiedene andere Komponenten](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) legen ihre SameSite-Optionen über Cookie-Optionen fest, z. b.

```c#
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.Cookie.SameSite = SameSiteMode.None;
        options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
        options.Cookie.IsEssential = true;
    });

services.AddSession(options =>
{
    options.Cookie.SameSite = SameSiteMode.None;
    options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
    options.Cookie.IsEssential = true;
});
```

Im vorangehenden Code legen sowohl die Cookie-als auch der Sitzungs Status Ihr SameSite-Attribut auf `None`fest, wobei das-Attribut mit einem `None` Wert ausgegeben wird, und legen das Secure-Attribut auf "true" fest.

### <a name="run-the-sample"></a>Ausführen des Beispiels

Wenn Sie das [Beispiel Projekt](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)ausführen, laden Sie den Browser Debugger auf der Anfangs Seite, und verwenden Sie ihn, um die Cookie-Sammlung für die Website anzuzeigen. Wenn Sie dies in Edge und Chrome tun möchten `F12` klicken Sie dann auf die Registerkarte `Application`, und klicken Sie im Abschnitt `Storage` unter der Option `Cookies` auf die Website-URL.

![Liste der Browser Debugger-Cookies](BrowserDebugger.png)

In der obigen Abbildung sehen Sie, dass das Cookie, das durch das Beispiel erstellt wird, wenn Sie auf die Schaltfläche "SameSite Cookie erstellen" klicken, einen SameSite-Attribut Wert `Lax`hat, der mit dem im [Beispielcode](#sampleCode)festgelegten Wert übereinstimmt.

## <a name="interception"></a>Abfangen von Cookies

Zum Abfangen von Cookies müssen Sie die `CookiePolicy` Middleware verwenden, um den Wert None gemäß seiner Unterstützung im Browser-Agent des Benutzers anzupassen. Dies muss **vor** allen Komponenten, die Cookies schreiben und innerhalb `ConfigureServices()`konfiguriert werden, in der HTTP-Anforderungs Pipeline platziert werden.

Verwenden Sie `app.UseCookiePolicy()` in der `Configure(IApplicationBuilder, IHostingEnvironment)`-Methode in [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs), um Sie in die Pipeline einzufügen. Beispiel:

```c#
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
       app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

Konfigurieren Sie dann im `ConfigureServices(IServiceCollection services)` die cookierichtlinie so zu konfigurieren, dass eine Hilfsklasse aufgerufen wird, wenn Cookies angehängt oder gelöscht werden. Beispiel:

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
    {
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.None;
        options.OnAppendCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
        options.OnDeleteCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
    });
}

private void CheckSameSite(HttpContext httpContext, CookieOptions options)
{
    if (options.SameSite == SameSiteMode.None)
    {
        var userAgent = httpContext.Request.Headers["User-Agent"].ToString();
        if (SameSite.BrowserDetection.DisallowsSameSiteNone(userAgent))
        {
            options.SameSite = (SameSiteMode)(-1);
        }
    }
}
```

Die `CheckSameSite(HttpContext, CookieOptions)`der Hilfsfunktion:

* Wird aufgerufen, wenn Cookies an die Anforderung angefügt oder aus der Anforderung gelöscht werden.
* Überprüft, ob die `SameSite`-Eigenschaft auf `None`festgelegt ist.
* Wenn `SameSite` auf `None` festgelegt ist und der aktuelle Benutzer-Agent bekanntermaßen nicht den Attribut Wert "None" unterstützt. Die Überprüfung erfolgt mithilfe der [samesitesupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) -Klasse:
  * Legt `SameSite` fest, um den Wert nicht auszugeben, indem die-Eigenschaft auf festgelegt wird `(SameSiteMode)(-1)`

## <a name="targeting-net-framework"></a>Ziel .NET Framework

ASP.net Core und System. Web (ASP.net Classic) verfügen über unabhängige Implementierungen von "SameSite". Die SameSite-KB-Patches für .NET Framework sind nicht erforderlich, wenn Sie ASP.net Core oder die System. Web SameSite-Mindestanforderungen (.NET 4.7.2) auf ASP.net Core anwenden.

ASP.net Core auf .net erfordert das Aktualisieren von nuget-Paketabhängigkeiten, um die entsprechenden Korrekturen zu erhalten.

Um die ASP.net Core Änderungen für .NET Framework zu erhalten, stellen Sie sicher, dass Sie über einen direkten Verweis auf die gepatchten Pakete und Versionen (2.1.14 oder höher 2,1 Versionen) verfügen.

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.CookiePolicy" Version="2.1.14" />
```

### <a name="more-information"></a>Weitere Informationen
 
[Chrome-Updates](https://www.chromium.org/updates/same-site)
[ASP.net Core SameSite-Dokumentation](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1)
[ASP.net Core 2,1 SameSite-Änderungs Ankündigung](https://github.com/dotnet/aspnetcore/issues/8212)