## Welcome to GitHub Pages
CoffeeBot Lab Instructions
CoffeeBot is a transactional chat bot that can help one order a mocha online.
Consider this conversation:
User: May I have a mocha?
CoffeeBot: What size? small, medium, large?
User: small
CoffeeBot: Would you like that iced or hot?
User: hot
CoffeeBot: You'd like me to order a small mocha. Is that right?
User: Make it a large mocha
CoffeeBot: You'd like me to order a large mocha. Is that right?
User: yeah
CoffeeBot: Great! Your mocha will be available for pickup soon. Thanks for using CoffeeBot!
First, we'll create the Amazon Lex bot. Then, we'll add some Lambda Functions to bring it to life. 
Amazon Lex bot
1. Create bot
1.	From the Amazon Lex console, create a Custom bot with these settings (you can see these in the "Settings" tab later)
o	Bot name: CoffeeBot
	To work independently in a shared environment, use your initials in the name (e.g., CoffeeBotXXX)
o	Session timeout: 5 min
o	IAM role: (accept the default) AWSServiceRoleForLexBots
o	COPPA: (our bot is not directed at children) No
2.	Review the Error handling settings
o	Prompts: (one prompt) Sorry, but I didn't understand that. Would you try again, please?
o	Maximum number of retries: 2
o	Hang-up phrase: (one phrase) Sorry, I could not understand. Goodbye.
2. Create Order Beverage Intent
From the left, add a new Intent called cafeOrderBeverageIntent with the following settings and click "Save Intent" to save the Intent.
To work independently in a shared environment, use your initials in the Intent name (e.g., cafeOrderBeverageIntentXXX).
1.	Lambda initialization and validation (leave unchecked)
2.	Fulfillment: choose "Return parameters to client" for now
3.	Confirmation prompt: You'd like me to order a {BeverageSize} {BeverageType}. Is that right? to confirm and Okay. Nothing to order this time. See you next time! to cancel.
4.	Sample Utterances: add these to the list of sample utterances so the bot recognizes similar phrases (each entry on a separate line)
I would like a {BeverageSize} {BeverageType}
Can I get a {BeverageType}
May I have a {BeverageSize} {Creamer} {BeverageType}
Can I get a {BeverageSize} {BeverageTemp} {Creamer} {BeverageType}
Let me get a {BeverageSize} {Creamer} {BeverageType}
3. Create Slot types
Add the following Slot types (each value should be a separate entry); remember to "Save slot type" as you go along. To work independently in a shared environment, use your initials in the names (e.g., cafeBeverageTypeXXX).
Note: Although they are saved with the AWS Account, Slot Types will only show up in the list when they are associated in the next step.
Slot type name	Description	Values (each entry on a separate line)
cafeBeverageType	Slot types are shared at the account level so text would help other developers determine if they can reuse this Slot type.	coffee; cappuccino; latte; mocha; chai; espresso; smoothie
cafeBeverageSize		kids; small; medium; large; extra large; six ounce; eight ounce; twelve ounce; sixteen ounce; twenty ounce
cafeCreamerType		two percent; skim milk; soy; almond; whole; skim; half and half
cafeBeverageTemp		kids; hot; iced

 
4. Add Slots to the Intent
Add the following entries to the list of Slots, choosing the Slot Types created above. Click "Save Intent".
Required	Name	Slot type	Prompt
Yes	BeverageType	cafeBeverageType	What kind of beverage would you like? For example, mocha, chai, etc.
Yes	BeverageSize	cafeBeverageSize	What size? small, medium, large?
 	Creamer	cafeCreamerType	What kind of milk or creamer?
 	BeverageTemp	cafeBeverageTemp	Would you like that iced or hot?
5. Test
Build the app and test some of the Utterances in the Test Bot dialog at the bottom right of the Amazon Lex Console. For example, if you say May I have a chai?, does Lex correctly map chai to the BeverageType slot?
Lambda Function
1.	Create the cafeOrderCoffee function by saving cafeOrderCoffee_lambda.js as a Node.js 6.10 function
o	To work independently in a shared environment, use your initials in the function name (e.g., cafeOrderCoffeeXXX)
o	You can get the function source in the Appendix
o	(No need to set up a trigger; you can accept default values for most of the configuration)
o	Choose an IAM role that includes the AWSLambdaBasicExecutionRole Managed Policy. If no such role exists, you can create a new IAM Role using one of these approaches:
	Choose "Create new role from template(s)", provide a role name, and choose Basic Lambda permissions from the "Policy templates" dropdown
	Choose "Create a Custom role", which should open up a new tab where an IAM role is shown; review the policy document and click "Allow"
2.	Configure the Test event and test to confirm the function works as expected (see cafeOrderCoffee_test.json)
o	you can get the event source in the Appendix
3.	You'll notice that the function checks the bot name it receives (if (event.bot.name !== 'CoffeeBot')); remember to change this value in the function and in the test event to match the name you used for your bot
 
Test the bot
1.	From the Lex Console, select the CoffeeBot bot and choose Latest from the version drop down to make changes
2.	Modify the cafeOrderBeverageIntent Intent
o	Add Thanks for choosing PressoBot! as the "Goodbye message"
o	Associate it with the new cafeOrderCoffee Lambda function (select "Lambda function" in the "Lambda initialization and validation" area)
	When prompted, allow Amazon Lex to call your new function
o	Associate it with the new cafeOrderCoffee Lambda function for (select "Lambda function" in the "Fulfillment" area); remember to click "Save Intent"
	The Lambda function overrides the "Goodbye message"
3.	Build the bot
4.	Test using the Amazon Lex Console; do you see any responses when you ask May I have a mocha?

