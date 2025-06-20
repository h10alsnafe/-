import logging
from telegram import Update
from telegram.ext import Updater, CommandHandler, CallbackContext
import datetime

# بيانات البوت
TOKEN = "8158185592:AAF9dTVlbrLa6KiMQxzCZJFDKDUdb4ZQhTM"
CHAT_ID = "@alshamk_For_matches_bot"

# لوجات التتبع
logging.basicConfig(format='%(asctime)s - %(name)s - %(levelname)s - %(message)s', level=logging.INFO)

# دالة مباريات اليوم (بيانات وهمية حاليًا)
def get_today_matches():
    today = datetime.datetime.now().strftime('%Y-%m-%d')
    matches = [
        {"home": "ريال مدريد", "away": "برشلونة", "time": "22:00"},
        {"home": "الهلال", "away": "الاتحاد", "time": "20:30"},
        {"home": "مانشستر سيتي", "away": "آرسنال", "time": "19:45"},
    ]

    msg = f"📅 مباريات اليوم {today}:\n\n"
    for match in matches:
        msg += f"🕘 {match['time']} - {match['home']} 🆚 {match['away']}\n"
    return msg

# أمر /start
def start(update: Update, context: CallbackContext):
    update.message.reply_text("مرحباً بك في بوت الشامخ للمباريات!⚽\nاكتب /matches لعرض مباريات اليوم.")

# أمر /matches
def matches_command(update: Update, context: CallbackContext):
    message = get_today_matches()
    update.message.reply_text(message)

# الجدولة التلقائية
def scheduled_job(context: CallbackContext):
    message = get_today_matches()
    context.bot.send_message(chat_id=CHAT_ID, text=message)

# تشغيل البوت
def main():
    updater = Updater(TOKEN, use_context=True)
    dp = updater.dispatcher

    # الأوامر
    dp.add_handler(CommandHandler("start", start))
    dp.add_handler(CommandHandler("matches", matches_command))

    # جدولة يومية الساعة 12:00
    updater.job_queue.run_daily(scheduled_job, time=datetime.time(hour=12, minute=0))

    updater.start_polling()
    updater.idle()

if __name__ == '__main__':
    main()
