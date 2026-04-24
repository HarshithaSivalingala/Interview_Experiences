LinkedIn - Staff software engineer - Compute infra 

Screen round - Design restrospective. 
Present a project you contributed the most, discuss the design, tradeoffs, failures, bottlenecks, success and learnings 

Onsite 
1.⁠ ⁠Coding 
Build LZ77 encode/decode using a sliding window. Emit (offset, char) where offset points to a previous occurrence; use (0, char) when no match exists. Continuously append processed characters to the history buffer.
abcabcabc -> (0,a) (0,b) (0,c) (3,a) (3,b) (3,c)

follow up 
Add an option to say forward or reverse. Adding a direction bit so matches can be copied either forward or backward from the history buffer
 abccba -> (0,a,0) (0,b,0) (0,c,0) (3,a,1) (3,b,1) (3,c,1)


2.⁠ ⁠AI coding  ( you will be given a LLM chat and you must use it responsibly. Asking the full solution is highly not recommended)
You are given a graph with n nodes (0 to n−1) and n bidirectional edges, where each edge represents a tunnel.
A hero starts at time t = 0 from any node, given as input.
You can place robots only on leaf nodes (nodes with exactly one edge). Hero does not start from the leaf. 
At each time step, both the hero and the robots can:
move to an adjacent node, or stay in place
All movements happen simultaneously, and each move takes one unit of time.
The hero and robots have perfect information—they always know each other’s positions.
The hero is caught if a robot and the hero occupy the same node at the same time. If the hero reaches any of the leaf nodes, hero lives. 
Goal
Determine the minimum number of robots needed (placed on leaf nodes initially) to guarantee that the hero will be caught, regardless of the hero’s starting position or strategy.


3.⁠ ⁠System design 
Interesting round, it was very focussed and deep dive from the start. Given a key-value database in one server, how would scale it if the memory is full. 
-> sharding, okay what is sharding, how do you do ? how do you route calls ? 
-> consistent hashing , why not simple fixed windows ? why not simple hash % n ? 
-> okay who routes the call -> central server? , can you do better ? 
-> client side hashing -> okay how do you reflect changes ? 
-> service discovery 
Likewise, he kept drilling on, failure handling, read-write balance, replication, syncing, raft partitioning, leader election, parition error handling, 
how many backups ? how do you distribute the data ? migration ? how can we reduce the number of servers ? still have backups . 

4.⁠ ⁠Behavioral 
Typically questions 
then how you influenced beyond your team, how did you evangelize the teams you wanted to adapt ? how you picked the teams ? 
Then he gave a org wide situation, assume you report to the CTO, how would driver a company wise operation(6 like orgs, numerous projects and numerous sub teams). 
Assume i say, we have to move all the companys code base repos ( like 1000 repos) from java 11 to 13, and i want a system that we would follow every time we want to migrate in future. 
Come up with a action plan, ideas and things to be cautious about and communication ownership, milestones and documenting. 
Gave me ten minutes and then asked the plan in whole and asked some questions. 


Verdict 
The recruiter initially said, the feedbacks were all positive and he would let me know after the debrief. 
But eventually came and told, they gave "hire as senior", I took a day and thought of accepting it, and then he told, linkedin went into hiring freeze and he will let me know in the next quarter, if it opens up