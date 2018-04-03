# chloe-dialog

`*.csv` files for the Chloe dialog engine

## Chloe Language

One of the most popular and "manageable chatbot approaches is the trigger->response or pattern->template mapping approach. 
This approach is very labor intensive, but flexible enough to create extremely complex behaviors (see ALICE and other AIML bot).

### Trigger->Response Dialog Specification

The minimal dialog specification that Chloe understands is 2 columns in a CSV file.
The first column contains glob star patterns.
The second column contains the corresponding response templates.

Here's an example `chloe.csv`:

```text
Stop             , OK.
who are you      , I'm Chloe, your virtual assistant.
what can you do  , I can describe the visual world, read text, and even tell jokes.
hey chloe        , "what would"
hello *          , "Hello, how are you today?""
hi *             , "Hi user!"
hello|hi|hey     , "Hello!"    
```

#### Trigger Patterns

If you've seen URL patterns and HTML templates for common web frameworks like `Django` or `Flask` you're already familiar with this architecture. 
The URL regular expressions are your trigger patterns. 
And your HTML template files are your response templates. 

The main difference for the pattern language for a voice-interface chatbot is that the patterns must match natural language text that can be verbalized (spoken). 
So the tokens for our pattern matching language are words (or phones) rather than characters and punctuation. 
We don't have to match unspoken stuff like punctuation with our patterns. 
And we use punctuation like `*`, `|`, `?`, and `-` in our trigger patterns as wild cards or logic control marks. 

We use a simplified version of regular expressions for Chloe's trigger patterns. 
It's similar to glob star patterns for file name matching in most modern shells like bash.
But we've added some features similar to AIML to make it a little more powerful for specifying chatbot dialog.

#### Response Templates

Another requirement for a website template that we don't have for voice-interface dialog templates is the requirement to be able to generate any and all punctuation marks. 
We don't have to generate most punctuation marks in our voice response templates. 
There only are a few punctuation marks that we need to emit or generate, like exclamation (`"!"`) and question marks (`"?"`) and newlines (`"\n"`). 
These affect how the utterance is vocalized by engines like Nuance's Vocalizer or the Android System TTS engine. 


#### State

One additional feature of chatbots that we need that you don't typically have in a stateless (REST) website is a way to manage "state". 
For example in the first trigger->response pair in the `chloe.csv` example above, Chloe asks a question of the user. 
So Chloe needs to listen for a different set of triggers (answers to her question) and be ready with different responses. 
Chloe needs to have "state". 

For a chatbot (or an interactive website) you must specify state transitions to build a graph of transitions for each of those trigger->response pairs.
This is Most chatbots use a pattern it's used for most commercial chatbots, but it's more commonly used for web applications for GET response programming... you'll see that when I show examples.

## We need 2 languages:

We need another "language" for specifying Chloe actions and statements (responses). 
We need a templating language like `jinja2` or `mustache` or python's `str.format`. 
Since we are specifying a string to be said, a starting poing could be jinja2 or even the builtin `.format()`python string interpolation language. Any language that specifies string templates populated with a dictionary/mapping of key-value pairs. In python, the dictionary could be the output of `locals()` to provide all the data available within the namespace of the interpreter, or it could be a huge `kwargs` dictionary containing the "context" of the current conversation, just like in Django and Flask. I don't know of any Android template interpreters, but all modern programming languages have a string interpolation language built in. I'm sure there are equivalents to jinja2.

## Examples

The simplest example of this 2-language (2-column csv) language might be:

    "Hi Chloe", "Hi! How can I help you."

A slightly more complex one might be:

    "Hi Chloe", "Hi {user.first_name}, How can I help you?"


OR

    "Hey|Hi Chloe|Klo, what time is it", "Hi {user.first_name}, it's {now.hour} {now.minute:02d}."

And for the "read" command it might be:

    "* Chloe * read *", "Hi {user.first_name}, here's what I see at the moment. Tell me when to stop. {ocr.text}"

If we're doing things right, text should be ready for us on the Android phone before this command is even finished from the user. The variable `ocr.text` would have been populated with the latest image with the most text in it (some balance of the 2 in our objective function).

One final example from the UX meeting:

    "Help", "Hi {user.first_name}, here's the sort of things I can do {docs.commands[:5].join(',')}. Would you like to learn more?

## Help!!

There are devils in the details, like:

1. the appropriate "fuzziness" of our pattern recognizer, and a way to specify it in the language
2. the simplest way to specify complex data queries
3. how to specify a dialog "chain" or "tree"
4. how to specify more complex actions involving nested conditionals
5. how to specify a language that specifies trigger based on non-verbal information (e.g. reminders at a particular time of day each way, I have an idea for this that would work with our glob * interpreter).
6. ways to compute "confidence" in the intent recognizer

## Nonverbal Triggers

If we specify the triggers as key-value pairs, with a key that specifies the source of the "statement" (string), we can handle a lot of simple nonverbal triggers.  
We could even specify the keys just like RMQ topics with slashes, hashes, and stars, so that they could refer to remote data sources.

    "now/first_exceedance/2018/01/26/12/00": "It's time for that weekly AI brownbag!" 
or

    "now/first_exceedance/Mon/12/00": "It's time for that weekly AI brownbag!" 

verbal triggers might be specified like

    "user/1234": "It's time for that weekly AI brownbag!" 

Alternatively those keys could also be specified in natural language and be passed through our pattern matcher too to generate RMQ path patterns automatically. 


## TODO

- unicode-ascii transcoder
- syntax checker and linting
- graph visualization and linting
- string interpolation templates (mustache)
- hook a Chloe instance up to Slack
- hook a Chloe instance up to TTS and STT (command line app)




