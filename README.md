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

### Code
<li><a href="https://github.com/yizhiyin86/EDGAR_ANALYTICS/blob/master/src/sessionization.py">link to the scipt</a></li>link to the scipt

        

