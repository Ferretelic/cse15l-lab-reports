# Lab Report 2 Servers and SSH Key (Week 3) {#week-3-lab-report}
In this blog, I have three parts, `Building a Web Server with Java`, `Connect to Server with SSH Key`, and `What I learned in CSE15L`.

## Building a Web Server with Java
In the first part, I'm going to explain how to build a web server called `ChatServer` which can receive a message and show the added messages. I would explain how we can implement it with Java, and then explain how we can use and show the examples.

### Code
This program is consisted by the two files, `Server.java`, `ChatServer.java`.
* `Server.java`: This file implements the basic elements required to launch the server, so this doesn't contain any specific implementation of chat system.
* `ChatServer.java`: This file implements how to process the received message and show it on the web.

``` java
import java.io.IOException;
import java.net.URI;

class Handler implements URLHandler {
  public String ERROR_MESSAGE =
  "You need to give the proper arguments. `/add-message?s=[message]&user=[user]`";
  int num = 0;
  public String[] messages = {};

  public String handleRequest(URI url) {
    if (url.getPath().equals("/")) {
      return listMessages();
    } else if (url.getPath().equals("/add-message")) {
      String[] parameters = url.getQuery().split("&");
      if (parameters.length >= 2) {
        if (parameters[0].startsWith("s=") && parameters[1].startsWith("user=")) {
          if (parameters[0].split("=").length >= 2 && parameters[1].split("=").length >= 2) {
            String text = parameters[0].split("=")[1];
            String user = parameters[1].split("=")[1];
            String newMessage = String.format("%s: %s", user, text);
            System.out.println(newMessage);

            String[] newMessages = new String[this.messages.length + 1];
            for (int i = 0; i < this.messages.length; i++) {
              newMessages[i] = this.messages[i];
            }
            newMessages[newMessages.length - 1] = newMessage;
            this.messages = newMessages;
            return listMessages();
          } else {
            return ERROR_MESSAGE;
          }
        } else {
          return ERROR_MESSAGE;
        }
      } else {
        return ERROR_MESSAGE;
      }
    }
    return "404 Not Found!";

  }

  public String listMessages() {
    String messageString = "";
    for (String message : this.messages) {
      messageString += message + "\n";
    }
    return messageString;
  }
}

class ChatServer {
  public static void main(String[] args) throws IOException {
    if (args.length == 0) {
      System.out.println("Missing port number! Try any number between 1024 to 49151");
      return;
    }

    int port = Integer.parseInt(args[0]);

    Server.start(port, new Handler());
  }
}

```

## What I learned in CSE15L
