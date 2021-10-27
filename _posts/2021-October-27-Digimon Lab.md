---
layout: post
title: Digimon Lab
subtitle: A brief explanation of my code
gh-repo: daattali/beautiful-jekyll
tags: [lab, digimon, I hope this works, Botamon is superior]
comments: true
---

In this lab we were asked to analyze a csv file containing stats on various types of Digimon. The three questions that my code computes are:

1. What is the average speed of all Digimon?
2. Count the number of Digimon with a specific attribute. 
3. Make a team consisting of 3 Digimon with a total of at least 300 attack and take up no more than 15 memory.

My first step was to write out pseudocode for each of the three functions, starting by listing their purpose, input and output. After those, I typed out the main part of my code where I call these functions. By having the csv file (as a string of dictionaries) it meant that the file would only have to be opened once as opposed to in each function. From there I began work on each individual functions.

## average_spd()

The first question I worked on was “What is the average speed of all Digimon?” The first part of the solution is knowing that to calculate the average you divide the total values of something by the number of values. From here we can tell that there are variables to keep track of: the **total** values and the **num**ber of values. From there, for each row in the data, or in other words for each dictionary in the list, the speed of the Digimon would be added to the total and one would be added to the number of Digimon counted. Once it goes through all rows, counting all Digimon the last step is to return the total speed divided by the number of Digimon. I casted this to a float so that it included decimals to be as precise as possible. 

Here is what the function looks like:

~~~python
def average_spd(data): 
    total = 0 
    num = 0 
    for row in data: 
        #for each Digimon add their speed to the total speed and add one to the Digimon count
        total += int(row["Spd"])
        num += 1 
    #return the average speed as a float to include possible decimals 
    return float(total/num)
~~~

## make_team()

The next function I worked on was making a team.  For this one it requires keeping track of three things: **names** of Digimon on the team, the team’s **memory**, and their total **at**tac**k**. Both memory and atk only need to hold a single number so they are integers but names holds multiple strings so it is a list.  After initializing all the variables it is time to move on to what they do. For each row in the csv, if the Digimon uses five or less memory and has 100 or more attack, then their name, memory and atk values are added to the team’s corresponding variables. (The numbers each come from 1/3 of the team max and minimums so that together, all three of them equal or surpass the values) The loop checking each row will continue until there are three team members on the list at which point it will print the stats and return the list of names. While there are more than three in the data that qualify for the team, if there were not the function would print “not enough digimon”.

Here is what the function looks like:

~~~python
def make_team(data):
    #new dictionary with keys that will be returned 
    names = []
    memory = 0
    atk = 0 
    for row in data:
        #if the digimon has less than 1/3 of max memory and 1/3 of min atk
        if int(row["Memory"]) <= 5 and int(row["Atk"]) >= 100: 
            #add corresponding values to name, memory, and atk keys 
            names.append(row["Digimon"])  
            memory += int(row["Memory"]) 
            atk += int(row["Atk"]) 
            #if there are 3 digimon on the team
            if len(names) == 3:
                #print team stats and return the list of team members 
                print("Your team has " + str(atk) + " atk and takes " + str(memory) + " memory") 
                return names
    #if there are not enough print a message that explains error 
    print("not enough digimon")
~~~

## count_attribute() and user input 

The last function was counting how many Digimon have a certain attribute. It was very simple to construct — _too_ simple. I figured why hard code instances when you could get user input? 

### count_attribute()
The function itself made a counter **I**, that for each Digimon in the data set, if the inputted key’s value was equal to to the inputted value would add one. The counter was then returned.

~~~python
def count_attribute(data, key, attribute):
    #new counter variable 
    i = 0 
    for row in data: 
        #if the inputted attribute is a value of the inputted key then add one to the counter 
        if row[key] == attribute: 
            i += 1 
    #return the counter 
    return i 
~~~

The real challenge was then making the values user input. 

### get_key()
The first value required for count_attribute() was the key or in other words the category that the category being counted is in. While the command to make a variable equal to the input only takes one line, it then has to be a valid one in order for the code to run. To check, as long as the user inputted **key** is not a key in data it will ask for a valid input also printing a list of valid keys for a user who is less familiar with the data. Once the input is valid it returns it to be used in count_attribute(). 

Here’s the code:

~~~python
def get_key(data):
    #key is a new variable that is equal to the command line input
    key = (input("what type of attribute do you want to count?: "))
    #if it is not a valid key ask for a new one until it is valid
    while key not in data[0].keys():
        print("invalid input. Check spelling and capitalization")
        print("try any key on this list: " + str(data[0].keys()))
        key = input("New input: ")
    #if the key is valid return it
    return key
~~~

### get_attribute()
The last piece of input needed was what actual attribute was being counted. Same as in get_key(), making the variable was easy, it was cleaning it that took more time. The first step was to get the variable. The difference between get_key() and get_attribute() is that keys you can check against and print in one line whereas it it much more difficult to go down a column. I found the best way to go about this was to make a list of possible attributes within the earlier defined key. To do this, for each row in the list, if the value of the previously defined key in that row is not already on the list of values, it will add the value to the list. Using that list I repeated the same process I did for get_key ending with a function that looks like this:

~~~python
def get_attribute(data, k):
    #attribute is a new variable that is equal to the command line input
    attribute = (input("what attribute do you want to count?: "))
    #make a list of valid attributes to compare input to
    attribute_list = []
    for row in data:
        if row[k] not in attribute_list:
            attribute_list.append(row[k])
    #if it is not a valid attribute ask for a new one until it is valid
    while attribute not in attribute_list:
        print("invalid input. Check spelling and capitalization")
        print("try any attribute on this list: " + str(attribute_list)) 
        attribute = input("New input: ")
    #if the attribute is valid return it
    return attribute
~~~

The final step was then to put it all together:
~~~python
#open the csv file 
with open("./digimon.csv", "r") as file:
    data = list(csv.DictReader(file))
    #run average_spd()
    print("The average digimon speed is " + str(average_spd(data)))
    #gets input for count_attribute()
    k = get_key(data)
    a = get_attribute(data, k)
    #run count_attribute() and make_team(0)
    print("There are " + str(count_attribute(data, k, a)) + "digimon with " + k + " = " + a)
    print("The team is " + str(make_team(data)))
~~~

## Notes 
To write this code I consulted my notes from previous assignments’ functions and spoke with my teacher, and with Divya Ponda (from Mr. Lee’s Art of Data class). I also used [this video](https://youtu.be/4OX49nLNPEE) to understand how to use user input.

I definitely had some difficulty with the code, thinking that we were supposed to use nested dictionaries in each function. After that was cleared up I found that the pseudocode was not that difficult to write but it took me a few tries to fully understand the proper syntax. Beyond the syntax I also learned and had a lot of practice reading error codes. 
All in all though, I am very happy with how my code turned out!

Most importantly though, my favorite Digimon is hands down Botamon

![Botamon](https://user-images.githubusercontent.com/91275863/139144365-0829cfe5-70d2-4526-b3be-eb3e4a20cf8f.png)

This fluffy little void's main attack is blowing bubbles to intimidate their opponents. For more information check out their [DigimonWiki page](https://digimon.fandom.com/wiki/Botamon)
