## Features

-   ⏱️ Easy to use!
-   📁 Support for all databases! (default is json)
-   ⚙️ Very customizable! (prize, duration, winners, ignored permissions, bonus entries, etc...)
-   🚀 Super powerful: start, edit, reroll, end, delete and pause giveaways!
-   💥 Events: giveawayEnded, giveawayRerolled, giveawayDeleted, giveawayReactionAdded, giveawayReactionRemoved, endedGiveawayReactionAdded
-   
## Installation

```js
npm install manage-giveaways
```

```js
const Discord = require('discord.js'),
    client = new Discord.Client({
        intents: [
            Discord.Intents.FLAGS.GUILDS,
            Discord.Intents.FLAGS.GUILD_MESSAGE_REACTIONS,
            Discord.Intents.FLAGS.GUILD_MEMBERS // Optional, for better performance
        ]
    }),
    settings = {
        prefix: 'g.',
        token: 'Discord Token Bot'
    };
// Requires Manager from discord-giveaways
const { GiveawaysManage } = require('manage-giveaways');
// Starts updating currents giveaways
const manager = new GiveawaysManage(client, {
    storage: './giveaways.json',
    default: {
        botsCanWin: false,
        embedColor: '#FF0000',
        embedColorEnd: '#000000',
        reaction: '🎉'
    }
});
// We now have a giveawaysManager property to access the manager everywhere!
client.giveawaysManager = manager;
client.on('ready', () => {
    console.log('I\'m on!');
});
client.login(settings.token);
```

### Start a giveaway

```js
client.on('interactionCreate', (interaction) => {
    const ms = require('ms');
    if (interaction.isCommand() && interaction.commandName === 'start') {
        // /start 2d 1 Awesome prize!
        // Will create a giveaway with a duration of two days, with one winner and the prize will be "Awesome prize!"
        const duration = interaction.options.getString('duration');
        const winnerCount = interaction.options.getInteger('winners');
        const prize = interaction.options.getString('prize');
        client.giveawaysManager.start(interaction.channel, {
            duration: ms(duration),
            winnerCount,
            prize
        }).then((gData) => {
            console.log(gData); // {...} (messageId, end date and more)
        });
        // And the giveaway has started!
    }
});
```

### Reroll a giveaway

```js
client.on('interactionCreate', (interaction) => {
    if (interaction.isCommand() && interaction.commandName === 'reroll') {
        const messageId = interaction.options.getString('message_id');
        client.giveawaysManager.reroll(messageId).then(() => {
            interaction.channel.send('Success! Giveaway rerolled!');
        }).catch((err) => {
            interaction.channel.send(`An error has occurred, please check and try again.\n\`${err}\``);
        });
    }
});
```

### Edit a giveaway

```js
client.on('interactionCreate', (interaction) => {
    if (interaction.isCommand() && interaction.commandName === 'edit') {
        const messageId = interaction.options.getString('message_id');
        client.giveawaysManager.edit(messageId, {
            addTime: 5000,
            newWinnerCount: 3,
            newPrize: 'New Prize!'
        }).then(() => {
            interaction.channel.send('Success! Giveaway updated!');
        }).catch((err) => {
            interaction.channel.send(`An error has occurred, please check and try again.\n\`${err}\``);
        });
    }
});
```

### Delete a giveaway

```js
client.on('interactionCreate', (interaction) => {
    if (interaction.isCommand() && interaction.commandName === 'delete') {
        const messageId = interaction.options.getString('message_id');
        client.giveawaysManager.delete(messageId).then(() => {
            interaction.channel.send('Success! Giveaway deleted!');
        }).catch((err) => {
            interaction.channel.send(`An error has occurred, please check and try again.\n\`${err}\``);
        });
    }
});
```

### End a giveaway

```js
client.on('interactionCreate', (interaction) => {
    if (interaction.isCommand() && interaction.commandName === 'end') {
        const messageId = interaction.options.getString('message_id');
        client.giveawaysManager.end(messageId).then(() => {
            interaction.channel.send('Success! Giveaway ended!');
        }).catch((err) => {
            interaction.channel.send(`An error has occurred, please check and try again.\n\`${err}\``);
        });
    }
});
```
### Pause a giveaway

```js
client.on('interactionCreate', (interaction) => {
    if (interaction.isCommand() && interaction.commandName === 'pause') {
        const messageId = interaction.options.getString('message_id');
        client.giveawaysManager.pause(messageId).then(() => {
            interaction.channel.send('Success! Giveaway paused!');
        }).catch((err) => {
            interaction.channel.send(`An error has occurred, please check and try again.\n\`${err}\``);
        });
    }
});
```

### Unpause a giveaway

```js
client.on('interactionCreate', (interaction) => {
    if (interaction.isCommand() && interaction.commandName === 'unpause') {
        const messageId = interaction.options.getString('message_id');
        client.giveawaysManager.unpause(messageId).then(() => {
            interaction.channel.send('Success! Giveaway unpaused!');
        }).catch((err) => {
            interaction.channel.send(`An error has occurred, please check and try again.\n\`${err}\``);
        });
    }
});
```

### Fetch giveaways

```js
// A list of all the giveaways
const allGiveaways = client.giveawaysManager.giveaways; // [ {Giveaway}, {Giveaway} ]
// A list of all the giveaways on the server with Id "1909282092"
const onServer = client.giveawaysManager.giveaways.filter((g) => g.guildId === '1909282092');
// A list of the current active giveaways (not ended)
const notEnded = client.giveawaysManager.giveaways.filter((g) => !g.ended);
```


### Database Supported

- MongoDb
- MySQL
- PostgreSQL
- Quick.db
- @replit/database
- Enmap