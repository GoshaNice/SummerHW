from bs4 import BeautifulSoup
import requests
import telebot
from telebot import types


class MusicFinder:
    def __init__(self,
                 song_name: str):
        singer, song = song_name.split("/")
        singer = singer.lower()
        singer = singer.replace(" ", "_")
        song = song.lower()
        song = song.replace(" ", "_")
        self._singer = singer
        self._song = song
        self._url = self._singer[0] + "/" + self._singer + "/" + self._song

    def find_original_text(self) -> str:
        url = f'https://en.lyrsense.com/{self._singer}/{self._song}'
        response = requests.get(url)
        print(response)
        if response.status_code != 200:
            return "Приношу свои извинения, я не смог найти данную песню"

        soup = BeautifulSoup(response.text, 'lxml')
        types_1 = soup.find_all("span", "puzEng")
        final_answer = ""
        for i in types_1:
            final_answer += i.text
            final_answer += "\n"
        return final_answer

    def find_translation(self) -> str:
        url = f'https://en.lyrsense.com/{self._singer}/{self._song}'
        response = requests.get(url)

        if response.status_code != 200:
            return "Приношу свои извинения, я не смог найти данную песню"

        print(response)
        soup = BeautifulSoup(response.text, 'lxml')
        types_1 = soup.find_all("p", id == "ru_text")[1]
        final_answer = ""
        for i in types_1:
            final_answer += i.text
            final_answer += "\n"
        return final_answer


bot = telebot.TeleBot("1824128367:AAEThtMft1hFinf7j74GqiSgRhERwP142ZY")


@bot.message_handler(commands=['help'])
def send_help(message):
    text_for_help = "Привет, я музбот! Я умею находить слова английских песен и их переводы! Давай Начнем?"
    bot.send_message(message.id, text=text_for_help)


@bot.message_handler(commands=['start'])
def send_keyboard(message, text="Привет, чем я могу тебе помочь?"):
    keyboard = types.ReplyKeyboardMarkup(row_width=2)  # наша клавиатура
    itembtn1 = types.KeyboardButton('Найти текст песни')  # создадим кнопку
    itembtn2 = types.KeyboardButton('Найти перевод песни')
    itembtn3 = types.KeyboardButton('Заканчиваем')
    keyboard.add(itembtn1, itembtn2)
    keyboard.add(itembtn3)
    msg = bot.send_message(message.from_user.id,
                           text=text, reply_markup=keyboard)

    # отправим этот вариант в функцию, которая его обработает
    bot.register_next_step_handler(msg, callback_worker)


def search_song(msg):
    bot.send_message(msg.chat.id, f'Операция выполняется, пытаюсь найти текст для : {msg.text}')
    song_name = msg.text
    music_bot = MusicFinder(song_name)
    txt = music_bot.find_original_text()
    print(txt)
    bot.send_message(msg.chat.id, txt)
    send_keyboard(msg, "Чем еще могу помочь?")


def search_translation(msg):
    bot.send_message(msg.chat.id, f'Операция выполняется, пытаюсь найти перевод для : {msg.text}')
    song_name = msg.text
    music_bot = MusicFinder(song_name)
    txt = music_bot.find_translation()
    print(txt)
    bot.send_message(msg.chat.id, txt)
    send_keyboard(msg, "Чем еще могу помочь?")


def callback_worker(call):
    if call.text == "Найти текст песни":
        msg = bot.send_message(call.chat.id,
                               'Давайте найдем текст песни! Напишите имя исполнителя и название песни через /. '
                               'Например: Sia/Chandelier')
        bot.register_next_step_handler(msg, search_song)
    elif call.text == "Найти перевод песни":
        msg = bot.send_message(call.chat.id,
                               'Давайте найдем перевод песни! Напишите имя исполнителя и название песни через /. '
                               'Например: Sia/Chandelier')
        bot.register_next_step_handler(msg, search_translation)
    elif call.text == "Заканчиваем":
        bot.send_message(call.chat.id, 'Хорошего дня! Когда захотите продолжнить нажмите на команду /start')


@bot.message_handler(content_types=['text'])
def handle_docs_audio(message):
    send_keyboard(message, text="Я не понимаю :-( Выберите один из пунктов меню:")


bot.polling(none_stop=True)
