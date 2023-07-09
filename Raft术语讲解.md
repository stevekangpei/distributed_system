

领导者	       leader	             节点的三种角色之一. 集群的首脑，负责发起”提议“、”提交“被多数派认可的决断.
跟随者	       follower	             节点的三种角色之一. 需要对 leader 的 ”提议“ 、”提交“和 candidate 的 发起的投票 进行响应.
候选人	       candidate	         节点的三种角色之一. 是一种处于竞选流程中的临时状态，根据多数派投票的结果会切为 leader 或 follower 的稳定态.
最终一致性	   final consistency	 对于写请求，服务端保证最终一定能提供出正确的结果，但需要同步时间. 同步期间，可能被读到不一致的老数据.
即时一次性	   immediate consistency 强一致性. 服务端要求做到写入立即可读.
预写日志    	   write ahead log	     记录写请求明细的日志.（单指 raft 算法下狭义的预写日志）
状态机	       state machine	     节点内存储数据的介质.
提议	           proposal	             两阶段提交的第一个阶段. 指的是 leader 向所有节点发起日志同步请求的过程.
提交	           commit	             两阶段提交的第二个阶段. 指的是 leader 认可一笔写请求已经被系统采纳的动作.
应用	           apply	             指的是将预写日志记录内记录的写操作应用到状态机的过程.
任期	           term	                 任期是用于标识 leader 更迭的概念. 每个任期内至多只允许有一个 leader.
日志索引	       index	             日志在预写日志数组中的位置.
脑裂	           brain split	         同一任期内，集群出现两个 leader，导致秩序崩盘.