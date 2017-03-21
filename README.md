## Let's connect!
[LinkedIn](https://www.linkedin.com/in/audreychu/) | [Email](audreychu27@gmail.com) | [Github](https://github.com/audreychu)

----

## Part 1: The Doomsday Algorithm

The Doomsday algorithm, devised by mathematician J. H. Conway, computes the day of the week any given date fell on. The algorithm is designed to be simple enough to memorize and use for mental calculation.

__Example.__ With the algorithm, we can compute that July 4, 1776 (the day the United States declared independence from Great Britain) was a Thursday.

The algorithm is based on the fact that for any year, several dates always fall on the same day of the week, called the <em style="color:#F00">doomsday</em> for the year. These dates include 4/4, 6/6, 8/8, 10/10, and 12/12.

__Example.__ The doomsday for 2016 is Monday, so in 2016 the dates above all fell on Mondays. The doomsday for 2017 is Tuesday, so in 2017 the dates above will all fall on Tuesdays.

The doomsday algorithm has three major steps:

1. Compute the anchor day for the target century.
2. Compute the doomsday for the target year based on the anchor day.
3. Determine the day of week for the target date by counting the number of days to the nearest doomsday.

Each step is explained in detail below.

### The Anchor Day

The doomsday for the first year in a century is called the <em style="color:#F00">anchor day</em> for that century. The anchor day is needed to compute the doomsday for any other year in that century. The anchor day for a century $c$ can be computed with the formula:
$$
a = \bigl( 5 (c \bmod 4) + 2 \bigr) \bmod 7
$$
The result $a$ corresponds to a day of the week, starting with $0$ for Sunday and ending with $6$ for Saturday.

__Note.__ The modulo operation $(x \bmod y)$ finds the remainder after dividing $x$ by $y$. For instance, $12 \bmod 3 = 0$ since the remainder after dividing $12$ by $3$ is $0$. Similarly, $11 \bmod 7 = 4$, since the remainder after dividing $11$ by $7$ is $4$.

__Example.__ Suppose the target year is 1954, so the century is $c = 19$. Plugging this into the formula gives
$$a = \bigl( 5 (19 \bmod 4) + 2 \bigr) \bmod 7 = \bigl( 5(3) + 2 \bigr) \bmod 7 = 3.$$
In other words, the anchor day for 1900-1999 is Wednesday, which is also the doomsday for 1900.

__Exercise 1.1.__ Write a function that accepts a year as input and computes the anchor day for that year's century. The modulo operator `%` and functions in the `math` module may be useful. Document your function with a docstring and test your function for a few different years.  Do this in a new cell below this one.


```python
year = 1954
```


```python
"""
This function computes the anchor day for any year (input).
"""

def anchor(year):
    year = str(year)
    cent = int(year[0:2])
    """cent searches for the century"""
    a = (5*(cent%4) + 2)%7
    return a
```


```python
anchor(year)
```




    3




```python
anchor(1994)
```




    3




```python
anchor(2003)
```




    2



### The Doomsday

Once the anchor day is known, let $y$ be the last two digits of the target year. Then the doomsday for the target year can be computed with the formula:
$$d = \left(y + \left\lfloor\frac{y}{4}\right\rfloor + a\right) \bmod 7$$
The result $d$ corresponds to a day of the week.

__Note.__ The floor operation $\lfloor x \rfloor$ rounds $x$ down to the nearest integer. For instance, $\lfloor 3.1 \rfloor = 3$ and $\lfloor 3.8 \rfloor = 3$.

__Example.__ Again suppose the target year is 1954. Then the anchor day is $a = 3$, and $y = 54$, so the formula gives
$$
d = \left(54 + \left\lfloor\frac{54}{4}\right\rfloor + 3\right) \bmod 7 = (54 + 13 + 3) \bmod 7 = 0.
$$
Thus the doomsday for 1954 is Sunday.

__Exercise 1.2.__ Write a function that accepts a year as input and computes the doomsday for that year. Your function may need to call the function you wrote in exercise 1.1. Make sure to document and test your function.


```python
"""
Doomsday function accepts the year and returns the doomsday for that year.
"""

def doomsday(year):
    year = str(year)
    y = int(year[-2:])
    """y is the last two digits of target year"""
    a = anchor(year)
    """Calls earlier definied anchor function"""
    d = (y + (y/4) + a)%7
    return d
```


```python
doomsday(1990)
```




    3




```python
doomsday(1954)
```




    0



### The Day of Week

The final step in the Doomsday algorithm is to count the number of days between the target date and a nearby doomsday, modulo 7. This gives the day of the week.

Every month has at least one doomsday:
* (regular years) 1/10, 2/28
* (leap years) 1/11, 2/29
* 3/21, 4/4, 5/9, 6/6, 7/11, 8/8, 9/5, 10/10, 11/7, 12/12

__Example.__ Suppose we want to find the day of the week for 7/21/1954. The doomsday for 1954 is Sunday, and a nearby doomsday is 7/11. There are 10 days in July between 7/11 and 7/21. Since $10 \bmod 7 = 3$, the date 7/21/1954 falls 3 days after a Sunday, on a Wednesday.

__Exercise 1.3.__ Write a function to determine the day of the week for a given day, month, and year. Be careful of leap years! Your function should return a string such as "Thursday" rather than a number. As usual, document and test your code.


```python
day = 5
month = 3
year = 2004
```


```python
"""
leap_year function determines if year is a leap year.  Leap years are divisible by four.
"""
def leap_year(year):
    year2 = str(year)
    y = year2[-2:]
    if (year%4 == 0):
        if (y == '00' and year%400 == 0):
            det_leap = 'leap'
        elif (y == '00' and year%400 !=0):
            det_leap = 'regular'
        else:
            det_leap = 'leap'
    else:
        det_leap = 'regular'
        
    return det_leap

```


```python

```


```python
"""
dayofweek function inputs day, month, and year variables and returns the day of the 
week for given date.  Uses doomsday algorithm as a foundation and incorporates leap
years.
"""

def dayofweek (day, month, year):

    """Determines if leap year"""
    d = leap_year(year)

    """Below matrices are doomsday dates for corresponding month by index"""
    dooms_reg = [10,28,21,4,9,6,11,8,5,10,7,12]
    dooms_leap = [11,29,21,4,9,6,11,8,5,10,7,12]
    day_name = ['Sunday','Monday','Tuesday','Wednesday','Thursday','Friday','Saturday']
    
    if (d == 'regular'):
        close = dooms_reg[month-1]
    elif (d == 'leap'):
        close = dooms_leap[month-1]
        
    """Btwn calculates the number of days between doomsday and input day"""
    btwn = day - close
    
    if btwn > 0:
        day_index = btwn%7 + doomsday(year)
        day_index = day_index%7

    else: 
        day_index =  doomsday(year)- -btwn%7
        day_index = day_index%7

    return (day_name[day_index])
```


```python
dayofweek(day,month,year)
```




    'Friday'




```python
dayofweek(21,7,1954)
```




    'Wednesday'



__Exercise 1.4.__ How many times did Friday the 13th occur in the years 1900-1999? Does this number seem to be similar to other centuries?


```python
day = 13
month_range = range(1,13)
year_range = range(1900,2000)
```


```python
"""
fri function reads in day, month range (which will fixed) as well as
range of years.  Functions counts the number of Friday the 23th occurences
and thus the true input that will change is year_range.
"""


def fri(day, month_range, year_range):
    """Starts with empty array to append into"""
    tester = []
    for x in year_range:        

        for i in month_range:
    
            """Checks if leap year"""
            year2 = str(year)
            y = year2[-2:]
            if (year%4 == 0):
                if (y == '00' and year%400 == 0):
                    det_leap = 'leap'
                elif (y == '00' and year%400 !=0):
                    det_leap = 'regular'
                else:
                    det_leap = 'leap'
            else:
                det_leap = 'regular'
    
            dooms_reg = [10,28,21,4,9,6,11,8,5,10,7,12]
            dooms_leap = [11,29,21,4,9,6,11,8,5,10,7,12]
            day_name = ['Sunday','Monday','Tuesday','Wednesday','Thursday','Friday','Saturday']
        
            """Finds closest doomsday depending on leap year and month"""
            if (det_leap == 'regular'):
                close = dooms_reg[i-1]
            else:
                close = dooms_leap[i-1]
                
            """Distance between days.  Positive means day is after doomsday.  Divide by 7 for week."""    
            btwn = day - close
            if btwn > 0:
                day_index = btwn%7 + doomsday(x)
                day_index = day_index%7
            else: 
                day_index = doomsday(x) - -btwn%7
                day_index = day_index%7
            if (day_name[day_index] == 'Friday' and day == 13 ):
                """Appends into tester dataframe to count rows"""
                tester.append(i)
    numrows = len(tester)    # 3 rows in your example
    return numrows
```


```python
fri(day, month_range, range(1900,2000))
```




    172




```python
fri(day, month_range, range(1800,1900))
```




    171




```python
fri(day, month_range, range(1700,1800))
```




    172




```python
fri(day, month_range, range(1600,1700))
```




    172



<i>We see that the occurences of Friday the 13ths are similar among varying centuries.</i>

__Exercise 1.5.__ How many times did Friday the 13th occur between the year 2000 and today?


```python
import time
```


```python
day = 13
month_range = range(1,13)
year_range = range(2000,(2001+int(time.strftime("%y"))))
```


```python
fri(day, month_range, year_range)
```




    30



## Part 2: 1978 Birthdays

__Exercise 2.1.__ The file `birthdays.txt` contains the number of births in the United States for each day in 1978. Inspect the file to determine the format. Note that columns are separated by the tab character, which can be entered in Python as `\t`. Write a function that uses iterators and list comprehensions with the string methods `split()` and `strip()` to  convert each line of data to the list format

```Python
[month, day, year, count]
```
The elements of this list should be integers, not strings. The function `read_birthdays` provided below will help you load the file.


```python
file_path = "/Users/audreychu/Documents/4th Year/STA141B/birthdays.txt"
```


```python
def read_birthdays(file_path):
    """Read the contents of the birthdays file into a string.
    
    Arguments:
        file_path (string): The path to the birthdays file.
        
    Returns:
        string: The contents of the birthdays file.
    """
    with open(file_path) as file:
        return file.read()
```


```python
bday = read_birthdays(file_path)
```


```python
bday = bday.split("\n")
```


```python
bday = bday[6:65537]
```


```python
bday = filter(None, bday)
```


```python
bday = bday[0:365]
```


```python
month = [line.split("/")[0] for line in bday]
```


```python
day = [line.split("/")[1] for line in bday]
```


```python
end = [line.split("/")[2] for line in bday]
```


```python
year = [line.split("\t")[0] for line in end]
```


```python
count = [line.split("\t")[1] for line in end]
```


```python
df = []
```


```python
for line in range(0,365):
    x =  [int(month[line]), int(day[line]), int(year[line]), int(count[line])]
    df.append(x)
```


```python
df
```




    [[1, 1, 78, 7701],
     [1, 2, 78, 7527],
     [1, 3, 78, 8825],
     [1, 4, 78, 8859],
     [1, 5, 78, 9043],
     [1, 6, 78, 9208],
     [1, 7, 78, 8084],
     [1, 8, 78, 7611],
     [1, 9, 78, 9172],
     [1, 10, 78, 9089],
     [1, 11, 78, 9210],
     [1, 12, 78, 9259],
     [1, 13, 78, 9138],
     [1, 14, 78, 8299],
     [1, 15, 78, 7771],
     [1, 16, 78, 9458],
     [1, 17, 78, 9339],
     [1, 18, 78, 9120],
     [1, 19, 78, 9226],
     [1, 20, 78, 9305],
     [1, 21, 78, 7954],
     [1, 22, 78, 7560],
     [1, 23, 78, 9252],
     [1, 24, 78, 9416],
     [1, 25, 78, 9090],
     [1, 26, 78, 9387],
     [1, 27, 78, 8983],
     [1, 28, 78, 7946],
     [1, 29, 78, 7527],
     [1, 30, 78, 9184],
     [1, 31, 78, 9152],
     [2, 1, 78, 9159],
     [2, 2, 78, 9218],
     [2, 3, 78, 9167],
     [2, 4, 78, 8065],
     [2, 5, 78, 7804],
     [2, 6, 78, 9225],
     [2, 7, 78, 9328],
     [2, 8, 78, 9139],
     [2, 9, 78, 9247],
     [2, 10, 78, 9527],
     [2, 11, 78, 8144],
     [2, 12, 78, 7950],
     [2, 13, 78, 8966],
     [2, 14, 78, 9859],
     [2, 15, 78, 9285],
     [2, 16, 78, 9103],
     [2, 17, 78, 9238],
     [2, 18, 78, 8167],
     [2, 19, 78, 7695],
     [2, 20, 78, 9021],
     [2, 21, 78, 9252],
     [2, 22, 78, 9335],
     [2, 23, 78, 9268],
     [2, 24, 78, 9552],
     [2, 25, 78, 8313],
     [2, 26, 78, 7881],
     [2, 27, 78, 9262],
     [2, 28, 78, 9705],
     [3, 1, 78, 9132],
     [3, 2, 78, 9304],
     [3, 3, 78, 9431],
     [3, 4, 78, 8008],
     [3, 5, 78, 7791],
     [3, 6, 78, 9294],
     [3, 7, 78, 9573],
     [3, 8, 78, 9212],
     [3, 9, 78, 9218],
     [3, 10, 78, 9583],
     [3, 11, 78, 8144],
     [3, 12, 78, 7870],
     [3, 13, 78, 9022],
     [3, 14, 78, 9525],
     [3, 15, 78, 9284],
     [3, 16, 78, 9327],
     [3, 17, 78, 9480],
     [3, 18, 78, 7965],
     [3, 19, 78, 7729],
     [3, 20, 78, 9135],
     [3, 21, 78, 9663],
     [3, 22, 78, 9307],
     [3, 23, 78, 9159],
     [3, 24, 78, 9157],
     [3, 25, 78, 7874],
     [3, 26, 78, 7589],
     [3, 27, 78, 9100],
     [3, 28, 78, 9293],
     [3, 29, 78, 9195],
     [3, 30, 78, 8902],
     [3, 31, 78, 9318],
     [4, 1, 78, 8069],
     [4, 2, 78, 7691],
     [4, 3, 78, 9114],
     [4, 4, 78, 9439],
     [4, 5, 78, 8852],
     [4, 6, 78, 8969],
     [4, 7, 78, 9077],
     [4, 8, 78, 7890],
     [4, 9, 78, 7445],
     [4, 10, 78, 8870],
     [4, 11, 78, 9023],
     [4, 12, 78, 8606],
     [4, 13, 78, 8724],
     [4, 14, 78, 9012],
     [4, 15, 78, 7527],
     [4, 16, 78, 7193],
     [4, 17, 78, 8702],
     [4, 18, 78, 9205],
     [4, 19, 78, 8720],
     [4, 20, 78, 8582],
     [4, 21, 78, 8892],
     [4, 22, 78, 7787],
     [4, 23, 78, 7304],
     [4, 24, 78, 9017],
     [4, 25, 78, 9077],
     [4, 26, 78, 9019],
     [4, 27, 78, 8839],
     [4, 28, 78, 9047],
     [4, 29, 78, 7750],
     [4, 30, 78, 7135],
     [5, 1, 78, 8900],
     [5, 2, 78, 9422],
     [5, 3, 78, 9051],
     [5, 4, 78, 8672],
     [5, 5, 78, 9101],
     [5, 6, 78, 7718],
     [5, 7, 78, 7388],
     [5, 8, 78, 8987],
     [5, 9, 78, 9307],
     [5, 10, 78, 9273],
     [5, 11, 78, 8903],
     [5, 12, 78, 8975],
     [5, 13, 78, 7762],
     [5, 14, 78, 7382],
     [5, 15, 78, 9195],
     [5, 16, 78, 9200],
     [5, 17, 78, 8913],
     [5, 18, 78, 9044],
     [5, 19, 78, 9000],
     [5, 20, 78, 8064],
     [5, 21, 78, 7570],
     [5, 22, 78, 9089],
     [5, 23, 78, 9210],
     [5, 24, 78, 9196],
     [5, 25, 78, 9180],
     [5, 26, 78, 9514],
     [5, 27, 78, 8005],
     [5, 28, 78, 7781],
     [5, 29, 78, 7780],
     [5, 30, 78, 9630],
     [5, 31, 78, 9600],
     [6, 1, 78, 9435],
     [6, 2, 78, 9303],
     [6, 3, 78, 7971],
     [6, 4, 78, 7399],
     [6, 5, 78, 9127],
     [6, 6, 78, 9606],
     [6, 7, 78, 9328],
     [6, 8, 78, 9075],
     [6, 9, 78, 9362],
     [6, 10, 78, 8040],
     [6, 11, 78, 7581],
     [6, 12, 78, 9201],
     [6, 13, 78, 9264],
     [6, 14, 78, 9216],
     [6, 15, 78, 9175],
     [6, 16, 78, 9350],
     [6, 17, 78, 8233],
     [6, 18, 78, 7777],
     [6, 19, 78, 9543],
     [6, 20, 78, 9672],
     [6, 21, 78, 9266],
     [6, 22, 78, 9405],
     [6, 23, 78, 9598],
     [6, 24, 78, 8122],
     [6, 25, 78, 8091],
     [6, 26, 78, 9348],
     [6, 27, 78, 9857],
     [6, 28, 78, 9701],
     [6, 29, 78, 9630],
     [6, 30, 78, 10080],
     [7, 1, 78, 8209],
     [7, 2, 78, 7976],
     [7, 3, 78, 9284],
     [7, 4, 78, 8433],
     [7, 5, 78, 9675],
     [7, 6, 78, 10184],
     [7, 7, 78, 10241],
     [7, 8, 78, 8773],
     [7, 9, 78, 8102],
     [7, 10, 78, 9877],
     [7, 11, 78, 9852],
     [7, 12, 78, 9705],
     [7, 13, 78, 9984],
     [7, 14, 78, 10438],
     [7, 15, 78, 8859],
     [7, 16, 78, 8416],
     [7, 17, 78, 10026],
     [7, 18, 78, 10357],
     [7, 19, 78, 10015],
     [7, 20, 78, 10386],
     [7, 21, 78, 10332],
     [7, 22, 78, 9062],
     [7, 23, 78, 8563],
     [7, 24, 78, 9960],
     [7, 25, 78, 10349],
     [7, 26, 78, 10091],
     [7, 27, 78, 10192],
     [7, 28, 78, 10307],
     [7, 29, 78, 8677],
     [7, 30, 78, 8486],
     [7, 31, 78, 9890],
     [8, 1, 78, 10145],
     [8, 2, 78, 9824],
     [8, 3, 78, 10128],
     [8, 4, 78, 10051],
     [8, 5, 78, 8738],
     [8, 6, 78, 8442],
     [8, 7, 78, 10206],
     [8, 8, 78, 10442],
     [8, 9, 78, 10142],
     [8, 10, 78, 10284],
     [8, 11, 78, 10162],
     [8, 12, 78, 8951],
     [8, 13, 78, 8532],
     [8, 14, 78, 10127],
     [8, 15, 78, 10502],
     [8, 16, 78, 10053],
     [8, 17, 78, 10377],
     [8, 18, 78, 10355],
     [8, 19, 78, 8904],
     [8, 20, 78, 8477],
     [8, 21, 78, 9967],
     [8, 22, 78, 10229],
     [8, 23, 78, 9900],
     [8, 24, 78, 10152],
     [8, 25, 78, 10173],
     [8, 26, 78, 8782],
     [8, 27, 78, 8453],
     [8, 28, 78, 9998],
     [8, 29, 78, 10387],
     [8, 30, 78, 10063],
     [8, 31, 78, 9849],
     [9, 1, 78, 10114],
     [9, 2, 78, 8580],
     [9, 3, 78, 8355],
     [9, 4, 78, 8481],
     [9, 5, 78, 10023],
     [9, 6, 78, 10703],
     [9, 7, 78, 10292],
     [9, 8, 78, 10371],
     [9, 9, 78, 9023],
     [9, 10, 78, 8630],
     [9, 11, 78, 10154],
     [9, 12, 78, 10425],
     [9, 13, 78, 10149],
     [9, 14, 78, 10265],
     [9, 15, 78, 10265],
     [9, 16, 78, 9170],
     [9, 17, 78, 8711],
     [9, 18, 78, 10304],
     [9, 19, 78, 10711],
     [9, 20, 78, 10488],
     [9, 21, 78, 10499],
     [9, 22, 78, 10349],
     [9, 23, 78, 8735],
     [9, 24, 78, 8647],
     [9, 25, 78, 10414],
     [9, 26, 78, 10498],
     [9, 27, 78, 10344],
     [9, 28, 78, 10175],
     [9, 29, 78, 10368],
     [9, 30, 78, 8648],
     [10, 1, 78, 8686],
     [10, 2, 78, 9927],
     [10, 3, 78, 10378],
     [10, 4, 78, 9928],
     [10, 5, 78, 9949],
     [10, 6, 78, 10052],
     [10, 7, 78, 8605],
     [10, 8, 78, 8377],
     [10, 9, 78, 9765],
     [10, 10, 78, 10351],
     [10, 11, 78, 9873],
     [10, 12, 78, 9824],
     [10, 13, 78, 9755],
     [10, 14, 78, 8554],
     [10, 15, 78, 7873],
     [10, 16, 78, 9531],
     [10, 17, 78, 9938],
     [10, 18, 78, 9388],
     [10, 19, 78, 9502],
     [10, 20, 78, 9625],
     [10, 21, 78, 8411],
     [10, 22, 78, 7936],
     [10, 23, 78, 9425],
     [10, 24, 78, 9576],
     [10, 25, 78, 9328],
     [10, 26, 78, 9501],
     [10, 27, 78, 9537],
     [10, 28, 78, 8415],
     [10, 29, 78, 8155],
     [10, 30, 78, 9457],
     [10, 31, 78, 9333],
     [11, 1, 78, 9321],
     [11, 2, 78, 9245],
     [11, 3, 78, 9774],
     [11, 4, 78, 8246],
     [11, 5, 78, 8011],
     [11, 6, 78, 9507],
     [11, 7, 78, 9769],
     [11, 8, 78, 9501],
     [11, 9, 78, 9609],
     [11, 10, 78, 9652],
     [11, 11, 78, 8352],
     [11, 12, 78, 7967],
     [11, 13, 78, 9606],
     [11, 14, 78, 10014],
     [11, 15, 78, 9536],
     [11, 16, 78, 9568],
     [11, 17, 78, 9835],
     [11, 18, 78, 8432],
     [11, 19, 78, 7868],
     [11, 20, 78, 9592],
     [11, 21, 78, 9950],
     [11, 22, 78, 9548],
     [11, 23, 78, 7915],
     [11, 24, 78, 9037],
     [11, 25, 78, 8275],
     [11, 26, 78, 8068],
     [11, 27, 78, 9825],
     [11, 28, 78, 9814],
     [11, 29, 78, 9438],
     [11, 30, 78, 9396],
     [12, 1, 78, 9592],
     [12, 2, 78, 8528],
     [12, 3, 78, 8196],
     [12, 4, 78, 9767],
     [12, 5, 78, 9881],
     [12, 6, 78, 9402],
     [12, 7, 78, 9480],
     [12, 8, 78, 9398],
     [12, 9, 78, 8335],
     [12, 10, 78, 8093],
     [12, 11, 78, 9686],
     [12, 12, 78, 10063],
     [12, 13, 78, 9509],
     [12, 14, 78, 9524],
     [12, 15, 78, 9951],
     [12, 16, 78, 8507],
     [12, 17, 78, 8172],
     [12, 18, 78, 10196],
     [12, 19, 78, 10605],
     [12, 20, 78, 9998],
     [12, 21, 78, 9398],
     [12, 22, 78, 9008],
     [12, 23, 78, 7939],
     [12, 24, 78, 7964],
     [12, 25, 78, 7846],
     [12, 26, 78, 8902],
     [12, 27, 78, 9907],
     [12, 28, 78, 10177],
     [12, 29, 78, 10401],
     [12, 30, 78, 8474],
     [12, 31, 78, 8028]]



__Exercise 2.2.__ Which month had the most births in 1978? Which day of the week had the most births? Which day of the week had the fewest? What conclusions can you draw? You may find the `Counter` class in the `collections` module useful.


```python
from collections import Counter
import pandas as pd
from pandas import *
```


```python
month = map(int, month)
day = map(int, day)
year = map(int, year)
count = map(int, count)
```


```python
new_df = pd.DataFrame({'Month': month,
                      'Day': day,
                      'Year': year,
                      'Count': count})
```


```python
max_month = new_df.groupby(['Month'])['Count'].sum()
```


```python
max_month = zip(max_month, range(1,13))
max(max_month)
```




    (302795, 8)



Month with highest counts is August.


```python
li = []
```


```python
for i in range(1,366):
    new_day = day[i-1]
    new_month = month[i-1]
    new_year = year[i-1]
    li.append(dayofweek(new_day, new_month, 1900+new_year))
```


```python
1900 + year[0]
```




    1978




```python
dayofweek(day[1], month[1], year[1])
```




    'Wednesday'




```python
dayofweek(1,1,1978)
```




    'Sunday'




```python
final_df = pd.DataFrame({'Month': month,
                      'Day': day,
                      'Year': year,
                      'Count': count,
                      'NameDay': li})
```


```python
final_df
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Count</th>
      <th>Day</th>
      <th>Month</th>
      <th>NameDay</th>
      <th>Year</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>7701</td>
      <td>1</td>
      <td>1</td>
      <td>Sunday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>1</th>
      <td>7527</td>
      <td>2</td>
      <td>1</td>
      <td>Monday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>2</th>
      <td>8825</td>
      <td>3</td>
      <td>1</td>
      <td>Tuesday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>3</th>
      <td>8859</td>
      <td>4</td>
      <td>1</td>
      <td>Wednesday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>4</th>
      <td>9043</td>
      <td>5</td>
      <td>1</td>
      <td>Thursday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>5</th>
      <td>9208</td>
      <td>6</td>
      <td>1</td>
      <td>Friday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>6</th>
      <td>8084</td>
      <td>7</td>
      <td>1</td>
      <td>Saturday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>7</th>
      <td>7611</td>
      <td>8</td>
      <td>1</td>
      <td>Sunday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>8</th>
      <td>9172</td>
      <td>9</td>
      <td>1</td>
      <td>Monday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>9</th>
      <td>9089</td>
      <td>10</td>
      <td>1</td>
      <td>Tuesday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>10</th>
      <td>9210</td>
      <td>11</td>
      <td>1</td>
      <td>Wednesday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>11</th>
      <td>9259</td>
      <td>12</td>
      <td>1</td>
      <td>Thursday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>12</th>
      <td>9138</td>
      <td>13</td>
      <td>1</td>
      <td>Friday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>13</th>
      <td>8299</td>
      <td>14</td>
      <td>1</td>
      <td>Saturday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>14</th>
      <td>7771</td>
      <td>15</td>
      <td>1</td>
      <td>Sunday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>15</th>
      <td>9458</td>
      <td>16</td>
      <td>1</td>
      <td>Monday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>16</th>
      <td>9339</td>
      <td>17</td>
      <td>1</td>
      <td>Tuesday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>17</th>
      <td>9120</td>
      <td>18</td>
      <td>1</td>
      <td>Wednesday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>18</th>
      <td>9226</td>
      <td>19</td>
      <td>1</td>
      <td>Thursday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>19</th>
      <td>9305</td>
      <td>20</td>
      <td>1</td>
      <td>Friday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>20</th>
      <td>7954</td>
      <td>21</td>
      <td>1</td>
      <td>Saturday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>21</th>
      <td>7560</td>
      <td>22</td>
      <td>1</td>
      <td>Sunday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>22</th>
      <td>9252</td>
      <td>23</td>
      <td>1</td>
      <td>Monday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>23</th>
      <td>9416</td>
      <td>24</td>
      <td>1</td>
      <td>Tuesday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>24</th>
      <td>9090</td>
      <td>25</td>
      <td>1</td>
      <td>Wednesday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>25</th>
      <td>9387</td>
      <td>26</td>
      <td>1</td>
      <td>Thursday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>26</th>
      <td>8983</td>
      <td>27</td>
      <td>1</td>
      <td>Friday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>27</th>
      <td>7946</td>
      <td>28</td>
      <td>1</td>
      <td>Saturday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>28</th>
      <td>7527</td>
      <td>29</td>
      <td>1</td>
      <td>Sunday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>29</th>
      <td>9184</td>
      <td>30</td>
      <td>1</td>
      <td>Monday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>335</th>
      <td>8528</td>
      <td>2</td>
      <td>12</td>
      <td>Saturday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>336</th>
      <td>8196</td>
      <td>3</td>
      <td>12</td>
      <td>Sunday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>337</th>
      <td>9767</td>
      <td>4</td>
      <td>12</td>
      <td>Monday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>338</th>
      <td>9881</td>
      <td>5</td>
      <td>12</td>
      <td>Tuesday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>339</th>
      <td>9402</td>
      <td>6</td>
      <td>12</td>
      <td>Wednesday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>340</th>
      <td>9480</td>
      <td>7</td>
      <td>12</td>
      <td>Thursday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>341</th>
      <td>9398</td>
      <td>8</td>
      <td>12</td>
      <td>Friday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>342</th>
      <td>8335</td>
      <td>9</td>
      <td>12</td>
      <td>Saturday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>343</th>
      <td>8093</td>
      <td>10</td>
      <td>12</td>
      <td>Sunday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>344</th>
      <td>9686</td>
      <td>11</td>
      <td>12</td>
      <td>Monday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>345</th>
      <td>10063</td>
      <td>12</td>
      <td>12</td>
      <td>Tuesday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>346</th>
      <td>9509</td>
      <td>13</td>
      <td>12</td>
      <td>Wednesday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>347</th>
      <td>9524</td>
      <td>14</td>
      <td>12</td>
      <td>Thursday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>348</th>
      <td>9951</td>
      <td>15</td>
      <td>12</td>
      <td>Friday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>349</th>
      <td>8507</td>
      <td>16</td>
      <td>12</td>
      <td>Saturday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>350</th>
      <td>8172</td>
      <td>17</td>
      <td>12</td>
      <td>Sunday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>351</th>
      <td>10196</td>
      <td>18</td>
      <td>12</td>
      <td>Monday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>352</th>
      <td>10605</td>
      <td>19</td>
      <td>12</td>
      <td>Tuesday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>353</th>
      <td>9998</td>
      <td>20</td>
      <td>12</td>
      <td>Wednesday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>354</th>
      <td>9398</td>
      <td>21</td>
      <td>12</td>
      <td>Thursday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>355</th>
      <td>9008</td>
      <td>22</td>
      <td>12</td>
      <td>Friday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>356</th>
      <td>7939</td>
      <td>23</td>
      <td>12</td>
      <td>Saturday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>357</th>
      <td>7964</td>
      <td>24</td>
      <td>12</td>
      <td>Sunday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>358</th>
      <td>7846</td>
      <td>25</td>
      <td>12</td>
      <td>Monday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>359</th>
      <td>8902</td>
      <td>26</td>
      <td>12</td>
      <td>Tuesday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>360</th>
      <td>9907</td>
      <td>27</td>
      <td>12</td>
      <td>Wednesday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>361</th>
      <td>10177</td>
      <td>28</td>
      <td>12</td>
      <td>Thursday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>362</th>
      <td>10401</td>
      <td>29</td>
      <td>12</td>
      <td>Friday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>363</th>
      <td>8474</td>
      <td>30</td>
      <td>12</td>
      <td>Saturday</td>
      <td>78</td>
    </tr>
    <tr>
      <th>364</th>
      <td>8028</td>
      <td>31</td>
      <td>12</td>
      <td>Sunday</td>
      <td>78</td>
    </tr>
  </tbody>
</table>
<p>365 rows × 5 columns</p>
</div>




```python
max_day = final_df.groupby(['NameDay'])['Count'].sum()
max_day
```




    NameDay
    Friday       500541
    Monday       487309
    Saturday     432085
    Sunday       421400
    Thursday     493149
    Tuesday      504858
    Wednesday    493897
    Name: Count, dtype: int64




```python
day_name = ['Friday','Monday','Saturday','Sunday','Thursday','Tuesday','Wednesday']
```


```python
max_day = zip(max_day, day_name)
max(max_day)
```




    (504858, 'Tuesday')



Day with highest counts is Tuesday.


```python
min_day = final_df.groupby(['NameDay'])['Count'].sum()
```


```python
min_day = zip(min_day, day_name)
min(min_day)
```




    (421400, 'Sunday')



Day with lowest counts is Sunday.

__Exercise 2.3.__ What would be an effective way to present the information in exercise 2.2? You don't need to write any code for this exercise, just discuss what you would do.

An effect way would be through a table as I did, or rather through a data frame.  Flow or organization charts would also be visually helpful--similiar to that of the video in Lesson 4.
