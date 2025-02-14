# QuickStart

## Manual build & run the sources

This method is not as straightforward as the docker quickstart but is a lot more flexible. If your aim is to develop some dApps it's better you follow these more advanced ways of launching a node. This section will walk you through configuring, running and interacting with an AElf node.

Pre-requisite: this guide has a dependency on Redis, so you should install it and run a server. A part from this, only very basic command line knowledge is required and you can just follow the steps.

First, if you haven't already done it, clone our [repository](https://github.com/AElfProject/AElf)

```bash
git clone https://github.com/AElfProject/AElf.git aelf
cd aelf/src
```

Navigate into the newly created **aelf** directory.

### Generating the nodes account

Installing aelf-command

```bash
npm i aelf-command -g # please intall the Nodejs at first
```

Create account

```bash
aelf-command create
```

The next command will export your datadir, this can be anywhere you want (depending on your system you can also set this is the bashrc or equivalent):

```bash
aelf-command create -d [path]
```

The output should look like this:

```bash
aelf-command create

Your wallet info is :
Mnemonic            : great mushroom loan crisp ... door juice embrace
Private Key         : e038eea7e151eb451ba2901f7...b08ba5b76d8f288
Public Key          : 0478903d96aa2c8c0...6a3e7d810cacd136117ea7b13d2c9337e1ec88288111955b76ea
Address             : 2Ue31YTuB5Szy7cnr3SCEGU2gtGi5uMQBYarYUR5oGin1sys6H
✔ Save account info into a file? … no / yes
✔ Enter a password … ********
✔ Confirm password … ********
✔
Account info has been saved to "/Users/xxx/.local/share/aelf/keyStore/2Ue31YTuB5Szy7cnr...Gi5uMQBYarYUR5oGin1sys6H.json"
```

If you want get your account info. The output should look like this:

```bash
aelf-command console -a 2Ue31YTuB5Szy7cnr3SCEGU2gtGi5uMQBYarYUR5oGin1sys6H
✔ Enter the password you typed when creating a wallet … ********
✔ Succeed!
Welcome to aelf interactive console. Ctrl + C to terminate the program. Double tap Tab to list objects

   ╔═══════════════════════════════════════════════════════════╗
   ║                                                           ║
   ║   NAME       | DESCRIPTION                                ║
   ║   AElf       | imported from aelf-sdk                     ║
   ║   aelf       | the instance of an aelf-sdk, connect to    ║
   ║              | http://127.0.0.1:8000                  ║
   ║   _account   | the instance of an AElf wallet, address    ║
   ║              | is                                         ║
   ║              | 2Ue31YTuB5Szy7cnr3SCEGU2gtGi5uMQBYarYUR…   ║
   ║              | 5oGin1sys6H                                ║
   ║                                                           ║
   ╚═══════════════════════════════════════════════════════════╝
```

Note that a more detailed section about the cli can be found [command line interface](../cli/cli.md).
The last line should give you the path to the default **data directory**, the command will automatically create the folder for you if it doesn't exist.

### Node configuration

We have one last step before we can run the node, we have to set up some configuration. Navigate into the **AElf.Launcher** directory:

```bash
cd AElf.Launcher/
```

This will require the information printed during the creation of the account. Open the **appsettings.json** file and edit the following sections:

The miners account:

```json
"Account":
{
    "NodeAccount": "ELF_5ta1yvi2dFE...THPHcfxMVLrLB",
    "NodeAccountPassword": "pwrd"
},
```

The node account field corresponds to the address, you also have to enter the password that you entered earlier.

```json
"InitialMiners" : [
    "04d8f8fd19cf9e3f7f84e....5cbc30bb7ccb1cc3105e557"
],
```

This is a configuration we use to specify the list of initial miners for the DPoS, for now just configure one, it's the miners public key that was printed during the account creation.

We're now ready to launch the node.

Note that if your redis server is on another host listening on a different port than the default, you will also have to configure this in the **appsettings.json**.

### Launch and test

Now we build and run the node navigate into the **aelf** directory and build the solution with the following commands:

```bash
dotnet build AElf.Launcher.csproj --configuration Release
dotnet bin/Release/netcoreapp2.2/AElf.Launcher.dll > aelf-logs.logs &
cd ..
```

You now should have a node that's running, to check this run the following command that will query the node for its current block height:

```bash
aelf-command get-blk-height -e http://127.0.0.1:1728
```

### Cleanup

To stop the node you can simply find and kill the process with:

```bash
ps -f | grep  [A]Elf.Launcher.dll | awk '{print $2}'
```

If needed you should also clean your redis database, with either of the following commands:

```bash
redis-cli FLUSHALL (clears all dbs)
```

```bash
redis-cli -n <database_number> FLUSHDB (clear a specified db)
```
