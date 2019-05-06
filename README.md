# anycastamqpnetlite
How to dynamically define an anycast routing type for an address when using AMQ .NET clients


Run the follow command

dotnet new console --name Example

Inside the Example directory open the Example.csproj file, add the follow ItemGroup tag into <Project> tag pointing to AMQP.dll available for download in this repo.

```
<ItemGroup>
  <Reference Include="AMQP">
          <HintPath>/home/raraujo/ferramentas/amq-dotnetcore/bin/netstandard2.0/AMQP.dll</HintPath>
  </Reference>
</ItemGroup>
```

Afterward open Program.cs file, clear all words and paste the code below: 


```
using System;
using Amqp;

namespace hello_world
{
    class Program
    {
        static void Main(string[] args)
        {


           string    url = (args.Length > 0) ? args[0] :                       
                "amqp://guest:guest@127.0.0.1:5672";
            string target = (args.Length > 1) ? args[1] : "anycast://example";           
            int     count = (args.Length > 2) ? Convert.ToInt32(args[2]) : 10;  

            Address      peerAddr = new Address(url);                           
            Connection connection = new Connection(peerAddr);                   
            Session       session = new Session(connection);
            SenderLink     sender = new SenderLink(session, "send-1", target);  

            for (int i = 0; i < count; i++)
            {

                Message msg = new Message("Test " + i);                       
                sender.Send(msg);                                               
                Console.WriteLine("Test: " + msg.Body.ToString());
            }

            sender.Close();                                                     
            session.Close();
            connection.Close();
        }
    }
}

```


Also, open the broker.xml file, find for the acceptor used by your application and add the anycastprefix as follows:

```
<acceptor name="amqp">tcp://0.0.0.0:5672?protocols=AMQP;anycastPrefix=anycast://</acceptor>

```
