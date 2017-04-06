---
title: Send and receive attachments | Microsoft Docs
description: Learn how to send and receive messages containing attachments such as images in a conversational application (bot).
keywords: bot framework, bot, attachment, images, send messages
author: DeniseMak
manager: rstand
ms.topic: article
ms.prod: bot-framework

ms.date: 02/24/2017
ms.reviewer:
#ROBOTS: Index
---

# Send and receive attachments using the Bot Builder SDK for Node.js

In this article, we'll discuss how to send messages containing attachments such as images by using the Bot Builder SDK for Node.js.

The [session.send()][SessionSend] method can be used to send messages in the form of a JSON object to the user, in addition to strings. 
The message object is expected to be an instance of an [IMessage][IMessage] and it's most useful to send the user a message as an object when you’d like to include an attachment like an image. 

<!-- TODO: Supported attachments table -->

## Send and receive an image attachment

The following example checks to see if the user has sent an attachment, and if they have, it will echo back any image contained in the attachment. You can test this with the Bot Framework Emulator by sending your bot an image.


```javascript
// Create your bot with a function to receive messages from the user
var bot = new builder.UniversalBot(connector, function (session) {
    var msg = session.message;
    if (msg.attachments && msg.attachments.length > 0) {
     // Echo back attachment
     var attachment = msg.attachments[0];
        session.send({
            text: "You sent:",
            attachments: [
                {
                    contentType: attachment.contentType,
                    contentUrl: attachment.contentUrl,
                    name: attachment.name
                }
            ]
        });
    } else {
        // Echo back users text
        session.send("You said: %s", session.message.text);
    }
});

```


## Additional resources

* [IMessage][IMessage]
* [Send a rich card][SendRichCard]
* [session.send][SessionSend]

[IMessage]: http://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.imessage
[SendRichCard]: send-card-buttons.md
[SessionSend]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.session.html#send