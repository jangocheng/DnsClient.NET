---
slug: readme
title: Readme
author: Michael Conrad
lastModified: 2017-04-05 12:00:00
pubDate: 2017-03-26 12:00:00
categories: DnsClient
---

# DnsClient.NET
DnsClient.NET is a simple yet very powerful and high performant open source library for the .NET Framework to do DNS lookups.

## Usage
See http://dnsclient.michaco.net for more details and documentation.

The following example instantiates a new `LookupClient` without specifying a DNS endpoint. 
DnsClient.NET will query your system network adapters to determine available DNS servers.

``` csharp

var lookup = new LookupClient();
var result = await lookup.QueryAsync("google.com", QueryType.ANY);

var record = result.Answers.ARecords().FirstOrDefault();
var address = record?.Address;
``` 

## Builds

[![Build status](https://ci.appveyor.com/api/projects/status/y1nlxim8tkv7w3f4?svg=true)](https://ci.appveyor.com/project/MichaCo/dnsclient-net)

Get it via NuGet https://www.nuget.org/packages/DnsClient/

Get beta builds from [MyGet](https://www.myget.org/feed/dnsclient/package/nuget/DnsClient).

### Build from Code
Just clone the repository and open the solution in Visual Studio 2017.
Or use the dotnet client via command line.

The unit tests don't require any additional setup right now.

If you want to test the different record types, there are config files for Bind under tools. 
Just [download Bind](https://www.isc.org/downloads/) for Windows and copy the binaries to tools/BIND, then run bind.cmd.
If you are running this on Linux, you can use my config files and replace the default ones if you want.

Now, you can use **samples/MiniDig** to query the local DNS server. 
The following should return many different resource records:

``` cmd
dotnet run -s localhost micha.mcnet.com any
```

To test some random domain names, run MiniDig with the `random` sub command (works without setting up Bind, too).

``` cmd
dotnet run random -s localhost
```

## Features
### General
* Full Async API
* UDP and TCP lookup, configurable if TCP should be used as fallback in case UDP result is truncated (default=true).
* Caching
  * Query result cache based on provided TTL 
  * Minimum TTL setting to overrule the result's TTL and always cache the responses for at least that time. (Even very low value, like a few milliseconds, do make a huge difference if used in high traffic low latency scenarios)
  * Cache can be disabled altogether
* Supports multiple DNS endpoints to be configured
* Retry logic in case of timeouts and errors (configurable)
* Audit trail of each response
* Configurable error handling. Throwing DNS errors, like `NotExistentDomain` is turned off by default

### Supported resource records
* PTR for reverse lookups
* A, AAAA, NS, CNAME, SOA, MB, MG, MR, WKS, HINFO, MINFO, MX, RP, TXT, AFSDB, SRV, URI, CAA
* OPT (currently only for reading the supported UDP buffer size, EDNS version)

## Examples

* The [Samples](https://github.com/MichaCo/DnsClient.NET.Samples) repository will have some solutions to showcase the usage and also to test some functionality.

* [MiniDig](https://github.com/MichaCo/DnsClient.NET/tree/dev/samples/MiniDig) (See the readme over there)

## Milestones
### 1.1 release
* More testing
* More RRs parsing

## Motivation and Thanks!
Thanks to 

[<img src="https://www.browserstack.com/images/layout/browserstack-logo-600x315.png" width="300">](https://www.browserstack.com) 

for supporting me to test my websites and projects!

I used [Heijden.Dns](https://github.com/ghuntley/Heijden.Dns) as a baseline for my first steps into the world of DNS RFCs. A big thanks to the original author of the code, Alphons van der Heijden and also Geoffrey Huntley who created a great package out of that. This was a great resource to learn from it and start building this API!

In the end, I decided to write my own API to make it easy to use, more robust, extensible and build on .NET Core for xplat support.
