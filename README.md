# Knights' Bay Bot
This repository holds the ```docker-compose.yml``` used to run the Knights' Bay Bot for LTO node operators.
The can currently do 3 things:
1. Respond to ```/price``` and respond with the current LTO token price.
2. Respond to ```/lambo``` and display the prices of different lambos in $LTO
3. Post information automatically to your Telegram groups whenever your node(s) builds a block, gets a new lease, or gets a lease canceled.

### Example messages
```/price```:

![Price response](./img/priceResponse.PNG) 

```/lambo```:

![Lambo response](./img/lamboResponse.PNG)

```Lease info:```

![Lease info](./img/leaseInfo.PNG)

```Block info```

![Price response](./img/blockInfo.PNG)

## Installing
In order for the bot to work, you have to create your own bot using [The BotFather](https://t.me/botfather)

### Creating your own bot
1. Start a conversation with The BotFather and create a bot of your own. You can name it whatever you want (within the BotFather rules, of course), give it any profile picture and description/bio. 
2. After creating a bot, you can ask The BotFather for your bot's token. This is a security token that you need in order to control your bot. Keep this safe.
3. Now you need to know the Chat ID of the groups/chats you want the bot to post info to. The easiest way to get this, is to add the Get ID's Bot to your group:
    - Go to your group, click the 3 dots up top, and click "Add members"
    - Search for ```@getidsbot``` and add it to your group.
    - Once added, it should automatically send a message in the group with a bunch of info, including the Chat ID (should start with "-1..." if it's a group).
    - Take that Chat ID and save it, then kick the bot from the group and remove the messages.
4. Now that we have the bot token, and the Chat Id, we can start editing the ```docker-compose.yml``` file.

### Editing the ```docker-compose.yml```
The ```docker-compose.yml``` file is meant to be as easy as possible to manage. All your settings for your bot should be done in this file.
Keep in mind that indentation and spacing matters in this file! So when adding/editing the lines, make sure the spacing an indentation is kept the way it is.
1. First we declare what services we want to start. In this case, we only want to start our bot, but in some cases there are other services you'd want to start. The service can be named anything, but default is ```kbbot```.
2. Next we have the ```container_name:```, which names the container that docker starts. This can also be named whatever you want.
3. Next is the ```image:```. This specifies what image to use, stored in the docker hub. The image for this bot is ```murmeel/knights-bay-bot:latest```.
4. Lastly, we have the ```environment:```. Here goes all the settings for your bot. It's a bit scuffed, I know, but works pretty good. 
    - ```Bot__Token=``` speaks for itself, your bot token goes here.
    - ```Bot__MonitoredNodes__0__Address=``` is where you put your first (maybe only) LTO node address.
    - ```Bot__MonitoredNodes__0__ChatIdsToPostTo__0=``` is where you put a Chat Id the bot should post block and lease info to, related to the node specified in                       ```MonitoredNodes__0__Address```.
    - ```Bot__MonitoredNodes__0__ChatIdsToPostTo__1=``` is wher you put <b>another</b> Chat Id the bot should post info to, related to the node specified in                           ```MonitoredNodes__0__Address``` (```...MonitoredNodes__0``` indicates what node the chat belongs to. Add as many chat ids as you want).
    - ```Bot__MonitoredNodes__1__Address=``` is where you put your <b>second</b> (if you have it) LTO node address.
    - ```Bot__MonitoredNodes__1__ChatIdsToPostTo__0=``` is where you put a Chat Id the bot should post block and lease info to, related to the node specified in                       ```MonitoredNodes__1__Address```.
    - ```Bot__MonitoredNodes__0__PostStartedLease=``` indicates if you want the bot to post when a new lease is started for the specified node.
    - ```Bot__MonitoredNodes__0__PostCanceledLease=``` indicates if you want the bot to post when a lease is canceled for the specified node.
    - etc...
    
    In this way, you can monitor any amount of nodes, and have the bot post to any amount of chats related to that node.
    
    - ```Network__Lto__ApiUri=``` is pointing towards mainnet by default. You can point this to testnet if you want (https://testnet.lto.network/), to monitor nodes on the             testnet!
    - ```Serilog__MinimumLevel__Default=``` (optional) sets logging level. Information is default, ```Debug``` can be used for some additional logging.
    - ```Serilog__WriteTo__0__Args__path=``` (optional) sets where the log gets stored.
    
That covers the basic settings, and with that, your bot should be all ready to go. When added to your group, it will only respond to the ```/price``` and ```/lambo``` messages, nothing else (also works if you message the bot directly).

### Obtaining api key for market cap
The bot should work fine without anything else, but if you want the bot to include the market cap of LTO, you need to obtain a CoinMarketCap api key (pretty simple):
1. Go to [coinmarketcap.com/api](https://coinmarketcap.com/api/)
2. Click "Get your API key now".
3. Sign up (if you don't already have an account).
4. Follow the steps to get the api key.

After that, just add the key to the ```docker-compose.yml``` file:
    - ```Network__CoinMarketCap__ApiKey=ApiKeyGoesHere```

Done! Your ```docker-compose.yml``` file should look something like this:

```yaml
services:
  kbbot:
    container_name: knights-bay-bot
    image: murmeel/knights-bay-bot:latest
    environment:
      - Bot__Token=somenumbers:morenumbersandletters_andmore
      - Bot__MonitoredNodes__0__Address=3Jt1mBoziZmoGDaYe1UbGygGMsGS493vkgN
      - Bot__MonitoredNodes__0__ChatIdsToPostTo__0=numbersofchatid
      - Bot__MonitoredNodes__0__ChatIdsToPostTo__1=anotherchatid
      - Bot__MonitoredNodes__0__PostStartedLease=true
      - Bot__MonitoredNodes__0__PostCanceledLease=true
      - Network__Lto__ApiUri=https://nodes.lto.network/
      - Network__CoinMarketCap__ApiKey=numbersand-lettersand-somemorenumbers-andletters
      - Serilog__MinimumLevel__Default=Information
      - Serilog__WriteTo__0__Args__path=Logs/log.txt
```
