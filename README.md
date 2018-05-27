# EDGAR_ANALYTICS

I used python to write the script.
## Version Python 3.6.3 
I used the following libraries 
<li>csv</li>
<li>os</li>
<li>datetime</li>
All these are from the Python Standard Library so no additional installations are needed if you are using Python 3.6.

## Approach to solve the challenge
### Data Structure
* each user=['ID',request_counts for current time stamp,..,..] e.g. user=['ID_1',0,0,1] User ID 1 made 0, 0, 1 requests at time stamp starting_time+0,starting_time+1,starting_time+2
* all users are stored in a list users=[ ['ID',0,0,1], [..],[..] ]

### Helper functions 
### function check_id(input_list,query_ID)
* input: 
** input_list = users ([ ['ID',0,0,1], [..],[..] ])
** query_ID='ID'
* Goal: 
** examine if the query_ID is in the input_list 
** Used to check if the upcoming request is from an existing user or not
* return:
** 'NOT FOUND' if query_ID is not in the input_list
** the index of the query_ID if it is in the input_list




