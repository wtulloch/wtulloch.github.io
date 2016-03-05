---
layout: post
title:  "Exploring Akka.NET clustering part 1"
date:   2016-02-27 21:22:52 +1100
comments: true
categories: Akka.NET
---
At the current client we are currently investigating implementing Akka.NET as a possible replacement for the framework they currently use to run batch jobs across multiple processors.
Having been an exponent of TPL Dataflow working I am not unfamilar with the Actor/Model pattern but even so it has taken me a little while to grok the Akka.NET way of doing things but I am getting there.

One of the attractions of Akka.NET is its clustering capabilities which allows work to be scaled out. 

If you pull down the Akka.NET source code you can find examples of clustering and the petabridge Akka.Net code samples [https://github.com/petabridge/akkadotnet-code-samples](https://github.com/petabridge/akkadotnet-code-samples "akkadotnet-code-samples") provides a more fully modelled clustering example. The problem I found with these examples is that it is not always self-evident how it actual works. much is left unexplained as I discovered when trying to create my own simple clustered system. So the purpose of this and future posts is to hopefully shed a little more light on working with clustering Akka.NET. 

 If you reading this I assume you already have some familiarity with Akka.NET. If not a good place to start is here [http://getakka.net/](http://getakka.net/ "akka.net") and working through the Akka bootcamp tutorial [https://github.com/petabridge/akka-bootcamp](https://github.com/petabridge/akka-bootcamp "akka bootcamp")

## Before we start ##
The code for all of this can be found at [https://github.com/wtulloch/Akka.NetRemotingAndClusteringExample](https://github.com/wtulloch/Akka.NetRemotingAndClusteringExample "Akka.net remoting and clustering example")
My logger of choice is [Serilog](http://serilog.net/ "Serilog") which used in combination with [https://getseq.net/](https://getseq.net/ "Seq") facilitates the development process. 

Console apps 
For the sake of brevity I am using console apps. The advantage with this is that can easily be converted to Windows service using Topshelf. 

When using remoting or clustering Akka.NET serialisation is strongly typed This means that any actors that you wish to deploy remoting must be available on the remote node. 
## Let's start with remoting ##

Akka.net remoting is a lower level framework used for distributed actors and provides the basis for clustering. 

### Creating the parent node ###

setting up the project


Install akka.net remoting

In this example we are going to deploy an actor to a remote instance. There are other ways of interacting with remote nodes but this approach is closer to how you would implement a clustered system.

The fun part setting up configuration

    <configuration>
	  <configSections>
	    <section name="akka" type="Akka.Configuration.Hocon.AkkaConfigurationSection, Akka" />
	  </configSections>

	  <akka>
	    <hocon>
	      <![CDATA[
	      akka{
	        loggers = ["Akka.Logger.Serilog.SerilogLogger, Akka.Logger.Serilog"]
	        loglevel = INFO
	      }
	      ]]>
	    </hocon>
	  </akka>
   
	</configuration>

Setting up the remote node

in the project Remote node we set up a new ActorSystem as follows:
     
     using (var actorSystem = ActorSystem.Create("RemoteNode"))
            {
                Log.Information("remote Node up");

                ReadKey();
            }

To make this node available for remoting we need to set the actor provider to Akka.Remote.RemoteActorRefProvider and define the remote endpoint.

<akka>
    <hocon>
      <![CDATA[
        akka {
        actor {
        provider = "Akka.Remote.RemoteActorRefProvider, Akka.Remote"
        }
        remote {
        helios.tcp {
            hostname = localhost
            port = 8090
          }
          
         
        }
      ]]>
    </hocon>
  </akka>

With remoting we need to explicitly define the port number since the parent node needs it.

Next we have to not only configure the parent node for remoting 

  akka{
        loggers = ["Akka.Logger.Serilog.SerilogLogger, Akka.Logger.Serilog"]
        loglevel = INFO
        actor {
          provider = "Akka.Remote.RemoteActorRefProvider, Akka.Remote"
          }
        
        remote {
        log-remote-lifecycle-events = DEBUG
        helios.tcp {
            hostname = "localhost"
            port = 0
          }
          }
      }
Not in the case of the parent node we have set the port number to 0. This indicates that Akk.NET should dynamically assign the port number when the service starts up. 

Next we to confgure which actor (or actors) will be deployed to the remote node.

In the actor configuration we add the following as a child of the actor section in the hocn configuration.

    deployment {
          /ProcessActor {
            remote = "akka.tcp://RemoteNode@localhost:8090"
          }
     }

What this configuration is doing is that for the actor with the name "ProcessActor" deploy to the remote node whose address is "akka.tcp://RemoteNode@localhost:8090" where RemoteNode is the name of the ActorSystem on the remote node.




