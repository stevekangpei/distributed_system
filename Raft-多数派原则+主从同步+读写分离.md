
## 多数派原则：


 ![](imgs/quorum.jpeg)


多数派，指的是一个群体的数量达到总数的一半以上.

多数派原则指的是，系统的决断无需全员参与,多数派达成的共识即可视为整个系统的答复.

以集群存在 5 个节点为例，多数派则需要集齐 3 个及 3 个以上节点，
至多可以允许 2 个节点存在开小差背离主流的情况. 同理，倘若集群 6 个节点，
则多数派需要集齐 4 个及 4 个以上节点，因此同样至多允许 2 个节点开小差. 
综上，这是奉行多数派原则的集群通常将节点个数设置为奇数的原因之一.



## 一主多从：

raft 算法下系统中的节点分为领导者 leader 和跟随者 follower 两类角色.
leader拥有更广阔的视野，需要总览全局，领导一些日常事务的推进；
follower 职责相对简单但同样重要，因为这是一个基于多数派原则运作的民众团体，
所有角色只要拧成一股绳，聚成了多数派，就能代表整个系统进行决断.



## 读写分离：

读操作可以由集群的任意节点提供服务；
写操作统一需要由 leader 收口处理，并向 follower 同步. 
倘若 follower 率先收到了来自客户端的写请求，也需要转发给 leader 进行处理.

（1）读操作可由任意节点提供服务，那么倘若一个存在数据状态滞后的 follower 提供了服务，
那么客户端就可能读到老数据. 因此到此为止，raft 只能保证到数据的最终一致性，而无法满足即时一致性. 
在工程落地中，也针对读操作的即时一致性提出了改进方案


（2）集群一主多从，纵览全局. 倘若 leader 出了问题，群龙无首，系统岂不是会分崩离析吗？
针对这个问题，raft 建立了一套完善的领导者选举机制，保证在 leader 不可用时会有替补席的 follower 挺身而出，
改朝换代成为新的 leader，保证系统的正常运作，
