# hello-world

import telebot
from telebot import types

# 1545343924:AAGXYY10LmFiNZK8_zYV6QRW8QPXiL70TK8

name = ''
surname = ''
age = 0

bot = telebot.TeleBot("1545343924:AAGXYY10LmFiNZK8_zYV6QRW8QPXiL70TK8", parse_mode=None)


@bot.message_handler(commands=['start', 'help'])
def send_welcome(message):
    bot.reply_to(message, "Howdy, how are you doing?")


@bot.message_handler(func=lambda m: True)
def echo_all(message):
    if message.text == 'Привет':
        bot.reply_to(message, 'Привет создатель бота!')
    elif message.text == 'hi':
        bot.reply_to(message, 'Hi again! The bot creator!')
    elif message.text == '/reg':
        bot.send_message(message.from_user.id, "Привет! Давай познакомимся! Как тебя зовут?")
        bot.register_next_step_handler(message, reg_name)
    # bot.reply_to(message, message.text)


def reg_name(message):
    global name
    name = message.text
    bot.send_message(message.from_user.id, "Какая у Вас фамилия?")
    bot.register_next_step_handler(message, reg_surname)


def reg_surname(message):
    global surname
    surname = message.text
    bot.send_message(message.from_user.id, "Сколько Вам лет?")
    bot.register_next_step_handler(message, reg_age)


def reg_age(message):
    global age
    # age = message.text
    while age == 0:
        try:
            age = int(message.text)
        except Exception:
            bot.send_message(message.from_user.id, "Вводите цифрами!")

    keyboard = types.InlineKeyboardMarkup()
    key_yes = types.InlineKeyboardButton(text='Да', callback_data='yes')
    keyboard.add(key_yes)
    key_no = types.InlineKeyboardButton(text='Нет', callback_data='no')
    keyboard.add(key_no)

    question = "Тебе " + str(age) + " лет? И тебя зовут " + name + ' ' + surname + '?'
    bot.send_message(message.from_user.id, text = question, reply_markup=keyboard)

@bot.callback_query_handler(func=lambda call: True)
def calback_worker(call):
    if call.data == 'yes':
        bot.send_message(call.message.chat.id, "Приятно познакомится, " + name)
    elif call.data == 'no':
        bot.send_message(call.messagr.chat.id, 'Попробуй ещё раз!')
        bot.send_message(call.messagr.chat.id, "Привет! Давай познакомимся! Как тебя зовут?")
        bot.register_next_step_handler(call.messagr.chat, reg_name)




bot.polling()
