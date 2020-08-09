# Robo Advisor for Retirement Plans

In this project, we will create the `RoboAdvisor` bot and add an intent with its corresponding slots. This chatbot project gives us a fundamental knowledge about how a chatbot is created and designed to work through Amazon Web Services(AWS). We are going to use Amazon Lex for creating a simple bot and further tuning it in with a lambda function that allows us to add more ability in validating data provided by the user on the Robo Advisor.

The process can be divided into three parts - Initial bot configuration, building & testing, and enhancing the Robo Advisor with an Amazon Lambda function.   


We start by creating an intent named `RecommendPortfolio` in which we configure some sample utterances as follows:    
      
      
* I want to save money for my retirement
* I'm ​`{age}​` and I would like to invest for my retirement
* I'm `​{age}​` and I want to invest for my retirement
* I want the best option to invest for my retirement
* I'm worried about my retirement
* I want to invest for my retirement
* I would like to invest for my retirement   

After that, we define four slots to have the bot ask some questions to users for eliciting responses. Here we define `firstName`, `age`, `investmentAmount` and `riskLevel` for the slots. We can use the built-in types for the first three slots but need to customize the last one having six different categories for risk appetite; None, very low, low, medium, high, and very high.

Building a bot is simply done by clicking the `Build` button in Amazon Lex. The next part is the most challenging in this project. Creating a lambda function from scratch is cumbersome at first sight but the majority of the structure is quite the same as the other one, once we get accustomed to how it works.

Simply speaking, a lambda function consists of several functions that can be categorized as helper functions and intents handler ones. When a user inputs responses for questions in slots, it becomes a dialog or `intest_requests` to the lambda function. An example is as follows.   

```{
  "messageVersion": "1.0",
  "invocationSource": "DialogCodeHook",
  "userId": "John",
  "sessionAttributes": {},
  "bot": {
    "name": "RoboAdvisor",
    "alias": "$LATEST",
    "version": "$LATEST"
  },
  "outputDialogMode": "Text",
  "currentIntent": {
    "name": "RecommendPortfolio",
    "slots": {
      "firstName": "John",
      "age": "40",
      "riskLevel": "Low",
      "investmentAmount": "5000"
    },
    "confirmationStatus": "None"
  }
}
```
In the example above, we can see the contents in the dictionary of `slots` representing the responses for the four slots we mentioned before. If we look into the [final lambda function](https://github.com/coolwonny/RoboAdvisor/blob/master/lambda_function_final.py), we can see that there is `get_slots` function to fetch all the slots and their values from the current intent. Then, `parse_int` function converts any non-integer value into integer form. Next, `validate_data` function takes arguments like **age** and **investment_amount** to validate whether they meet certain conditions set in the function. Here we set an age validation where we make sure the user is less than 65 years old and also avoid taking negative and zero ages. The same goes for the investment amount where the minimum amount should not be less than U$5000. We imported another function called `build_validation_result` to pass on a Boolean value to see if we can move on to the next slot or stop for the next available input while sending certain messages to users.   
   
We now move on to the final slot `riskLevel`. We defined `recommend_portfolio` function that takes in *intent_request* to see if both of the age and the investment amount are successfully validated by using the `validate_data`function. If it is valid, it enters into a conditional statement by looking at the value of `riskLevel` in the *intent_request*. Based on the value, it will return one of six recommendations to the user.

For testing the lambda function, we made four different scenarios.    
- [Age Error where age is more than 65 years old](https://github.com/coolwonny/RoboAdvisor/blob/master/Test_Cases/age_error.txt)
- [Negative age error where age is equal to or less than zero](https://github.com/coolwonny/RoboAdvisor/blob/master/Test_Cases/negative_age_error.txt)
- [Incorrect amount error where the amount is less than 5000](https://github.com/coolwonny/RoboAdvisor/blob/master/Test_Cases/incorrect_amount_error.txt)
- [Correct dialog where all conditions are met](https://github.com/coolwonny/RoboAdvisor/blob/master/Test_Cases/correct_dialog.txt) 

    
As a result, all four scenarios worked well.    

Now we are going to add the lambda function to the bot. The process is quite intuitive that we open the Amazon Lex Console and navigate to the bot configuration, integrate the lambda function by selecting it in the *Lambda initialization and validation and Fulfillment* sections. After building the bot again, we finally have the conversation demonstrated [**here**](https://github.com/coolwonny/RoboAdvisor/blob/master/Amazon%20Lex%20-%20Chrome%202020-08-04%2014-53-43.mp4).   

You may find the [RoboAdvisor Json file](https://github.com/coolwonny/RoboAdvisor/blob/master/RoboAdvisor_Export.json) to see how it is structured.



 
