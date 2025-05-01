"token":  MTM2NzU2Nzk1NDMwODM3MDQ2Mg.G9_x8O.H1jg15mzXDnw9d1BtGZVTOtTVc_xJD4_3vtqRs
new SlashCommandBuilder()
    .setName('zarat')
    .setDescription('1 ile 6 arasında zar atar'),
const commands = [
    new SlashCommandBuilder()
        .setName('yazitura')
        .setDescription('Yazı mı, tura mı?'),
    new SlashCommandBuilder()
        .setName('tkm')
        .setDescription('Taş Kağıt Makas oyna!')
        .addStringOption(option =>
            option.setName('seçim')
                .setDescription('Taş, kağıt ya da makas seç')
                .setRequired(true)
                .addChoices(
                    { name: 'Taş', value: 'taş' },
                    { name: 'Kağıt', value: 'kağıt' },
                    { name: 'Makas', value: 'makas' },
                )),
    new SlashCommandBuilder()
        .setName('zarat')
        .setDescription('1 ile 6 arasında zar atar'),
].map(command => command.toJSON());
if (interaction.commandName === 'zarat') {
    const zar = Math.floor(Math.random() * 6) + 1;
    await interaction.reply(`🎲 Zar attın ve **${zar}** geldi!`);
}
