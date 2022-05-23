this is a draft :)

Taught by Drshika Asher, Justin Hu, and Joanna Huang

------------------------------------------

# Getting Ready

1. Download a **text editor** (VSCode: https://code.visualstudio.com/download) and make sure you have a version of **python** greater than 3.0 and https://pip.pypa.io/en/stable/installation/ installed. To check your python version, run the following command:

```bash
   $  python --version
```
- Tutorial for Windows: https://feaforall.com/how-to-install-python-3-on-windows-and-set-the-path/
- Tutorial for Mac: https://www.educative.io/edpresso/how-to-add-python-to-the-path-variable-in-mac
- Install Pip: https://pip.pypa.io/en/stable/installation/

2. Learn a bit of git on the commmand line. You can use any intro tutorial, but [this web browser one](https://learngitbranching.js.org/) looks good.

3. Learn some basics of how a command line works. [Here is a quick crash course](https://www.vikingcodeschool.com/web-development-basics/a-command-line-crash-course) with links to resources to learn even more. The command line is intimidating, but once you get used to it, you'll wonder how you lived without it. It's part of almost every programmer's toolbox.

# Workshop

In this course, we will build a Command Line app (CLI) that helps you be grateful every day. You will learn how to work with different Python Libraries, reading and writing from text and YAML files and taking user input. 

Prior Knowledge: Beginner Programming
Note: I've done this on MacOs Monterey 12.1 but you should be able to do this on any computer. Your progress shouldn't be drastically different, but you may need to adjust depending on how your OS does things. I used `python 3.9.9`.

## Libraries

Create a file called `requirements.txt` and fill it with this. This is a common convention in Python projects for listing all the libraries required.

```js
questionary
datetime
pyyaml
pyfiglet
plumbum
ruamel.yaml
```

Once you've saved this file, run the following to install them (if you use `python3` to execute commands, then use `pip3`):

```bash
$ pip install -r requirements.txt
[install output]
```

<details>
<summary>Note: for the code fences for terminal commands: </summary>
<br>
<code>
$ (this part is what you type)<br/>
(this part is the output)
</code>
</details>   

The `-r` flag tells `pip` to look at `requirements.txt` and install everything in it.

Let me explain how we're using each library.

* [questionary](https://github.com/tmbo/questionary) give us fancy interactive menu interfaces.
* [pyfiglet](https://github.com/pwaller/pyfiglet) provides ASCII art displays.
* [plumbum](https://plumbum.readthedocs.io/en/latest/) gives us a way of accepting input, displaying help information and calling existing system commands.
* [pyyaml](https://pyyaml.org/wiki/PyYAMLDocumentation) allows us to set a filepath and your name
* [datetime](https://pypi.org/project/DateTime/) gives us the date and time
* [ruaeml.yaml](https://pypi.org/project/ruamel.yaml/) extends capabilities of pyyaml and allows us to format strings properly

## Get Started

Create a file called `journal.py`.

We'll use `Figlet` from the `pyfiglet` library to make a fancy greeting for the user. At the moment, our app does nothing but tell us that it's working with the message. 

```python
from pyfiglet import Figlet
from plumbum import colors

   def main():
       greeting = "Gratitude Journal"
       with colors['LIGHT_SEA_GREEN']:
           print(Figlet(font='slant').renderText(greeting))
```

Now create another file called `config.yaml`. We will use this to store information about where your journal is persistently across different runs of the program.   

When you run the program, python collects your inputs and stores it in variables. If you don't save the data to an external file, that data will be lost and the next time you run your program it will go away. So we will use this file to store our configuration.

```yaml
   author: ""
   path: ""
```
Here's what you should put for now.

## Building the Logic

Let's imagine this gratitude journal in real life. Here are some steps you may take:

1. Get a Journal
2. Open the Journal to the right page (for today)
3. Journal
4. Close the Journal
5. View past Journals

We're going to imitate all of these steps in our code. 

### Get a Journal

First, let's start with (1) Get a Journal. We first need to create our journal in python. Our journal must have a location and author.  Let's start by prompting our user for a name and author. For this, we will use our questionary library to prompt the user for input. Create a function called create_journal and use the `questionary.text().ask()` function to prompt the user for input. Add in a text string with the prompt(s) you want to ask the user. For the path, you can choose to either save this in this folder, or to another location.

```python
import questionary
from questionary import prompt

def create_journal():
    #prompt user to name journal
    author = questionary.text("What is your name?").ask()

    #find the place to save the journal
    path = questionary.text().ask("What is the absolute path of your journal?")
```
Now that we have got the input from the user, we have to create a folder for all the journal entries to reside in. In the terminal, you can use a command called `mkdir` or **m**ake **dir**ectory to create a folder. Here we will be importing `os` and `plumbum` to write this in python. Add the import statements at the top of your file and add the following below prompting the user for the path: `os.makedirs(path)`

Now just in case we run into any errors during making the directory, we're going to add some print statements to check if it has been made. Wrap `os.makedirs(path)` in a try, except, else statement. We're going to **try** to make the directory but if it does not work, we will **except** an `OSError` and print out a message to the user using an fstring (which I will explain next). **Else**, if it does work, we will also tell the user. 

Also called “formatted string literals,” f-strings are string literals that have an f at the beginning and curly braces containing expressions that will be replaced with their values. To create an f-string, add an f to the beginning of any string and use {variable_name} to indicate a variable that will be inserted into the string at runtime. Here is what we just wrote:

```python
import os
import plumbum

def create_journal():  
   ...
    path = questionary.text().ask("What is the absolute path of your journal?")

   #making the directory
   try:
       os.makedirs(path)
   except OSError:
       print(f"Creating the directory {path} has failed.")
   else:
       print(f"Successfully created {author}'s journal at {path}")
```

Now we have successfully created our directory for our journal! Let's make sure we save the user inputted filepath and author name to the `config.yml` file so it's available after we quit the program. First, import the `yaml` and `ruamel.yaml` libraries. After you do this, use the `ruamel.yaml.scalarstring.DoubleQuotedScalarString()` cast on the two questionary calls to format the strings with double quotes. Then, create a dictionary with the author and the path. After this, create an instance of the `ruamel.yaml.YAML()` object. Finally use the `yaml.dump()` method to convert your dictionary to yaml. Here's the final code snippet:

```python
author = ruamel.yaml.scalarstring.DoubleQuotedScalarString(questionary.text("What is your name?").ask())
    
    #find the place to save the journal
path = ruamel.yaml.scalarstring.DoubleQuotedScalarString(questionary.text().ask("What is the absolute path of your journal?"))

my_dict = dict(author=author, path=path)
yaml = ruamel.yaml.YAML()

with open('data.yml', 'w') as outfile:
    yaml.dump(my_dict, outfile)
```

To open a file for reading or writing (in this case we used the `w` for writing) you normally have to use an `open('filepath', 'w')` paired with a `file.close()` statement and handle any exceptions. By using the `with open(text file, 'w') as filename:` syntax, we can make our code cleaner and handle any exceptions that pop up.

While we're at it, let's also write out the code to load our data from the YAML file. We're going to use the same with syntax that we discussed earlier, and assign our author and path global* variables to that of the YAML file. This will go right below your imports and above your `create_journal()` function.

```python 
with open("config.yaml", "r") as yamlfile:
data = yaml.safe_load(yamlfile)
author = data['author']
path = data['path']
```

*Since we are going to use these values in many places in our program, we have made these **global** variables. This means we can access them inside or outside any function.

Another thing we must fix in our create_journal() method is to declare that we will be modifying the global variables `author` and `path`. To do this, add `global author, path` to the first line in your `create_journal()` function.

Finally, add a call to create_journal() at the bottom of main() to see if your journal gets created properly.

<details open>
<summary>Here's what you should have by now:</summary>
<p>

```python
import os
import plumbum
import yaml, ruamel.yaml
from pyfiglet import Figlet
from plumbum import colors
import questionary
from questionary import prompt

with open("config.yaml", "r") as yamlfile:
    data = yaml.safe_load(yamlfile)
author = data['author']
path = d    ata['path']

#(1) buy the journal
def create_journal():
    #prompt user to name journal
    global author, path
    author = ruamel.yaml.scalarstring.DoubleQuotedScalarString(questionary.text("What is your name?").ask())
    
    #find the place to save the journal
    path = ruamel.yaml.scalarstring.DoubleQuotedScalarString(questionary.text().ask("What is the absolute path of your journal?"))

    my_dict = dict(author=author, path=path)
    yaml = ruamel.yaml.YAML()

    with open('data.yml', 'w') as outfile:
        yaml.dump(my_dict, outfile)

    #making the directory
    try:
        os.makedirs(path)
    except OSError:
        print(f"Creating the directory {path} has failed.")
    else:
        print(f"Successfully created {author}'s journal at {path}")

def main():
    greeting = "Gratitude Journal"
    with colors['LIGHT_SEA_GREEN']:
        print(Figlet(font='slant').renderText(greeting))
    create_journal()
```
</p>
</details>

To run the app, type the following into your terminal:
```bash
$ python journal.py
Successfully created {author}'s journal at {path}
```
Troubleshooting: Make sure that you are running this from the folder that journal.py is located in. If you are using python 3, you may have to use python3 instead of python and pip3 instead of pip since some computers come pre-installed with a version of python 2.

### Open the journal
Now let's build out the logic for opening our journal. The naming convention that we will follow is that each entry is titled with today's date in Year-month-day format. The contents of the file will be the journal entry. 

To name the entry, we will use the `datetime` module from python format it as a string. We will add `.txt` to the end because each entry will be a text file. 
```python
today_entry = str(datetime.today().strftime('%Y-%m-%d')) + ".txt"
```

However, if our entry already exists, we don't want to overwrite the existing one! We will just call another method that will take care of that for us. Let's check our folder to see if there are already entries for today. In the terminal, we use the `cd` or **c**hange **d**irectory command to go into the right folder. Then we use the `ls` command to list out all the directories available to us. Here's the translation of this in python. 

```python
os.chdir(path)
journal_list = os.listdir()
```
We have saved the names of the journals as a list. Now, we must loop through the journals to see if there's an entry for today. If there is one for today, we can append content to the page. If there is not content, we can create a page. If the folder itself is empty (therefore the list is empty), we can create a page. Here is what this logic looks like in python

```python
#looping through the list of journals
if not journal_list:
    add_page()
for journal in journal_list:
    if fnmatch.fnmatch(journal, today_entry):
        add_content(today_entry)
    else:
        add_page()
```

Finally, let's build the functionality for adding a page. This function is super simple. Follow our naming convention to create the file name. Use the `open()` function to create the text file (we will use the `x` file since we are using the function exclusively for file creation). Then, call the function to add content to the file. 

```python
def add_page():
    entry_name = str(datetime.today().strftime('%Y-%m-%d'))+ ".txt"
    open(entry_name, 'x')
    print("Created Entry" + entry_name)
    add_content(entry_name)
```
<details open> 
<summary> Here's everything you should have in this part: </summary> 
<p>

```python
def add_page():
    entry_name = str(datetime.today().strftime('%Y-%m-%d'))+ ".txt"
    open(entry_name, 'x')
    print("Created Entry" + entry_name)
    add_content(entry_name)

def open_journal(): 
    today_entry = str(datetime.today().strftime('%Y-%m-%d')) + ".txt"
    os.chdir(path)
    journal_list = os.listdir()
    if not journal_list:
        add_page()
    for journal in journal_list:
        if fnmatch.fnmatch(journal, today_entry):
            add_content(today_entry)
        else:
            add_page()
```
</p>
 </details>

### Journal
Now, for the most important part. Let's build the functionality for writing an entry. Our function will take a **parameter** which is a variable that we pass to the function. Using our `with` syntax from before, we will open the text file as a string stream for appending (which allows us to add without destroying the pre-existing content). Then, we will ask the user what they want to journal for today. After this, we will use the `textwrap` package to wrap the text nicely. Finally we will write this nicely formatted text to the text file. 

```python
def add_content(title):
    with open(title, 'a') as entry:
        writing = questionary.text("What are you grateful for?").ask()
        prettier_writing = textwrap.fill(writing) + "\n"
        entry.write(prettier_writing)
```

And that's the entire function! You can also get creative here by adding multiple fun prompts or refer to the questionary documentation to track your mood for the day. 

### Close the Journal
Now that we've built out all the functionality for writing our journal, we should create a way for our user to open and close the journal. In the main function, let's start building a menu that allows us to do that. 

Delete the call to create_journal() that you have right now. Now we will use `questionary`'s `select().ask()` method to ask the user to choose what they want to do. 

```python
choice = questionary.select(
    "What would you like to do",
    choices=[
        'Journal',
        'Read Entries',
        'Quit'
    ]).ask()
```
Now the user will be able to choose to either journal, read their pre-existing entries or quit. 

If the user chooses to journal, there are two possibilities. They either already have a journal, or they don't. If they don't we should create the journal first and then open that journal (since we cannot open a journal that does not exist!). If they do, we can just open the journal. 

If the user chooses to quit, we can give them a nice message letting them know that they are quitting the app. 

If the user chooses to read entries, we will call the method that will take care of the functionality for that (which we will build in the last section).

```python
if choice == 'Journal':
    if path == "": 
        create_journal()
    open_journal()
    elif choice == 'Read Entries':
        read_entries()
    elif choice == 'Quit':
        print("Goodbye, have a lovely day!")
```

Here is what that looks like in python. And here is what your completed main method should look like:

<details open>
<summary>Here's what you should have by now:</summary>
<p>

```python
def main():
    global path,author
    greeting = "Gratitude Journal"
    with colors['LIGHT_SEA_GREEN']:
        print(Figlet(font='slant').renderText(greeting))
    choice = questionary.select(
    "What would you like to do",
    choices=[
        'Journal',
        'Read Entries',
        'Quit'
    ]).ask()
    if choice == 'Journal':
        if path == "":
            create_journal()
        open_journal()
    elif choice == 'Read Entries':
        read_entries()
    elif choice == 'Quit':
        print("Goodbye, have a lovely day!")
```

</p>
</details>

### View past Journals

Okay, last part! You got this :) Let's allow our user to read what they have written in the past. We want to navigate to the folder and list out all the files inside, allowing the user to choose which one to read. Then we can display the contents of the file. 

Let's start by navigating to the directory in which our entries are. Then save all the journals to a list. To create a custom question, we must create a dictionary following the syntax from questionary [TODO: add link to docs when I have wifi again]. We will prompt the user with this question and save the response as the entry we are looking to print out. Then we will use the `with` syntax to open the file as a string stream to read only using the `r` flag. Finally, we print the file to the console. 

```python
def read_entries():
    os.chdir(path)
    journal_list = os.listdir()

    question = [{
        "type": "select",
        "name": "select_entry",
        "message": "Choose an entry to read",
        "choices": journal_list
    },]
    entry = prompt(question)['select_entry']
    with open(entry, 'r') as e:
        print(e.read())
```

## Conclusion

<details>
<summary>Final code (also viewable on github):</summary>
<p>

```python
from datetime import datetime
import os, fnmatch
import textwrap
import questionary
from questionary import prompt
from pyfiglet import Figlet
from plumbum import colors
import yaml, ruamel.yaml

with open("config.yaml", "r") as yamlfile:
    data = yaml.safe_load(yamlfile)
author = data['author']
path = data['path']

#(1) buy the journal
def create_journal():
    #prompt user to name journal
    global author, path
    author = ruamel.yaml.scalarstring.DoubleQuotedScalarString(questionary.text("What is your name?").ask())
    
    #find the place to save the journal
    path = ruamel.yaml.scalarstring.DoubleQuotedScalarString(questionary.text().ask("What is the absolute path of your journal?"))

    my_dict = dict(author=author, path=path)
    yaml = ruamel.yaml.YAML()

    with open('data.yml', 'w') as outfile:
        yaml.dump(my_dict, outfile)

    #making the directory
    try:
        os.makedirs(path)
    except OSError:
        print(f"Creating the directory {path} has failed.")
    else:
        print(f"Successfully created {author}'s journal at {path}")

def add_content(title):
    with open(title, 'a') as entry:
        writing = questionary.text("What are you grateful for?").ask()
        prettier_writing = textwrap.fill(writing) + "\n"
        entry.write(prettier_writing)
        
#flip to the right page
def add_page():
    entry_name = str(datetime.today().strftime('%Y-%m-%d'))+ ".txt"
    open(entry_name, 'x')
    print("Created Entry" + entry_name)
    add_content(entry_name)
    
#open the journal
def open_journal():
    today_entry = str(datetime.today().strftime('%Y-%m-%d')) + ".txt"

    os.chdir(path)
    journal_list = os.listdir()

    #looping through the list of journals to check if there is an entry for today
    if not journal_list:
        add_page()
    for journal in journal_list:
        if fnmatch.fnmatch(journal, today_entry):
            add_content(today_entry)
        else:
            add_page()

# flip through past pages
def read_entries():
    os.chdir(path)
    journal_list = os.listdir()

    question = [{
        "type": "select",
        "name": "select_entry",
        "message": "Choose an entry to read",
        "choices": journal_list
    },]
    entry = prompt(question)['select_entry']
    with open(entry, 'r') as e:
        print(e.read())

def main():
    global path,author
    greeting = "Gratitude Journal"
    with colors['LIGHT_SEA_GREEN']:
        print(Figlet(font='slant').renderText(greeting))
    choice = questionary.select(
    "What would you like to do",
    choices=[
        'Journal',
        'Read Entries',
        'Quit'
    ]).ask()
    if choice == 'Journal':
        if path == "":
            create_journal()
        open_journal()
    elif choice == 'Read Entries':
        read_entries()
    elif choice == 'Quit':
        print("Goodbye, have a lovely day!")
main()
```

</p>
</details>

Thank you so much for joining us for this workshop. Thanks to Harsh Deep & Monica Para (cofounder of 125 Summer of Side Projects: [https://125summer.tech/](https://125summer.tech/)) and [Candace Williams](https://medium.com/thebit/intro-to-file-i-o-and-terminal-usage-how-to-create-a-journal-using-python-7bf1ccf1549a) for a lot of language, explanations and packages used to create this app. ILL!