# Mobitoimport logging import re from telegram import Update from telegram.ext import ApplicationBuilder, MessageHandler, filters, ContextTypes

Вставь свой токен

TOKEN = "7877601947:AAFX-1Hi1dPJaWSLEwA4C5OMRTSOjagK_oQ"

ID твоей группы (замени на свой ID)

GROUP_ID = -1001234567890

Логирование

logging.basicConfig(level=logging.INFO) logger = logging.getLogger(name)

REQUIRED_KEYWORDS = ["модель", "цена", "обмен", "город", "контакт"]

def get_missing_keywords(text): text_lower = text.lower() return [word for word in REQUIRED_KEYWORDS if word not in text_lower]

async def handle_message(update: Update, context: ContextTypes.DEFAULT_TYPE): message = update.message if not message: return

has_photo = bool(message.photo)
text = message.caption if has_photo else message.text or ""

if has_photo:
    missing = get_missing_keywords(text)
    if not missing:
        await context.bot.send_photo(
            chat_id=GROUP_ID,
            photo=message.photo[-1].file_id,
            caption=text
        )
    else:
        await message.reply_text(
            "Пожалуйста, укажите следующие поля — иначе объявление не будет опубликовано:\n" +
            "\n".join(f"— {word}" for word in missing)
        )
else:
    await context.bot.send_message(chat_id=GROUP_ID, text=text)

if name == "main": app = ApplicationBuilder().token(TOKEN).build() app.add_handler(MessageHandler(filters.ALL, handle_message)) app.run_polling()

