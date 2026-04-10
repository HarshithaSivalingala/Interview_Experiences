
Uber LLD interview experience(SDE-2):-
Design a cab booking Service for a city(e.g. Bangalore).The cabService can have multiple branches in city like in Koremangla,Whitefield etc. In each branch there can be different vehicles like Sudan,SUV,Hatchback etc.
The price to book a vehicle depends on both the branch to which the vehicle belongs to and the vehicle itself.
Our system should support these 3 functions:-
1) addBranch(branchName)
2) addCab(cabId,cabType,branchName)
3) bookCab(cabType,startTime,endTime)

Also to book a cab there can be different strategies.E.x:- choose cab of particular cabType from all of the branches with the lowest price or choose the nearest cab(had to implement the first one only but code should be extendable).Also a cab can only be booked if it is not occupied in any overlapping time slot.

Time:- 50 mins.Was able to explain my approach and code all of the functions in C++ but could not test it due to lack of time...
