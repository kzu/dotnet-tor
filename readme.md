![Icon](https://raw.githubusercontent.com/devlooped/dotnet-tor/main/assets/img/icon-32.png) dotnet-tor
============

[![Version](https://img.shields.io/nuget/v/dotnet-tor.svg?color=royalblue)](https://www.nuget.org/packages/dotnet-tor) [![Downloads](https://img.shields.io/nuget/dt/dotnet-tor.svg?color=darkmagenta)](https://www.nuget.org/packages/dotnet-tor) [![License](https://img.shields.io/github/license/devlooped/dotnet-tor.svg?color=blue)](https://github.com/devlooped/dotnet-tor/blob/main/LICENSE)

Installing or updating (same command can be used for both):

```
dotnet tool update -g dotnet-tor
```

<!-- #content -->
Usage:

```
> dotnet tor -?
tor
  Tor proxy service

Usage:
  tor [options] [command]

Options:
  -p, --proxy <proxy>      Proxy port [default: 1337]
  -s, --socks <socks>      Socks port [default: 1338]
  -c, --control <control>  Control port [default: 1339]
  --version                Show version information
  -?, -h, --help           Show help and usage information

Commands:
  add <name> <service>  Adds a service to register on the Tor network
  config                Edits the full torrc configuration file.
```

The program will automatically check for updates once a day and recommend updating 
if there is a new version available.

Configured services are persisted in the global [dotnet-config](https://dotnetconfig.org) file at `%userprofile%\tor\.netconfig`, and on first run (after configuration), their `.onion` address and keys will be available in a sub-directory alongside the `.netconfig`. This allows the tool to self-update while preserving all configurations and services.

### Exposing local HTTP APIs via Tor

After installation, you might want to expose an .NET Core HTTP service from local port 7071 over the Tor network on port 80. 
You could configure the service with:

```
> dotnet tor add api 127.0.0.1:7071 -p 80
```

Then start the Tor proxy normally with:

```
> dotnet tor
```

There will now be a `%userprofile%\tor\.netconfig\api\hostname` file with the .onion address for the service, like `2gzyxa5ihm7nsggfxnu52rck2vv4rvmdlkiu3zzui5du4xyclen53wid.onion`. You can now reach web API endpoints natively via a .NET 6 client with:

```csharp
using System;
using System.Net;
using System.Net.Http;

var http = new HttpClient(new HttpClientHandler
{
    Proxy = new WebProxy("socks5://127.0.0.1:1338")
});

var response = await http.GetAsync("http://2gzyxa5ihm7nsggfxnu52rck2vv4rvmdlkiu3zzui5du4xyclen53wid.onion/[endpoint]"));
```

The client can just use another `dotnet-tor` proxy running locally with default configuration values and things will Just Work™ and 
properly reach the destination service running anywhere in the world :).

You can even expose the local HTTPS endpoint instead. In this case, the client would need to perform custom validation 
(or entirely bypass it) of the certificate, but otherwise, things work as-is too.

Service-side:

```
> dotnet tor add api 127.0.0.1:5001 -p 443
```

Note that since we're exposing the service over the default port for SSL, we don't need to specify the port in the client:

```csharp
var http = new HttpClient(new HttpClientHandler
{
    ClientCertificateOptions = ClientCertificateOption.Manual,
    ServerCertificateCustomValidationCallback = (_, _, _, _) => true,
    Proxy = new WebProxy("socks5://127.0.0.1:1338"),
});

var response = await http.SendAsync(new HttpRequestMessage(HttpMethod.Get, "https://kbu3mvegpytu4gewdgvjae7zhrzszmetmr5jdlwk5ct5pfzlbaqbdqqd.onion")
{
    Content = new StringContent("Hello World!")
});

response.EnsureSuccessStatusCode();
```

You can play around with a trivial echo service by installing the [dotnet-echo](https://nuget.org/packages/dotnet-echo) tool 
and exposing it over the Tor network.

<!-- #content -->

## Dogfooding

[![CI Version](https://img.shields.io/endpoint?url=https://shields.kzu.io/vpre/dotnet-tor/main&label=nuget.ci&color=brightgreen)](https://pkg.kzu.io/index.json) [![Build](https://github.com/devlooped/dotnet-tor/workflows/build/badge.svg?branch=main)](https://github.com/devlooped/dotnet-tor/actions)

We also produce CI packages from branches and pull requests so you can dogfood builds as quickly as they are produced. 

The CI feed is `https://pkg.kzu.io/index.json`. 

The versioning scheme for packages is:

- PR builds: *42.42.42-pr*`[NUMBER]`
- Branch builds: *42.42.42-*`[BRANCH]`.`[COMMITS]`


<!-- include https://github.com/devlooped/sponsors/raw/main/footer.md -->
# Sponsors 

<!-- sponsors.md -->
[![Clarius Org](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/clarius.png "Clarius Org")](https://github.com/clarius)
[![Kirill Osenkov](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/KirillOsenkov.png "Kirill Osenkov")](https://github.com/KirillOsenkov)
[![MFB Technologies, Inc.](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/MFB-Technologies-Inc.png "MFB Technologies, Inc.")](https://github.com/MFB-Technologies-Inc)
[![Stephen Shaw](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/decriptor.png "Stephen Shaw")](https://github.com/decriptor)
[![Torutek](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/torutek-gh.png "Torutek")](https://github.com/torutek-gh)
[![DRIVE.NET, Inc.](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/drivenet.png "DRIVE.NET, Inc.")](https://github.com/drivenet)
[![Daniel Gnägi](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/dgnaegi.png "Daniel Gnägi")](https://github.com/dgnaegi)
[![Ashley Medway](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/AshleyMedway.png "Ashley Medway")](https://github.com/AshleyMedway)
[![Keith Pickford](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/Keflon.png "Keith Pickford")](https://github.com/Keflon)
[![Thomas Bolon](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/tbolon.png "Thomas Bolon")](https://github.com/tbolon)
[![Kori Francis](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/kfrancis.png "Kori Francis")](https://github.com/kfrancis)
[![Toni Wenzel](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/twenzel.png "Toni Wenzel")](https://github.com/twenzel)
[![Giorgi Dalakishvili](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/Giorgi.png "Giorgi Dalakishvili")](https://github.com/Giorgi)
[![Mike James](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/MikeCodesDotNET.png "Mike James")](https://github.com/MikeCodesDotNET)
[![Dan Siegel](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/dansiegel.png "Dan Siegel")](https://github.com/dansiegel)
[![Reuben Swartz](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/rbnswartz.png "Reuben Swartz")](https://github.com/rbnswartz)
[![Jacob Foshee](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/jfoshee.png "Jacob Foshee")](https://github.com/jfoshee)
[![](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/Mrxx99.png "")](https://github.com/Mrxx99)
[![Eric Johnson](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/eajhnsn1.png "Eric Johnson")](https://github.com/eajhnsn1)
[![Norman Mackay](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/mackayn.png "Norman Mackay")](https://github.com/mackayn)
[![Certify The Web](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/certifytheweb.png "Certify The Web")](https://github.com/certifytheweb)
[![Rich Lee](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/richlee.png "Rich Lee")](https://github.com/richlee)
[![](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/nietras.png "")](https://github.com/nietras)
[![Ix Technologies B.V.](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/IxTechnologies.png "Ix Technologies B.V.")](https://github.com/IxTechnologies)
[![David JENNI](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/davidjenni.png "David JENNI")](https://github.com/davidjenni)
[![Jonathan ](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/Jonathan-Hickey.png "Jonathan ")](https://github.com/Jonathan-Hickey)
[![Oleg Kyrylchuk](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/okyrylchuk.png "Oleg Kyrylchuk")](https://github.com/okyrylchuk)
[![Mariusz Kogut](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/MariuszKogut.png "Mariusz Kogut")](https://github.com/MariuszKogut)
[![Charley Wu](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/akunzai.png "Charley Wu")](https://github.com/akunzai)
[![Jakob Tikjøb Andersen](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/jakobt.png "Jakob Tikjøb Andersen")](https://github.com/jakobt)
[![Seann Alexander](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/seanalexander.png "Seann Alexander")](https://github.com/seanalexander)
[![Tino Hager](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/tinohager.png "Tino Hager")](https://github.com/tinohager)
[![Mark Seemann](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/ploeh.png "Mark Seemann")](https://github.com/ploeh)
[![Angelo Belchior](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/angelobelchior.png "Angelo Belchior")](https://github.com/angelobelchior)
[![Blauhaus Technology (Pty) Ltd](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/BlauhausTechnology.png "Blauhaus Technology (Pty) Ltd")](https://github.com/BlauhausTechnology)
[![Ken Bonny](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/KenBonny.png "Ken Bonny")](https://github.com/KenBonny)
[![Simon Cropp](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/SimonCropp.png "Simon Cropp")](https://github.com/SimonCropp)
[![agileworks-eu](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/agileworks-eu.png "agileworks-eu")](https://github.com/agileworks-eu)
[![](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/sorahex.png "")](https://github.com/sorahex)
[![](https://raw.githubusercontent.com/devlooped/sponsors/main/.github/avatars/wjgthb.png "")](https://github.com/wjgthb)


<!-- sponsors.md -->

[![Sponsor this project](https://raw.githubusercontent.com/devlooped/sponsors/main/sponsor.png "Sponsor this project")](https://github.com/sponsors/devlooped)
&nbsp;

[Learn more about GitHub Sponsors](https://github.com/sponsors)

<!-- https://github.com/devlooped/sponsors/raw/main/footer.md -->
