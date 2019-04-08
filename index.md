<body>
<img src="172.jpg"/>
            botkit.createConversation(this, message, cb);
        };


        bot.send = function(message, cb) {

            var facebook_message = {
                recipient: {},
            if (typeof(message.channel) == 'string' && message.channel.match(/\+\d+\(\d\d\d\)\d\d\d\-\d\d\d\d/)) {
                facebook_message.recipient.phone_number = message.channel;
