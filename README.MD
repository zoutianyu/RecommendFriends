1.设计思路
我们的输入是deg2friend.txt，保存用户之间相互关注的信息。每行有两个用户ID，以逗号分割，表示这两个用户之间相互关注即认识。
A,B
B,C
C,D
D,E
E,F
F,D
F,C
F,G
G,I
G,H
H,I
I,C
H,A
二度好友的计算需要两轮的MapReduce。第一轮MapReduce的Map中，如果输入是“H，I”，我们的输出是key=H，value=“H，I”跟key=I，value=“H，I”两条结果。前者表示I可以通过H去发现他的二度好友，后者表示H可以通过I去发现他的二度好友。
 
根据第一轮MapReduce的Map，第一轮MapReduce的Reduce 的输入是例如key =I，value={“H，I”、“C，I”、“G，I”} 。其实Reduce 的输入是所有与Key代表的结点相互关注的人。如果H、C、G是与I相互关注的好友，那么H、C、G就可能是二度好友的关系，如果他们之间不是相互关注的。对应最上面的图，H与C是二度好友，G与C是二度好友，但G与H不是二度好友，因为他们是相互关注的。第一轮MapReduce的Reduce的处理就是把相互关注的好友对标记为一度好友（“deg1friend”）并输出，把有可能是二度好友的好友对标记为二度好友（“deg2friend”）并输出。
 
第二轮MapReduce则需要根据第一轮MapReduce的输出，即每个好友对之间是否是一度好友（“deg1friend”），是否有可能是二度好友（“deg2friend”）的关系，确认他们之间是不是真正的二度好友关系。如果他们有deg1friend的标签，那么不可能是二度好友的关系；如果有deg2friend的标签、没有deg1friend的标签，那么他们就是二度好友的关系。另外，特别可以利用的是，某好友对deg2friend标签的个数就是他们成为二度好友的支持数，即他们之间可以通过多少个都相互关注的好友认识。