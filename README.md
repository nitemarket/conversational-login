# FormMessenger.js

ver. 0.3.6

FormMessenger.js is a javascript plugin that transforms HTML forms into a conversational chat room.

Not require JQuery dependency. It's is plain Javascript.

### Setup

#### HTML 

```html
<link rel="stylesheet" href="css/formmessenger.min.css">
<script src="js/formmessenger.min.js"></script>
```

```html
<div id="fm-container"></div>
<form name="signup" id="fm-initiator" method="post" action="signup">
    <input type="hidden" name="secret" value="silent">
    <input type="text" name="why" value="secret" fm-disabled>
	<input type="text" name ="name" placeholder="Full Name"
    	fm-questions="May I know your name?">
    <input type="checkbox" name ="interest" value="code" 
        fm-questions="What do you do in free time?"
        fm-label="Coding">
    <input type="checkbox" name ="interest" value="play-games" fm-label="Playing Games">
    <input type="checkbox" name ="interest" value="sleep" fm-label="Sleeping">
    <input type="checkbox" name ="interest" value="travel" fm-label="Traveling">
	<input type="email" name="email" placeholder="Email Address"
		fm-questions="Hi {{previousResponse}}, what's your email?">
	<input type="password" name="password" placeholder="Password" 
    	fm-questions="Secure your account now. What's your password?">
	<input type="submit" name="submit">
</form>
```
Plugin is automatically initiated by assigning id `#fm-initiator` to a HTML form.

#### Javascript

```javascript
window.FormMessenger = new FormMessenger({
    formEl: document.getElementById("fm-initiator"),
    containerEl: document.getElementById("fm-container"),
});
```
Plugin can be manually initiated by providing a DOM element as an option `formEl`.

#### Tags

HTML tags, attribules, id:
- `#fm-initiator` : The form id to initiate the the transformation 
- `#fm-container` : The container of the created chat room
- `fm-disabled` : To be excluded from the questions
- `fm-questions` : Questions that will be asked for the input
- `fm-label` : Label of multi-selection input for bubble display
- `fm-sensitive` : Label of sensitive element to prevent from being displayed
- `fm-nobubble` : Prevent bubbles from being displayed
- `input/button[type=submit]` : Input / button is needed to submit the form

Object key `formEl` must be provided to call the bot out.

The generated chat room will be appended in `<body>` if id `#fm-container` is not assigned.


### Options / configurations:

- `previousResponsePattern` : Pattern of previous response to show in next question
- `formCompleteCallback` : Function can be defined to overwrite the process of form submission
- `chatListClass` : Class to be inserted into chat list div
- `chatElementClass` : Class to be inserted into chat response div
- `inputContainerClass` : Class to be inserted into user input container div
- `inputBoxClass` : Class to be inserted into user input box
- `inputButtonClass` : Class to be inserted into user input submit button
- `formValidation` : Object of inputs validation function. @see custom validation section
- `formSelection` : A selection of forms for user to choose @see form selection section
- `formSelectionQuestion` : Question for user to select a form
- `formSubmissionText` : Text to display when form is being submitted
- `humanized` : To determine if bot is typing like human
- `speedPerCharacter` : Typing speed in milliseconds if `humanized` is true
- `greetingText` : Text to display when chat is initiated

#### Custom validation

Option `formValidation` is an javascrip object that contains pairs of input `name` and `function`. 

- For single input (type: text, password...), the input (`string`) is parsed as an argument.
- For multiple input (type: checkbox, radio...), `array` is provided.

`this.setErrorAndReply(question)` is used to repeat the question with different statement.

```javascript
formValidation: {
    //input name : function()
    'email': function(value){
        if(!(/^\w+([\.-]?\ w+)*@\w+([\.-]?\ w+)*(\.\w{2,3})+$/.test(value))) {
            this.setErrorAndReply("Please provide a valid email.");
        }
    },
    'interest': function(values){
        // at least 2 selections
        if(values instanceof Array && values.length < 2) {
            this.setErrorAndReply("Please select more than one.");
        }
    },
},
```

### Method implementation

A proper way to communicate with existing Bot Messenger.

#### Form selection

In order to allow users quickly jump into a particular form, two options (`formSelection` and `formSelectionQuestion`) are provided. Two use cases are explained below:

```javascript
    {
        formSelectionQuestion: "Hi, which section do you want to proceed?",
        formSelection: {
            //Bubble label : form element
            "Sign In": document.getElementById("signin"),
            "Sign Up": document.getElementById("signup"),
        },
    }
```

```javascript
    window.formMessenger.setFormSelection({
        //Bubble label : form element
        "Sign In": document.getElementById("signin"),
        "Sign Up": document.getElementById("signup"),
    }, 
    "Hi, which section do you want to proceed?");
```

#### Form Proceed Yes/No

Similar to form selection but a form element is provided and users are asked Yes/No if they want to proceed to that form.
Three arguments (`form`, `question`, `noCallback`) are provided.

```javascript
    window.formMessenger.setFormSelection({
        label: "Sign In",
        elem: document.getElementById("signin"),
    }, 
    "Hi, which section do you want to proceed?",
    function() {
        alert("User press no. Form ends");
    });
```

#### Set Error Response

Method: `setErrorResponse`

```javascript
    window.formMessenger.setErrorResponse("Error. Please try again.", function() {
        alert('What to do next?');
    });
```

#### Set Info Response

Method: `setInfoResponse`

```javascript
    window.formMessenger.setInfoResponse("Info. Good trial.", function() {
        alert('What to do next?');
    });
```

### Future improvements

- Add more HTML input type. Eg. select, file