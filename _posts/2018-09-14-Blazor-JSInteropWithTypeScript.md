---
layout: post
title:  "Blazor - Setting up a JSInterop library using TypeScript"
date:   2018-09-30 21:22:52 +1100
comments: true
categories:  Blazor; .NET
---
## Intro
At some point, if you have been playing around with Blazor, you may consider creating a Blazor JsInterop library.
If you create a new Blazor library project (`dotnet new blazorlib` ) the project assumes you use JavaScript but what if want use TypeScript to write your interop code?

If you look through the Blazor source code will see that is exactly what is being done for the interop libraries where both TypeScript and webpack is being used. What is not immediately obvious is how this being wired together.

The rest of this post is my attempt at documenting how to get this working to save time in the future plus some additional notes around creating JSInterop libraries for Blazor

## Getting started
NOTE: This is not of my own making but has been dug out of the Blazor solution (Microsoft.AspNetCore.Blazor.Browser.JS). I just thought it would be worth documenting it since the solution was new to me and would be worth making more visible.

In summary the steps requred are as follows:
1. initialise npm
2. import webpack and associated libraries
3. create a webpack configuration file
4. Edit the proj file 

I am, for the sake of expendency, going to assume that have node and npm installed. I will also assume you have the Blazor pieces installed but if not you will need to install the following:
1. [.NET Core 2.1 SDK](https://go.microsoft.com/fwlink/?linkid=873092) (2.1.402 or later).
2. [Visual Studio 2017](https://go.microsoft.com/fwlink/?linkid=873093) (15.8 or later) with the *ASP.NET and web development* workload selected.
3. The latest [Blazor Language Services extension](https://go.microsoft.com/fwlink/?linkid=870389) form the Visual Studio Marketplace
4. The Blazor templates on the command-line:      
``dotnet new -i Microsoft.AspNetCore.Blazor.Templates ``

### Create a new Blazor library
There isn't a Visual Studio template for a Blazor library so to create one you use the dotnet cli:
``` dotnet new blazorlib -o ExampleLibrary ``` 

### Add node modules
While we are doing command line stuff `cd` to the library project folder.

First initialise npm    
`npm init -y`   

Next install the following node modules:
 * webpack
 * wepack-cli
 * typescript
 * ts-loader

 ```npm i webpack webpack-cli typescript ts-loader -D```

Once that is done open up the project in Visual Studio.

### Configuring webpack
Add a new ``` webpack.config.js ``` to the project and use the following as the basic configuration (replacing the entry values).

```JavaScript
const path = require('path');
const webpack = require('webpack');

module.exports = (env, args) =>({
    resolve: {
        extensions: ['.ts', '.js']
    },
    devtool: args.mode === 'development' ? 'inline-source-map' : 'none',
    module: {
        rules: [
            {
                test: /\.ts?$/,
                loader: 'ts-loader'
            }
        ]
    },
    entry: {
        "indexedDb.Blazor": './src/InitialiseIndexDbBlazor.ts'
    },
    output: {
        path: path.join(__dirname, '/dist'),
        filename: '[name].js'
    }
});
```

### update the project file 
Next open the project file and use the following example has a basic guideline for the changes that need to be made.

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
     <TargetFramework>netstandard2.0</TargetFramework>
    <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
    <TypeScriptCompileBlocked>true</TypeScriptCompileBlocked>
    <TypeScriptToolsVersion>Latest</TypeScriptToolsVersion>
    <DefaultItemExcludes>${DefaultItemExcludes};dist\**;node_modules\**</DefaultItemExcludes>
    <NoWarn>CS2008</NoWarn>

    
     <!--VS's FastUpToDateCheck doesn't consider .ts file changes, so it's necessary to disable it to get incremental builds to work correctly (albeit not as fast as if FastUpToDateCheck did work for them)--> 
    
    <DisableFastUpToDateCheck>true</DisableFastUpToDateCheck>
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.Blazor.Build" Version="0.6.0" />
    <WebpackInputs Include="**\*.ts" Exclude="dist\**;node_modules\**; **\*.d.ts" />
  </ItemGroup>
  
  <Target Name="EnsureNpmRestored" Condition="!Exists('node_modules')">
    <Message Importance="high" Text="Restoring dependencies using 'npm'. This may take several minutes..." />
    <Exec Command="npm install" />
  </Target>

  <Target Name="RunWebpack" AfterTargets="ResolveReferences" Inputs="@(WebpackInputs)" Outputs="dist\indexedDb.Blazor.js" DependsOnTargets="EnsureNpmRestored">
    <RemoveDir Directories="dist" />
    <Exec Command="npm run build:debug" Condition="'$(Configuration)' == 'Debug'" />
     <Exec Command="npm run build:production" Condition="'$(Configuration)' != 'Debug'" />
    <ItemGroup>
      <EmbeddedResource Include="dist\**\*.js" LogicalName="blazor:js:%(RecursiveDir)%(Filename)%(Extension)" />
    </ItemGroup>
  </Target>
</Project>
```

### Update npm config.
Finally update the npm config (package.json) and add the following to the script node.
```json
{
  ...
  "scripts": {
    "build:debug": "webpack --mode=development",
    "build:production": "webpack --mode=production"
   
  },
  ...
}
```
In theory once this is all done running build should generate the JavaScript output.

