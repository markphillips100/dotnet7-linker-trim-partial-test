## Linker Performance Comparison App

This repo contains a single console app generated from SDK 7.0.103 `dotnet new console`.  It's purpose is to allow a comparison of linker performance when ```TrimMode``` is set to ```Partial```.

The project references 2 packages with an MSBuild condition to support inclusion or not from the command line.  The property ```IncludeEFCoreSqlServerReference``` controls the inclusion of ```Microsoft.EntityFrameworkCore.SqlServer``` package, the other property ```UseDotNet8Linker``` controls the inclusion of the dotnet 8 linker.

The EFCore SqlServer package was chosen via a process of repeated package reference elimination and publishing to establish which references seemed to effect the publish time the most.  The EFCore Cosmos and SqlServer stood out the most, however these both reference other packages so it's possible to perhaps narrow the package scope even further.

## Test Conditions

- CPU: i7-8700K (not overclocked)
- OS: Windows 11

## Results
Four tests are possible with the combination of 2 condition properties.

- Test 1: ```dotnet publish .\LinkTest.csproj -v n```
- Test 2: ```dotnet publish .\LinkTest.csproj -v n /p:IncludeEFCoreSqlServerReference=true```
- Test 3: ```dotnet publish .\LinkTest.csproj -v n /p:UseDotNet8Linker=true```
- Test 4: ```dotnet publish .\LinkTest.csproj -v n /p:IncludeEFCoreSqlServerReference=true /p:UseDotNet8Linker=true```

These are the results for relative comparison:

|Test| Linker Version | Reference Included | Time |
|----|----------------|--------------------|------|
|1|7|No|4.67s|
|2|7|Yes|37.01s|
|3|8|No|4.58s|
|4|8|Yes|15.25s|

The dotnet 8 linker is 3 times slower just by inclusion of the reference so some consideration as to why this is should probably be investigated.  However, the dotnet 7 linker is significantly slower by comparison.