import os
from telegram import Update
from telegram.ext import ApplicationBuilder, CommandHandler, MessageHandler, filters, ContextTypes

BOT_TOKEN = os.environ.get("BOT_TOKEN")
OWNER_ID = int(os.environ.get("OWNER_ID"))

user_data = {}

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    chat_id = update.effective_chat.id
    user_data[chat_id] = {"step": "familiya"}
    await context.bot.send_message(chat_id=chat_id, text="📛Familiyangizni kiriting:")

async def handle_message(update: Update, context: ContextTypes.DEFAULT_TYPE):
    chat_id = update.effective_chat.id
    text = update.message.text

    if chat_id not in user_data:
        await start(update, context)
        return

    step = user_data[chat_id]["step"]

    if step == "familiya":
        user_data[chat_id]["familiya"] = text
        user_data[chat_id]["step"] = "ism"
        await update.message.reply_text("📛Ismingizni kiriting:")
    elif step == "ism":
        user_data[chat_id]["ism"] = text
        user_data[chat_id]["step"] = "sharif"
        await update.message.reply_text("📛Otangizning ismini kiriting:")
    elif step == "sharif":
        user_data[chat_id]["sharif"] = text
        user_data[chat_id]["step"] = "yosh"
        await update.message.reply_text("🔞Yoshingizni kiriting:")
    elif step == "yosh":
        user_data[chat_id]["yosh"] = text
        user_data[chat_id]["step"] = "viloyat"
        await update.message.reply_text("🌆Viloyatingizni kiriting:")
    elif step == "viloyat":
        user_data[chat_id]["viloyat"] = text
        user_data[chat_id]["step"] = "shahar"
        await update.message.reply_text("🌆Tuman/shahringizni kiriting:")
    elif step == "shahar":
        user_data[chat_id]["shahar"] = text
        user_data[chat_id]["step"] = "manzil"
        await update.message.reply_text("📍Yashash manzilingizni kiriting:")
    elif step == "manzil":
        user_data[chat_id]["manzil"] = text
        user_data[chat_id]["step"] = "maktab"
        await update.message.reply_text("🏫Maktab nomini kiriting:")
    elif step == "maktab":
        user_data[chat_id]["maktab"] = text
        await update.message.reply_text("✅ Ma'lumotlar qabul qilindi! Rahmat.")

        # Adminga yuborish
        info = user_data[chat_id]
        msg = f"""📥 Yangi foydalanuvchi:
📛 Familiya: {info['familiya']}
📛 Ism: {info['ism']}
📛 Sharif: {info['sharif']}
🔞 Yosh: {info['yosh']}
🌆 Viloyat: {info['viloyat']}
🌆 Shahar/Tuman: {info['shahar']}
📍 Manzil: {info['manzil']}
🏫 Maktab: {info['maktab']}"""

        await context.bot.send_message(chat_id=OWNER_ID, text=msg)
        del user_data[chat_id]

app = ApplicationBuilder().token(BOT_TOKEN).build()
app.add_handler(CommandHandler("start", start))
app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, handle_message))

print("Bot ishlayapti...")
app.run_polling()
