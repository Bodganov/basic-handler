# HANDLER BÁSICO PARA INICIOS DE UN BOT.

## Handler para iniciar un bot totalmente desde 0. 
```js
const Discord = require('discord.js');
const client = new Discord.Client({ intents: 32767 });

const { readdirSync } = require('fs');
const path = require('path');

require('dotenv').config();

client.slash = new Discord.Collection();
client.data = [];

const commands = readdirSync(path.join(__dirname, 'commands'))
for(const folders of commands){
    const folder = readdirSync(path.join(__dirname, 'commands', folders));
    for(const file of folder){
        const cmd = require(path.join(__dirname, 'commands', folders, file));
        client.slash.set(cmd.name, cmd);
        client.data.push(cmd.data.toJSON());
        console.log(`Commands - ${folders}/${file}(${cmd.name}) loaded`);
    }
}

const events = readdirSync(path.join(__dirname, 'events'));
for(const folders of events){
    const folder = readdirSync(path.join(__dirname, 'events', folders));
    for(const file of folder){
        const event = require(path.join(__dirname, 'events', folders, file));
        client.on(event.name, (...args) => event.run(client, ...args));
        console.log(`Events - ${folders}/${file}(${event.name}) Loaded.`);
    }
}

client.login(process.env.TOKEN);
```

# Ejemplo de como debe estar organizadas las carpetas, de eventos y comandos. y de como se usa el handler de comandos.
![image](https://user-images.githubusercontent.com/64504421/175132259-790fa0b6-d301-413c-a4c9-8e2abb7b4a97.png)

## Ejemplo para usar el handler de eventos.
```js
module.exports = {
  name: 'ready', 

  run: async (client) => {
    connect(process.env.URLMONGOOSE, {
      useNewUrlParser: true,
      useUnifiedTopology: true
    }).then(() => { 
      console.log(`DataBase connect.`); 
    }).catch(() => {
      console.log(`Error in load of DataBase`);
    });
    
    console.log(`Logeado correctamente como ${client.user.username} (${client.user.id})`);
    try {
      await client.application?.commands.set(client.data);
    } catch(e) { console.error(e) }
  }
}
```

### Dependencias utilizadas:
discord.js, dotenv, fs, path.
