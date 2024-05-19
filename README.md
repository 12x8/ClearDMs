# 🗑️ ClearDM
Script pour supprimer vos DM ou dans un channel

```js
import { Client } from 'discord.js-selfbot-v13';
const config = require('./config.json');
const token = config.token;
const prefix = config.prefix;
import 'colors';

const client = new Client({
    ws: { properties: { $browser: "Discord iOS" } },
});

client.on('ready', () => {
    console.log(`Le selfbot est connecté ✔\n` .green);
});

client.on('messageCreate', async (message) => {
    if (!message.author.bot && message.content.startsWith(prefix)) {
        const args = message.content.slice(prefix.length).trim().split(/ +/);
        const command = args.shift()?.toLowerCase();

        if (command === 'clear') {
            let limit: number | undefined = parseInt(args[0]);
            if (isNaN(limit)) limit = undefined;

            let success = 0;
            let failed = 0;

            try {
                const messages = await message.channel.messages.fetch({ limit: 100 });
                await Promise.all(messages.map(async (msg) => {
                    if (msg.author.id === client.user?.id) {
                        try {
                            await msg.delete();
                            success++;
                        } catch {
                            failed++;
                        }
                    }
                }));
                console.log(`- [`.white + ` ${success} `.green + `]`.white + ` Messages ont été supprimés`.green + `\n` +  `- [`.white + ` ${failed} `.red + `]`.white + ` ont échoué (car c'est peut etre pas vos messages).\n`.red);
            } catch (error) {
                console.error('Erreur : '.red, error);
            }
        }
    }
});

client.login(token);
```
