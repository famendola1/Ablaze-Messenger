# Random Messenger

## Description
Are you tired of your messaging apps having all these fancy features and options, and just want get back to classic messaging? Do you want to expand your social network and connect with people from all over the world? Well, Random Messenger is the messenger app for you. Random Messenger allows user to chat with random strangers with just the click of a button. The simple interface removes all the distracting options like images, documents, emoji's, stickers, etc. and is strickly only text. This way there is no need to decipher a sequence of emojis or accidentally sending a random sticker. This app is also semi-anonymous, as your username could be whatever you want (i.e. your actual name or a screenname), but profile pictures are replaced with a box filled with the color of your choice.

## Minimum Viable Product
The MVP for this application should allow users to sign up, login, and logout and send a message to a random user. The MVP should also be able to display the messages sent between users and list the conversations that the user is a part of. Also, the MVP should allow a user to coninue a conversation that already existed.

## Requirements

* [Ruby 2.3+](https://www.ruby-lang.org/en/)
* [Rails 5+](http://rubyonrails.org)

## Run on Localhost
You will need to open two terminals. In one type the command ```rackup private_pub.ru -s thin -E production```. This is for the publish/subscribe flow for the messages. In the other terminal run:
* ```bundle install```, installs the necessary Ruby gems if not installed
* ```rake assets:precompile```, precomiles javascript, stylesheets, etc.
* ```rake db:reset```, clears and then seeds the database with the information specified in db/seed.rb.

Now, to use the app, run ```rails server``` and navigate to localhost:3000 in your browser of choice.

## Functionality
### Sign Up
To create an account, click the sign up link. You will be prompted to enter your name, email, favorite color (or any color) in hex, and your password. You will see a confirmation on the page that you created an account.

![](signup.png)

### Login
You can login by entering the email and password you created the account with or with one of the already created accounts specified in the db/seed.rb. You will see a confirmation on the page that you are logged in.

![](login.png)

### Logout
Click the logout link to logout. You will see a confirmation on the page that you logged out.

![](logout.png)

### Send Message
To send a message click the big green button and a chat box will appear to message a randomly selected user that isn't you. If the database isn't seeded you will not be able to send a message because htere won't be any other users.

![](message.png)

### Conversations
All your conversations appear on the main page and clicking on the conversation will open a chat box showing the messages between you and that user.

![](convo.png)

## Architecture
### User
I used a ruby gem called [devise](https://github.com/plataformatec/devise) to handle the user data. The user data is collected through a form on the sign up page. The data is then stored in the database with columns that are predefined by devise. I added two columns to the database, name and color, to store the values that were given in the form since they weren't predefined by devise. A user also has a has_many relationship with conversations.

### Messages
Messages are created when the user submit's the form in the chat box, which is just the text area at the bottom. Messages are stored in the database with the following columns:

* body, text of message
* conversation_id (belongs_to relationship)
* user_id (belongs_to relationship)
* created_at, time message was created
* updated_at, time message was created
* read, if meassage was read or not

Mesages also validate the presence of a body, conversation_id, and user_id (i.e. A message existing means that it's body, conversation_id, and user_id also exist).

### Conversations
Conversations are created when the user clicks the send message button. Conversations are stored in the database with the following columns:

* sender_id (belongs_to relationship), first person to send message in conversation
* recipient_id (belongs_to relationship), person who receives the message first in the conversation
* created_at, time conversation was created
* updated_at, time conversation was updated

Conversations have a has_many relationship with messages and they validate the uniqueness of sender_id within the scope of the recipient (i.e. each conversation where the user is a recipient has a unique sender_id).

## Known Issues
* Chat boxes don't resize when there are too many to fit on the screen
    - I considered making the chat box sizes dynamic, but if there are too many chat boxes open then each one would be too         small. Currently, at full screen, five chat boxes can be open without this issue. My assumption is that the user is           unlikely to need all five open at once and would be able to close some if this issue arises.
* New conversations don't appear on page when you send the first message without reloading
    - I figure that this isn't a problem for the person sending the first message since the chat box will already be open.         The receiver of the first message will not see the new conversation without a refresh.
* User only sees new message when chat box is open without refreshing
    - If a chat box is not open when the user receives a message, they don't know they have a new message without refreshing.       A fix I considered was a notification system for messages or simply updating the conversation on the main page to show       a new message
 
 
 ## References
 * Extended messaging logic from this tutorial: http://josephndungu.com/tutorials/gmail-like-chat-application-in-ruby-on-rails
 * Styling and javascript logic used from:
     - http://anantgarg.com/2009/05/13/gmail-facebook-style-jquery-chat
     - http://css-tricks.com/replicating-google-hangouts-chat/
