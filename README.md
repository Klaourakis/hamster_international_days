# hamster_international_days
# script to get each day, via email, from Wikipedia which international day is the day-after-tomorrow.

#import libraries
from urllib.request import urlopen
from bs4 import BeautifulSoup
import datetime
import smtplib, ssl

now = datetime.datetime.now()
j=4

#specify the url
quote_page = 'https://en.wikipedia.org/wiki/List_of_minor_secular_observances'

#query the website and return the html to the variable ‘page’
page = urlopen(quote_page)

#parse the html using beautiful soup and store in variable `soup`
soup = BeautifulSoup(page, 'html.parser')

#take out all td from the html 
day = soup.findAll('td')

#find what is the day after tomorrow in the MM dd format
day_after_tomorrow = (now + datetime.timedelta(days=2)).strftime("%B %#d")

#create an empty list to append the events of the day
data_events = []

#for loop to look at each day from td and compare it with the day after tomorrow
for x in day:
    try:
        day_text = day[j].text.strip()
        event = day[j+1].text.strip()
        j = j + 1

#if the day from td is the same with the day after tomorrow append the events in the data_events list
        if day_after_tomorrow == day_text:
            data_events.append(event)

#when the elements of day in the for loop reach to the end pass the IndexError that is flagged and continue with the script
    except IndexError:
        pass

#concatenate the elements of the data_events list to a strings with spaces between each element
detailed_events = '\n'.join(data_events)

#if the data_events list was not empty continue with the email sending script
if len(detailed_events) != 0:
    port = 465  # For SSL
    smtp_server = 'smtp.gmail.com'
    sender_email = ''  # enter your address
    receiver_email = ['']  # enter receiver address
    password = 'hippohamster' #enter password of sender email

    SUBJECT = 'You have an international day coming up in two days!'
    TEXT = 'On' + ' ' + day_after_tomorrow + ' ' + 'it\'s the:' + '\n' + detailed_events
    message = 'Subject: {}\n\n{}'.format(SUBJECT, TEXT)

    context = ssl.create_default_context()
    with smtplib.SMTP_SSL(smtp_server, port, context=context) as server:
        server.login(sender_email, password)
        server.sendmail(sender_email, receiver_email, message)

# end of script and we automate through Windows Task Scheduler to run this script daily
