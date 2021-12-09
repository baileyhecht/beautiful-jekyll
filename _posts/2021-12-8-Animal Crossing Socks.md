---
layout: post
title: Animal Crossing Lab
subtitle: A brief explanation of my code
gh-repo: daattali/beautiful-jekyll
tags: [Lab, Why do socks have gender, Ankha, Do duh-do dooo do do]
comments: true
---

For this lab, I used an API to make a CSV file that I then analyzed using separate code.

## Collecting the Data

For my first code, ac_csv.py, (short for animal crossing CSV) I started by defining the terms necessary to access the API: 

~~~python
BASE_URL = "https://hm-cs.herokuapp.com"
ENDPOINT = "/socks"
API_KEY = "ArtOfDataKEY123"
idx = 0
~~~

Next, I defined my Socks class that was used to write lines of the CSV and the methods to get the values from the API. To start the main section of my code, I first accessed the 0th index of the API to get collum headers. From there, for each valid index, I used my “get” methods to retrieve the values. This was more of a challenge than I expected because the API returned text objects rather than JSON. This basically means that instead of indexing by words or strings I had to parse through char by char. I was able to use commas as benchmarks for where to start and end my string. For example, to find color 1, the method had to first count 5 commas then start adding chars to the string until it read the sixth comma. My code for that looks like this:

~~~python
def getColor1(data):
    i = 0
    comma = 0
    while comma < 5:
        if data[i] != ",":
            i += 1
        else: 
            comma += 1 
            i += 1

    #color1 is set to the first char after the 6th comma
    color1 = data[i]
    i += 1

    #chars are added to color 1 until the next comma
    while data[i] != ",":
        color1 = f"{color1}{data[i]}"
        i += 1
    return color1
~~~

The first section of lines parse through the first five commas, then the next loop, while there are no commas, adds every non-comma to a string. I repeated this same strategy for finding both the name and color 2 of each sock. Once all three types of data were collected, they were passed through to make an object of my sock class. I used this format for my string method so when called, it would return clean lines for the CSV:

~~~python
  def __str__(self):
        return f"{self.name}, {self.color1}, {self.color2}"
~~~

The next step, of course, was then to print each clean sock row to the CSV. Once that was done the index was increased by one and the cycle repeated until there were no farther indexes. 

## Analyzing the Data

There were two big questions to answer:

1. Which socks have the most variation?
2. How many socks of each color are there?

### Answering Question 1

For the first one, I made a method called mostVariations() that makes a dictionary to count the occurrences of each name, finds the highest value in the dictionary, and then returns all the keys with that associated value. 

~~~python
def mostVariations():
    with open("./ac.csv", "r") as file:
        data = csv.DictReader(file)
        names = {}
        mostVarNames = []
        maxVal = 0
        
        #make a dictionary with how many variation for each type
        for row in data:
            if row["\ufeffName"] not in names.keys(): #decode the "\ufeff" 
                names[row["\ufeffName"]] = 1
            else:
                names[row["\ufeffName"]] += 1

        #find and return the keys with the greatest value 
        #line 24 adapted from https://stackoverflow.com/questions/268272/getting-key-with-maximum-value-in-dictionary
        maxVal = max(names, key=names.get) 
        mostVarNum = names[maxVal]
        for k,v in names.items():
            if v == mostVarNum:
                mostVarNames.append(k)
    return mostVarNames
~~~

There are two things I would like to point out in this method. First, that instead of just saying “name” as the key, it says “\ufeffName”. This is because the data given from the API has a BOM or a byte order mark that basically tells the computer how to read the text. Since I could not figure out how to undo it, an easy workaround was to just include it. The second is line 24: 

~~~python
maxVal = max(names, key=names.get) 
~~~

For this line where I find the maximum value in the dictionary, I played around with a new method. The max() method takes a dictionary and a key and finds the max value. There is a very similar method called min() that does the opposite and finds the smallest value in a dictionary. [Here](https://stackoverflow.com/questions/268272/getting-key-with-maximum-value-in-dictionary) is where I got the idea from. 

Based on the API as of December 8th, 2021, the socks with the most variation are argyle crew socks, color-blocked socks, frilly knee-high socks, holey tights, kiddie socks, mixed-tweed socks, no-show socks, semi-opaque socks, semi-opaque tights, sequin leggings, simple-accent socks, striped socks, striped tights, tube socks, ultra no-show socks, vivid leggings, vivid socks, and vivid tights each with 8 variations.

### Answering Question 2 
The second question was much more straightforward. For this one, all I did was start by making a dictionary with how many occurrences there are of each color. For each sock on my CSV, if the color already existed in the dictionary I added one to it, otherwise, I would add a new key that was that color and set it to one. 

~~~python
        for row in data:
            if row[" Color 1"] not in colors.keys(): 
                colors[row[" Color 1"]] = 1
            else:
                colors[row[" Color 1"]] += 1
~~~

After that, if the sock’s second color wasn’t the same as the sock’s first color I repeated the process with the second color:

~~~Python
            if row[" Color 1"] != row[" Color 2"]:
                if row[" Color 2"] not in colors.keys(): 
                    colors[row[" Color 2"]] = 1
                else:
                    colors[row[" Color 2"]] += 1
~~~

The last step was then to print the returned answers from the two methods. As of December 8th, 2021 this is the dictionary it returns:

{'Pink': 41, 'Red': 39, 'Green': 50, 'Light blue': 33, 'Orange': 27, 'Purple': 37, 'Blue': 47, 'Yellow': 33, 'Beige': 15, 'White': 85, 'Black': 59, 'Brown': 11, 'Gray': 31, 'Colorful': 14}

## My Process

This lab felt fairly straightforward. When we were first assigned the lab most of the class talked through potential complications and solutions. Most of my work in these codes is my own but Aidan pointed out that the API returned text instead of JSON and Mikail and I also bounced ideas off each other when trying to figure out how to properly use the index. Beyond those and the aforementioned line 24, most of my work was my own. It was very nice to know though, that if I did have a problem I had six new people to turn to. 

Something that I think went well was that, since ac_csv.py took so long to run, I frequently used another scrapPaper.py to test certain smaller ideas on their own without altering my code. Beyond the long run time though, this lab felt pretty straightforward with minimal trial and error. 
