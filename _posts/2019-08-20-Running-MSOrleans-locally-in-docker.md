---
layout: post
title:  Running MS Orleans in Docker locally
date:   2019-08-20 12:22:52 +1000
comments: true
categories: Orleans, Docker
---
A note for my future self since clearly I forget what I have in the past.

If you have pulled down the Microsoft Orleans [Docker sample](https://github.com/dotnet/orleans/tree/master/Samples/2.0/docker-aspnet-core) and then try to run it against the Azure Storage Emulator you may find yourself pulling your hair out wondering why it doesn't work.

Simply put if you try to use the default Azure Storage Emulator connection string it won't work because the storage emulator endpoints (http://127.0.0.1:10000-2) are not accessible by default from external networks including the Docker network.

The first time I tackled this I solved it following Rahul Gupta's [article](https://rahulgupta.us/azure-storage-emulator-with-docker-on-windows/) which uses netsh proxy mapping. This solution works but is itself a fair bit of work to set-up and doesn't persist if you restart your machine.

The second option, which is a lot simpler, is just to replace ```http://127.0.0.1``` with ```host.docker.internal```.
so in you code the connection string to the Azure Storage Emulator will be as follows:

```CSharp
const string connectionString = "DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://host.docker.internal:10000/devstoreaccount1;TableEndpoint=http://host.docker.internal:10002/devstoreaccount1;QueueEndpoint=http://host.docker.internal:10001/devstoreaccount1;";

```
