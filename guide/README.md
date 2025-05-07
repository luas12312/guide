atlantis-bot/
├── commands/
│   └── eglen.js  ← Tüm komutlar burada
├── deploy-commands.js
├── index.js
├── .env
├── .gitignore
└── package.json
{
  "name": "atlantis-bot",
  "version": "1.0.0",
  "description": "Atlantis eğlence botu",
  "main": "index.js",
  "scripts": {
    "start": "node index.js",
    "deploy": "node deploy-commands.js"
  },
  "dependencies": {
    "discord.js": "^14.11.0",
    "dotenv": "^16.0.3"
  }
}

TOKEN=bot-MTM2OTc5NDczNzA1NzU2Njk2MQ.GPQsZe.CRG3p8gSoq_uNUH8sLPgMmb3MFwFNR9Dq8L5GE
CLIENT_ID=1369794737057566961
GUILD_ID=1369759947918938286
.env
node_modules
const { SlashCommandBuilder, PermissionFlagsBits } = require('discord.js');

module.exports = {
  data: new SlashCommandBuilder()
    .setName('eglen')
    .setDescription('Atlantis Bot ile eğlen!')
    .addSubcommand(sub =>
      sub.setName('şaka')
        .setDescription('Rastgele bir şaka yapar.'))
    .addSubcommand(sub =>
      sub.setName('zar')
        .setDescription('1 ile 6 arasında zar atar.'))
    .addSubcommand(sub =>
      sub.setName('tokat')
        .setDescription('Bir kullanıcıya tokat atar.')
        .addUserOption(option =>
          option.setName('hedef')
            .setDescription('Tokat atılacak kullanıcı')
            .setRequired(true)
        )
    ),
    
  async execute(interaction) {
    const subcommand = interaction.options.getSubcommand();

    if (subcommand === 'şaka') {
      const şakalar = [
        "Bilgisayar neden çöktü? Çünkü çok yorgundu.",
        "404: Şaka bulunamadı.",
        "Botlar kahve içmez ama enerjisi hiç bitmez!"
      ];
      const rastgele = şakalar[Math.floor(Math.random() * şakalar.length)];
      await interaction.reply(rastgele);
    }

    if (subcommand === 'zar') {
      const zar = Math.floor(Math.random() * 6) + 1;
      await interaction.reply(`🎲 Zar sonucu: **${zar}**`);
    }

    if (subcommand === 'tokat') {
      const hedef = interaction.options.getUser('hedef');
      await interaction.reply(`${hedef} 👋 Tokatlandı!`);
    }
  }
};
const fs = require('node:fs');
const path = require('node:path');
const { Client, Collection, GatewayIntentBits } = require('discord.js');
require('dotenv').config();

const client = new Client({
  intents: [GatewayIntentBits.Guilds]
});

client.commands = new Collection();

const commandsPath = path.join(__dirname, 'commands');
const commandFiles = fs.readdirSync(commandsPath).filter(file => file.endsWith('.js'));

for (const file of commandFiles) {
  const filePath = path.join(commandsPath, file);
  const command = require(filePath);
  client.commands.set(command.data.name, command);
}

client.once('ready', () => {
  console.log(`🌊 Atlantis Bot aktif: ${client.user.tag}`);
});

client.on('interactionCreate', async interaction => {
  if (!interaction.isChatInputCommand()) return;

  const command = client.commands.get(interaction.commandName);
  if (!command) return;

  try {
    await command.execute(interaction);
  } catch (error) {
    console.error(error);
    await interaction.reply({ content: 'Komutu işlerken bir hata oluştu.', ephemeral: true });
  }
});

client.login(process.env.TOKEN);
const { REST, Routes } = require('discord.js');
const fs = require('node:fs');
const path = require('node:path');
require('dotenv').config();

const commands = [];
const commandsPath = path.join(__dirname, 'commands');
const commandFiles = fs.readdirSync(commandsPath).filter(file => file.endsWith('.js'));

for (const file of commandFiles) {
  const command = require(path.join(commandsPath, file));
  commands.push(command.data.toJSON());
}

const rest = new REST({ version: '10' }).setToken(process.env.TOKEN);

(async () => {
  try {
    console.log('⌛ Slash komutlar yükleniyor...');
    await rest.put(
      Routes.applicationGuildCommands(process.env.CLIENT_ID, process.env.GUILD_ID),
      { body: commands }
    );
    console.log('✅ Komutlar yüklendi (Atlantis hazır)!');
  } catch (error) {
    console.error(error);
  }
})();
npm install
npm run deploy
npm start
