import logging
import datetime
import telebot
logging.basicConfig(level=logging.INFO, filename="Gosha_log.log", filemode = "w")
bot = telebot.TeleBot("TOKEN")
WFI = 1 #Waiting For Input

logging.info(str(datetime.datetime.now()) + ": Initiated new session of Gosha v1.1.0.")
print(str(datetime.datetime.now()) + ": Initiated new session of Gosha v1.1.0.")

helpmessage = "Привет! Я Гоша.\nЯ пока не имею функционала, но меня уже учат общаться.\nКстати, если ты хочешь написать письмо нашей команде - используй команду /suggestion"
@bot.message_handler(commands=['start', 'help'])
def send_welcome(message):
	logging.info(str(datetime.datetime.now()) + ": Help request from ID " + str(message.from_user.id))
	print(str(datetime.datetime.now()) + ": Help request from ID " + str(message.from_user.id))
	bot.reply_to(message, helpmessage)

sugg_inpt_stats = {}

@bot.message_handler(commands=["suggestion"])
def suggestion_init(message):
	sugg_inpt_stats[message.from_user.id] = WFI;
	bot.send_message(message.from_user.id, "Я очеь рад, что ты хочешь что-то предложить нашей команде!\nНапиши совё пожелание/идею/новость:")
	logging.info(str(datetime.datetime.now()) + ": Initiated suggestion. ID " + str(message.from_user.id))
	print(str(datetime.datetime.now()) + ": Initiated suggestion. ID " + str(message.from_user.id))
	
@bot.message_handler(func=lambda message: sugg_inpt_stats.get(message.from_user.id)==WFI)
def suggestion(message):
	bot.reply_to(message, "Принял!")
	del sugg_inpt_stats[message.from_user.id]
	if str(message.from_user.last_name)=="None":
		name = str(message.from_user.first_name)
	else:
		name = str(message.from_user.first_name) + " " + str(message.from_user.last_name)
	sugg = "Ник: " + str(name) + "\nЮзернейм: @" + str(message.from_user.username) + "\nID: " + str(message.from_user.id) + "\nПисьмо: \n" + message.text
	bot.send_message("@GROUP", sugg)
	logging.info(str(datetime.datetime.now()) + ": Suggestion from ID " + str(message.from_user.id) + ". Text:\n" + message.text)
	print(str(datetime.datetime.now()) + ": Suggestion from ID " + str(message.from_user.id) + ". Text:\n" + message.text)

@bot.message_handler(func=lambda message: True)
def default(message):
	bot.send_message(message.from_user.id, "Хэй, я тебя пока не понимаю :/ \nСкоро мы это решим!")
	logging.info(str(datetime.datetime.now()) + ": Common message from ID " + str(message.from_user.id) + ". Text:\n" + message.text)
	print(str(datetime.datetime.now()) + ": Common message from ID " + str(message.from_user.id) + ". Text:\n" + message.text)
	#bot.answer_callback_query(message.id, text="Хэй, я тебя пока не понимаю :/ \nСкоро мы это решим!")

bot.infinity_polling()
