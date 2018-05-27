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
* for each user the number of reqeust is the value of each element, while the index of each element represents units of elapsed time
* all users are stored in a list users=[ ['ID',0,0,1], [..],[..] ]

### Helper functions 
#### function check_id(input_list,query_ID)
* input: 
    - input_list = users ([ ['ID',0,0,1], [..],[..] ])
    - query_ID='ID', string
* Goal: 
    - examine if the query_ID is in the input_list 
    - Used to check if the upcoming request is from an existing user or not
* return:
    - 'NOT FOUND' if query_ID is not in the input_list
    - the index of the query_ID if it is in the input_list
``` python
def check_id(input_list,ID):
#     print(input_list)
#     print(ID)
    ID_list=[i[0] for i in input_list]
#     print('This is the ID_list\n'.format(ID_list))
    if ID in ID_list:
        index=ID_list.index(ID)
#         print('hey I found the ID:{} and the index :{}'.format(ID,index))
        return index
    else:
#         print('ID is {}'.format(ID))
#         print('No ID found')
#       I did not return False because index =0 == False!!!!!
        return 'Not Found'
```

#### function add_user(input_list, new_id, col_add)
* input: 
    - input_list = users ([ ['ID',0,0,1], [..],[..] ])
    - new_ID='ID', string
    - col_add = number of elapsed time stamp units, int
* Goal: 
    - updated each vector the input_list with [0]*col_add 
    - append a new vector of the new_id with the same size but with the last element as 1
* return:
    - No return. Just used to update input_list
* example:
    -input_list= [ ['a',0,1],['b',1,1 ] ], new_id = 'c', col_add= 1
    - Update the input_list to [ ['a',0,1],['b',1,1 ],['c',0,1] ]
``` python
def add_new_user(input_list,new_id,col_add):
    if col_add!=0:
        i=0
        while i < len(input_list):
            input_list[i]+=[0]*col_add
            i+=1
    #check the vector_size for the new user after adding column 
    vector_size=len(input_list[0])
    #make a vector of filled with 0
    new_user=[0]*vector_size
    #replace the first element in the new_user vector with new_id, last one with a count of 1
    new_user[0]=new_id
    new_user[-1]=1
    #append new_user to the input_list
    input_list.append(new_user)
```
    
#### function split_session(input_list, col_to_check)
* input: 
    - input_list = users ([ ['ID',0,0,1], [..],[..] ])
    - col_to_check = number of time units in the inactivity period, int
* Goal: 
    - split users whose session has ended from those in the input_list
* return:
    - remaining_list(active users)
    - removed_list(users whose sessions have ended)
* example:
    -input_list= [ ['a',1,1,0,0],['b',1,1,0,1 ] ], col_to_check=2
    -output:
        - remaining_list=[ ['b',1,1,0,1 ] ]
        -removed_list=[ ['a',1,1,0,0] ]
```python
def split_session(input_list,col_to_check):
    check_list=[0]*(col_to_check)
    removed_list=[]
    remaining_list=[]
    for ele in input_list:
    #        print('\n current element')
    #        print(ele)
            
        if ele[(0-col_to_check): ]==check_list:
    #             print('find one to remove')
            removed_list.append(ele)
    #             print('index for the element to pop is {}'.format(index))
                
        else:
            remaining_list.append(ele)
    # print('\n in the function\n')
    # print('removed_list is\n')
    # debug(removed_list)
    return remaining_list,removed_list
```

#### function session_info(input_list)
* input: 
    - input_list = user to print out (Note only pass user[1:], do not include the 'ID')
    -e.g. for user [ ['a',0,1,0,0] ], pass input_list = user[1:]
* Goal: 
    - take user list and figure out the request_start, request_end, duriation, and total request count 
* How:
    - request_start= index of the first  NOT 0 element
    - request_end=index of the last NOT 0 element
    - duration=request_end-request_start+1
    - count=sum of all elements in the input_list
* return:
    - (request_start, request_end, duration,count)
* example:
    -input_list= [1,2,0,0]
    -output:
        - (request_start=0,request_end=1,duration=2,count=3)
