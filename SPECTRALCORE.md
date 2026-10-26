# Spectral Core modifications

This fork is a Modification of the InterBase ADO.NET Data Provider (`InterBaseSql.Data.InterBaseClient` 10.0.3,
[Embarcadero/IB.NETDataProvider](https://github.com/Embarcadero/IB.NETDataProvider) commit `6b1b737`), itself derived from the
Firebird ADO.NET Data Provider. It is derived from Original Code provided by the Initial Developers, Carlos Guzman Alvarez and
Jiri Cincura; portions created by Embarcadero are Copyright (C) Embarcadero. The modifications are distributed under the
Initial Developer's Public License Version 1.0, the same as the Original Code (see [LICENSE](LICENSE)).

Spectral Core ships the resulting `InterBaseSql.Data.InterBaseClient.dll` (net8.0, file version 10.0.3.1) with Full Convert.

## Changes

2026-10-22, Spectral Core (files under `NETProvider/Provider/src/InterBaseSql.Data.InterBaseClient`):

- `Common/DbField.cs` - text read through a wider connection character set (UTF8) is cut only of its padding. InterBase
  describes such a column with the column's own byte length, so the character count derived from it cut the value (a WIN1252
  `CHAR(20)` read as 5 characters).
- `Common/DbValue.cs` - text parameters are sent whole and the server checks their length in the column's character set; the
  client-side check refused values that fit ("string right truncation").
- `Client/Native/Marshalers/XsqldaMarshaler.cs` - a parameter's `sqllen` is the length of its encoded value, which can exceed
  the described length.
- `Client/Native/IBClientFactory.cs` - the client library registry is filled under a lock; two first connections opened at
  once failed with "An item with the same key has already been added".
- `Embarcadero.Data.InterBaseClient.csproj` - file version 10.0.3.1; assembly version and strong-name key unchanged.
- `Schema/IBMetaData_legacy.xml` - added unchanged from the `InterBaseSql.Data.Schema.IBMetaData_legacy.xml` resource of the
  NuGet `InterBaseSql.Data.InterBaseClient` 10.0.3 assembly; the project embeds it, the upstream repository lacks it.

## Build

```
dotnet build NETProvider/Provider/src/InterBaseSql.Data.InterBaseClient/Embarcadero.Data.InterBaseClient.csproj -c Release -f net8.0
```
