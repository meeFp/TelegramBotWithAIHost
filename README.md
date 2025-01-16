# Хостинг Telegram-бота на PythonAnywhere

Этот проект демонстрирует, как развернуть Telegram-бота на платформе PythonAnywhere с использованием Flask для обработки вебхуков и API Groq для генерации ответов. В этом README описано, как развернуть бота на PythonAnywhere и настроить его для взаимодействия с пользователями через Telegram.

## Описание

Этот бот получает сообщения от пользователей через вебхук, обрабатывает их с помощью модели Groq и отправляет ответ обратно в Telegram. Бот развернут на платформе PythonAnywhere, которая предоставляет удобную среду для хостинга Python-приложений.

## Шаги по хостингу

### Шаг 1: Создание Telegram-бота

1. Откройте Telegram и найдите **BotFather**.
2. Используйте команду `/newbot`, чтобы создать нового бота. Следуйте инструкциям.
3. Получите API токен для вашего бота (например, `"свой API токен"`).

### Шаг 2: Подготовка проекта

1. Установите необходимые библиотеки:
   ```bash
   pip install Flask telebot groq
   ```

2.Создайте файл bot.py с кодом, описанным ниже:
   ```bash
      from flask import Flask, request
      import telebot
      from groq import Groq
      import urllib3
      
      secret = ""
      
      bot = telebot.TeleBot("свой API токен")
      bot.delete_webhook()
      bot.set_webhook(
          "https://meeFp.pythonanywhere.com/{}".format(secret), max_connections=1
      )
      
      client = Groq(api_key="свой API от groq")
      
      app = Flask(__name__)
      
      
      @app.route("/{}".format(secret), methods=["POST"])
      def telegram_webhook():
          update = request.get_json()
          if "message" in update:
              chat_id = update["message"]["chat"]["id"]
              if "text" in update["message"]:
                  text = update["message"]["text"]
                  messages = [
                      {"role": "user", "content": f"{text}. "}
                  ]
                  response = client.chat.completions.create(
                      model="llama3-70b-8192", messages=messages, temperature=0
                  )
                  bot.send_message(chat_id, response.choices[0].message.content)
              else:
                  bot.send_message(chat_id, "Sorry, I didn't understand that kind of message")
          return "OK"
      
      
      bot.polling()
   ```
### Шаг 3: Развертывание на PythonAnywhere
1.Регистрация на PythonAnywhere.Перейдите на сайт ([PythonAnywhere](https://www.pythonanywhere.com/)) и зарегистрируйтесь для получения учетной записи.