```python
def session_info(input_list):
#     print(input_list)
    request_list=[]
    count=sum(input_list)
    i=0
    while i <len(input_list):
        if input_list[i]!=0:
            request_list.append(i)
        i+=1
    request_start=request_list[0]
    request_end=request_list[-1]
    duration=request_end-request_start+1
    return request_start,request_end,duration,count

```

### Main script
```python
#file name and the folder name for the input
# import dependencies
import csv
import os
from datetime import datetime,timedelta


input_dirt='input'
input_file='log.csv'
#file name and folder name for the output
output_dirt='output'
output_file='sessionization.txt'
#get the inactivity name
inactivity_file='inactivity_period.txt'

#join the path for the input csv file
input_path=os.path.join(input_dirt,input_file)

# get the inactivity_value
inactivity_path=os.path.join(input_dirt,inactivity_file)
with open(inactivity_path,'r') as text:
    inactivity_value=text.read()
#convert the inactivity_value as int
inactivity_value=int(inactivity_value)

#cler the output file before each testing
output_path=os.path.join(output_dirt,output_file)
with open(output_path,'w',newline='') as output_file:
    output_file.truncate()


#set up a empty list to hold all user users=[[user],[user],..]
users=[]
#unit of time stamp is recorded 
#I assume in general it is 1 second not 0.5 or 0.1 second 
time_unit=1

#calculate how many time stamps to examine to consider inactivity
col_to_check= int(inactivity_value/time_unit)


#read input csv
with open(input_path,'r',newline='') as csvfile:
    csv_reader=csv.reader(csvfile,delimiter=',')
    #skip header
    next(csv_reader, None)
    #assign initial value
    first_row=next(csv_reader)
    
    sessiontime=datetime.strptime(first_row[1]+'-'+first_row[2], '%Y-%m-%d-%H:%M:%S')
    #get the id and the count at the first time_stamp for the first user and append it to the users list
    user=[first_row[0],1]
    users.append(user)
    #set up the current time_stamp as the first user's sessiontime
    time_stamp=datetime.strptime(first_row[1]+'-'+first_row[2], '%Y-%m-%d-%H:%M:%S')
    
    #set up initial_start to monitor the starting time of current session to examine for inacitivity
    initial_start=sessiontime

    #Constant variable not changing  the beginning of all the requests
    #need it for printing output
    beginning=datetime.strptime(first_row[1]+'-'+first_row[2], '%Y-%m-%d-%H:%M:%S')

    #iterate starting from the second user
    for row in csv_reader:
        cur_row=row
        next_row=next(csv_reader,None)

        # if the current row is the last row
        if next_row==None:
            
            #assign the time of request as sessiontime
            sessiontime=datetime.strptime(cur_row[1]+'-'+cur_row[2], '%Y-%m-%d-%H:%M:%S')

            #calculate the time difference between the requested time and the time_stamp
            delta_second=int((sessiontime-time_stamp).total_seconds())

            #store IP address as user_id
            user_id=cur_row[0]

            # check whether if the time stamp has changed
            if delta_second == 0:
                #check if the the request is from an existing user
                user_index=check_id(users,user_id)
                
                #if the request is from a new user
                if user_index=='Not Found':
                    #then it is a new user append new user, col_to_add is 0 since time_stamp has not changed
                    add_new_user(users,user_id,0)
                
                #if the request is from an existing user
                else:
                    # incrase the count at the current time_stamp (should be the last value in the list)
                    users[user_index][-1]+=1
            
            # if the sessiontime is greather than the time_stamp,append the user
            elif delta_second>0:
                #figure out how many units of time has passed between the time difference between this current request from the last request
                col_to_add=int(delta_second/time_unit)
                
                #append this request
                # check if the request is from an new user or an existing user
                user_index=check_id(users,user_id)
                #if new user, append
                if user_index== 'Not Found':
                    add_new_user(users,user_id,col_to_add)

                #if request is from an existing user 
                else:
                    #we need to elapsed time units ( [0] *col_to_add ) to all user in the users
                    # and update the count for the last time stamp with a value of 1 for this request
                    # add_new_user() can update all users' time stamp and append this request as if it is from a new user at last
                    add_new_user(users,user_id,col_to_add)
                    #pop up the redundantly appended new_user (actually it is an old user)
                    users.pop()
                    #change the count at the current time stamp for at the correct index
                    users[user_index][-1]=1

                #change time_stamp to the current sessiontime
                time_stamp=sessiontime          



        # if not the last row
        else:
            rows=[cur_row,next_row]
            for each_row in rows:
                sessiontime = datetime.strptime(each_row[1]+'-'+each_row[2], '%Y-%m-%d-%H:%M:%S')
                delta_second=int((sessiontime-time_stamp).total_seconds())
                user_id=each_row[0]


                if delta_second == 0:
                        #check if it is a new user
                        user_index=check_id(users,user_id)
                        if user_index=='Not Found':
                            #then it is a new user append new user
                            add_new_user(users,user_id,delta_second)
                        #if the new user is in the  list
                        else:
                            #add count at current session by 1
                            users[user_index][-1]+=1

                
                elif delta_second>0:

                    col_to_add=int(delta_second/time_unit)
                    #examine if current session is over
                    if sessiontime>initial_start+timedelta(seconds=(inactivity_value)):
                        #use split_session to split expired and remaining users
                        # split_session return removed_users[[..],[..]],remaining_users[[..],[..],[..]]
                        users,expired_users= split_session(users,col_to_check)
                        # print('Session started at  {} for the following users have ended'.format(initial_start))
                        # debug(expired_users)
                        
                        #export the removed users 
                        with open(output_path,'a',newline='') as output_file:
                            writer = csv.writer(output_file)
                            for expired in expired_users:
                                    expired_id=expired[0]
                                    request_start,request_end,duration,count=session_info(expired[1:])
                                    session_start=(beginning+timedelta(seconds=request_start*time_unit)).strftime('%Y-%m-%d %H:%M:%S')
                                    session_end=(beginning+timedelta(seconds=request_end*time_unit)).strftime('%Y-%m-%d %H:%M:%S')
                                    line=[expired_id,session_start,session_end,duration,count]
                                    writer.writerow(line)
                        #update the start time of session to examine by one unit of time stamp
                        initial_start+=timedelta(seconds=time_unit)

                    
                    #append this current request to the users (may or may not changed depending on if one session has been examined)
                    # check user index again
                    user_index=check_id(users,user_id)
                    if user_index== 'Not Found':
                        add_new_user(users,user_id,col_to_add)
                        
                    else:
                        add_new_user(users,user_id,col_to_add)
                        users.pop()
                        users[user_index][-1]=1

                    #flip session time
                    time_stamp=sessiontime


#function to print the rest of users
# print('\n After all the loop, the remaining users are\n')
# debug(users)
#function to print the rest of users
if len(users) !=0:
    with open(output_path,'a',newline='') as output_file:
        writer = csv.writer(output_file)
        for user in users:
            user_id=user[0]
            request_start,request_end,duration,count=session_info(user[1:])
            session_start=(beginning+timedelta(seconds=request_start*time_unit)).strftime('%Y-%m-%d %H:%M:%S')
            session_end=(beginning+timedelta(seconds=request_end*time_unit)).strftime('%Y-%m-%d %H:%M:%S')
            line=[user_id,session_start,session_end,duration,count]
            writer.writerow(line)
 ```
### One example output
The content of the  sessionization.txt tested on the provided example is as follows:
* 101.81.133.jja,2017-06-30 00:00:00,2017-06-30 00:00:00,1,1
* 108.91.91.hbc,2017-06-30 00:00:01,2017-06-30 00:00:01,1,1
* 107.23.85.jfd,2017-06-30 00:00:00,2017-06-30 00:00:03,4,4
* 106.120.173.jie,2017-06-30 00:00:02,2017-06-30 00:00:02,1,1
* 107.178.195.aag,2017-06-30 00:00:02,2017-06-30 00:00:04,3,2
* 108.91.91.hbc,2017-06-30 00:00:04,2017-06-30 00:00:04,1,1

It is as expected.

I also included one other test I did in the tests folder. 

        

