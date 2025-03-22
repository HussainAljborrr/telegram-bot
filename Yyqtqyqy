import configparser
from pyrogram import Client, filters
from pyrogram.types import InlineKeyboardMarkup, InlineKeyboardButton

# 🔹 تحميل الإعدادات من config.ini
config = configparser.ConfigParser()
config.read("config.ini")

API_ID = int(config["telegram"]["api_id"])  
API_HASH = config["telegram"]["api_hash"]  
BOT_TOKEN = config["telegram"]["bot_token"]  

# 🔹 تشغيل حساب تيليجرام الشخصي (Userbot)
app = Client("my_account", api_id=API_ID, api_hash=API_HASH)

# 🔹 إعدادات القنوات (يمكن تغييرها لاحقًا)
SOURCE_CHANNEL = -1001234567890  # معرف القناة الخاصة
DESTINATION_CHANNEL = -1009876543210  # معرف القناة الوجهة

user_state = {}

# 🔹 نسخ الرسائل من القناة الخاصة وإرسالها للقناة الوجهة
@app.on_message(filters.chat(SOURCE_CHANNEL))
async def forward_messages(client, message):
    await message.copy(DESTINATION_CHANNEL)

# 🔹 عرض الإعدادات بأزرار
@app.on_message(filters.command("settings"))
async def show_settings(client, message):
    keyboard = InlineKeyboardMarkup([
        [InlineKeyboardButton("🔄 تغيير القناة المصدر", callback_data="change_source")],
        [InlineKeyboardButton("🔄 تغيير القناة الوجهة", callback_data="change_destination")],
    ])
    await message.reply_text("⚙️ إعدادات البوت:\nاختر العملية التي تريد تنفيذها:", reply_markup=keyboard)

@app.on_callback_query(filters.regex("change_source"))
async def change_source_callback(client, callback_query):
    user_state[callback_query.from_user.id] = "change_source"
    await callback_query.message.edit_text("✏️ أرسل معرف القناة الخاصة الجديدة.")

@app.on_callback_query(filters.regex("change_destination"))
async def change_destination_callback(client, callback_query):
    user_state[callback_query.from_user.id] = "change_destination"
    await callback_query.message.edit_text("✏️ أرسل معرف القناة الوجهة الجديدة.")

@app.on_message(filters.text)
async def handle_user_input(client, message):
    user_id = message.from_user.id

    if user_id in user_state:
        action = user_state[user_id]

        try:
            new_channel = int(message.text)

            global SOURCE_CHANNEL, DESTINATION_CHANNEL
            if action == "change_source":
                SOURCE_CHANNEL = new_channel
                await message.reply_text(f"✅ تم تغيير القناة المصدر إلى: {new_channel}")
            elif action == "change_destination":
                DESTINATION_CHANNEL = new_channel
                await message.reply_text(f"✅ تم تغيير القناة الوجهة إلى: {new_channel}")

            del user_state[user_id]

        except ValueError:
            await message.reply_text("❌ يرجى إرسال معرف قناة صالح!")

print("✅ البوت يعمل الآن...")
app.run()
