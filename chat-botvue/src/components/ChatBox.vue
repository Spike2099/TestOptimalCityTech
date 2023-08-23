<template>
    <div class="chatbox-container">
        <div class="container">
            <h1>Vue Chat Bot</h1>
            <div class="messageBox mt-8">
                <template v-for="(message, index) in messages" :key="index">
                    <div :class="message.from == 'user' ? 'messageFromUser' : 'messageFromChatGpt'">
                        <div :class="message.from == 'user' ? 'userMessageWrapper' : 'chatGptMessageWrapper'">
                            <!-- Аватарка бота -->
                            <div class="avatar" v-if="message.from != 'user'"></div>
                            <div :class="message.from == 'user' ? 'userMessageContent' : 'chatGptMessageContent'">
                                <div v-if="message.loading" class="loadingDots"></div>
                                <div v-else>{{ message.data }}</div>
                            </div>
                        </div>
                    </div>
                </template>
            </div>
            <div class="inputContainer">
                <input v-model="currentMessage" type="text" class="messageInput" placeholder="Задай мне вопрос..."
                    @keyup.enter="sendMessage" />
                <button @click="sendMessage" class="askButton">Вопрос</button>
            </div>
            <div class="optionsContainer">
                <div class="optionsTitle">Выберите действие:</div>
                <div class="options">
                    <button v-for="(option, index) in options" :key="index" @click="sendOption(option)"
                        class="optionButton">{{ option }}</button>
                </div>
            </div>
        </div>
    </div>

</template>

<style src="./chat-widget-styles.css" scoped></style>

<script>
export default {
    data() {
        return {
            messages: [],
            currentMessage: "",
            options: [
                "Заказать пиццу",
                "Установить будильник",
                "Вывести погоду"
            ]
        };
    },
    mounted() {
        // Приветственное сообщение при запуске приложения
        this.messages.push({
            from: "chat",
            data: "Привет! Я умею заказывать пиццу, заводить будильник и выводить информацию о погоде, чего пожелаете?"
        });
    },
    methods: {
        sendMessage() {
            const message = this.currentMessage.trim();
            if (message === "") return;

            this.currentMessage = "";

            this.processUserMessage(message); // Функция для обработки сообщения пользователя
            this.scrollChatToLastMessage();
        },
        processUserMessage(message) {
            if (this.options.includes(message)) {
                this.sendOption(message);
            } else {
                this.messages.push({
                    from: "user",
                    data: message
                });
                
                const response = "Извините, я не понимаю этот запрос. Пожалуйста, выберите одно из доступных действий.";
                
                setTimeout(() => {
                    this.messages.push({
                        from: "chat",
                        data: response
                    });
                }, 500);
            }
           

        },
        sendOption(option) {
            this.messages.push({ from: "user", data: option });

            let response = "";
            switch (option) {
                case "Заказать пиццу":
                    response = "Хорошо, я заказываю пиццу. Что еще могу сделать?";
                    break;
                case "Установить будильник":
                    response = "Ок, будильник установлен. Что еще могу сделать?";
                    break;
                case "Вывести погоду":
                    response = "Сейчас я покажу погоду. Что еще могу сделать?";
                    break;
                default:
                    response = "Не понял вашего запроса. Что еще могу сделать?";
                    break;
            }
            
            setTimeout(() => {
                this.messages.push({
                    from: "chat",
                    data: response
                });
            }, 500);
        }
    }

};
</script>