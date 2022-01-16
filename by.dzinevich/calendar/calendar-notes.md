##Calendar relationships model
![](/Users/daryazinevich/code/system-design-notes/by.dzinevich/calendar/calendar-notes.png)
###[view the model ](https://whimsical.com/calendar-ARAUJxPKmsP6B7vT1ArRVJ)

Clarifying questing: what features (mayme notifi, reminders..)

Requirements:
- add events
- reminders at timezone
- invite others to join

Types of data: entities
- a calendar
- users (maybe break down also - person info, payments, etc...)
- events
- notification/reminder 
- notif channel(email, sms...)

Build model:
what are the relationships? 

Room for improvement: 
talk about columns that we would like to index 
For example we may quite often search for all the events that belong to a particular calendar,
so event_id and calendar_id are really great columns to index

Rollup Tables?? 
