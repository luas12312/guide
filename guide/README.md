@@ -0,0 +1,6 @@
 {
     "prefix": "/",
     "sahip": "1076528057403326576",
     TOKEN=MTM2NzU3NzQ0MDY1MzgwNzY1Ng.GHfk38._Slqv2SYDnOIHkWO_PGK2dfTrhob8vCEr1TcB8
   }
const { Client, GatewayIntentBits, SlashCommandBuilder, REST, Routes, PermissionsBitField } = require('discord.js');
require('dotenv').config();

const client = new Client({
    intents: [GatewayIntentBits.Guilds, GatewayIntentBits.GuildMembers, GatewayIntentBits.GuildMessages, GatewayIntentBits.MessageContent]
});

// Komutları tanımla
const commands = [
    new SlashCommandBuilder()
        .setName('kick')
        .setDescription('Bir kullanıcıyı sunucudan at')
        .addUserOption(option =>
            option.setName('kullanıcı')
                .setDescription('Atılacak kullanıcı')
                .setRequired(true)
        ),

    new SlashCommandBuilder()
        .setName('ban')
        .setDescription('Bir kullanıcıyı sunucudan banla')
        .addUserOption(option =>
            option.setName('kullanıcı')
                .setDescription('Banlanacak kullanıcı')
                .setRequired(true)
        ),

    new SlashCommandBuilder()
        .setName('sil')
        .setDescription('Mesajları sil')
        .addIntegerOption(option =>
            option.setName('sayı')
                .setDescription('Silinecek mesaj sayısı (1-100)')
                .setRequired(true)
        ),
].map(cmd => cmd.toJSON());

client.once('ready', async () => {
    console.log(`${client.user.tag} aktif!`);

    const rest = new REST({ version: '10' }).setToken(process.env.TOKEN);

    try {
        await rest.put(
            Routes.applicationCommands(client.user.id),
            { body: commands }
        );
        console.log('Komutlar başarıyla yüklendi.');
    } catch (error) {
        console.error(error);
    }
});

client.on('interactionCreate', async interaction => {
    if (!interaction.isChatInputCommand()) return;

    if (interaction.commandName === 'kick') {
        if (!interaction.member.permissions.has(PermissionsBitField.Flags.KickMembers)) {
            return interaction.reply({ content: '❌ Bu komutu kullanmak için yetkin yok.', ephemeral: true });
        }

        const user = interaction.options.getUser('kullanıcı');
        const member = interaction.guild.members.cache.get(user.id);
        if (!member) return interaction.reply('Kullanıcı bulunamadı.');

        try {
            await member.kick();
            await interaction.reply(`${user.tag} sunucudan atıldı.`);
        } catch (error) {
            await interaction.reply('❌ Kullanıcı atılamadı. Yeterli yetkiye sahip misin?');
        }
    }

    if (interaction.commandName === 'ban') {
        if (!interaction.member.permissions.has(PermissionsBitField.Flags.BanMembers)) {
            return interaction.reply({ content: '❌ Bu komutu kullanmak için yetkin yok.', ephemeral: true });
        }

        const user = interaction.options.getUser('kullanıcı');
        try {
            await interaction.guild.members.ban(user);
            await interaction.reply(`${user.tag} sunucudan banlandı.`);
        } catch (error) {
            await interaction.reply('❌ Kullanıcı banlanamadı. Yeterli yetkiye sahip misin?');
        }
    }

    if (interaction.commandName === 'sil') {
        if (!interaction.member.permissions.has(PermissionsBitField.Flags.ManageMessages)) {
            return interaction.reply({ content: '❌ Bu komutu kullanmak için yetkin yok.', ephemeral: true });
        }

        const amount = interaction.options.getInteger('sayı');
        if (amount < 1 || amount > 100) {
            return interaction.reply({ content: 'Lütfen 1 ile 100 arasında bir sayı gir.', ephemeral: true });
        }

        const messages = await interaction.channel.bulkDelete(amount, true).catch(err => {
            console.error(err);
            interaction.reply('❌ Mesajlar silinemedi.');
        });

        await interaction.reply({ content: `🧹 ${messages.size} mesaj silindi.`, ephemeral: true });
    }
});

client.login(process.env.TOKEN);
Spider'S Moderation Bot#1231 aktif!
