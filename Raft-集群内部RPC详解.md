
## AppendEntries RPC  日志同步请求

Request：

term	        领导者的任期
leaderID	    leader 的节点 id，方便后续 follower 转发写请求
leaderCommit	leader 最新提交的日志 index，方便 follower 更新数据进度
prevLogIndex	当前同步日志的前一条日志的 index.
prevLogTerm 	当前同步日志的前一条日志的 term.
log[]	        同步的日志，可能为多笔，因为 follower 可能滞后了多笔日志

Resp:
term	        节点当前的任期
success	        同步日志的请求是否成功


Receiver: （请求接收者）

> 终点1：leader

处理方式：

I 倘若该任期小于自身，拒绝，并回复自己的最新任期；

II 倘若该任期大于自身，退位为 follower,按照 follower 的模式处理该请求.


> 终点2：follower

处理方式：

I：倘若 leader 任期落后于自己，拒绝请求，并回复自己所在的任期；

II：倘若 follower 存在不一致的日志，则删除多余的日志，同步 leader 日志与之保持一致；

III：倘若 follower 存在日志滞后，则拒绝请求，让 leader 重发更早的日志，直到补齐所有缺失.

 

> 终点3：candidate

I：倘若 leader 任期大于等于自己，退回 follower，按照 follower 模式处理请求；

II：如果 leader 任期小于自己，拒绝，并回复自己的最新任期.


## AppendEntries RPC  心跳

Request
term	        领导者的任期
leaderID	    leader 的节点 id，方便后续 follower 转发写请求
leaderCommit	leader 最新提交的日志 index，方便 follower 更新数据进度

Receiver：
终点1：leader

处理方式：

I 倘若该任期小于自身，直接无视；

II 倘若该任期大于自身，退位为 follower,按照 follower 的模式处理该请求.


终点2：follower

处理方式：

I 倘若任期小于自身，直接无视

II 重置 leader 心跳检测计时器. 查看 leaderCommit, 看是否有预写日志可以被提交.
 

终点3：候选人

处理方式：

I 倘若任期小于自身，直接无视

II 如果任期大于等于自己，退回 follower，按照 follower 模式处理请求.


## RequestVote RPC

Request

term	        candidate 的竞选任期，如果上位了，就采用此任期
candidateID	    candidate 的节点 id，方便 follower 标记自己将票投给了谁
lastLogIndex	candidate 最晚一笔预写日志的 index
lastLogTerm	    candidate 最晚一笔预写日志的 term

Resp: 

term	        节点当前的任期
granted	        是否投了赞同票


（4）请求终点：

终点1：leader

处理方式：

I 倘若 candidate 的竞选任期小于自身，拒绝，并回复自己的最新任期；

II 倘若 candidate 的竞选任期大于自身，退位为 follower,按照 follower 的模式处理该请求.

 

终点2：follower

处理方式：

I：倘若 candidate 的竞选任期小于自身，拒绝，并回复自己的最新任期；

II：倘若自己已经将票投给了其他 candidate，拒绝；

III：倘若自己已经将票投给了这个 candidate，接受；（candidate 侧会幂等去重）

IV：倘若 candidate 的 lastLogTerm 大于自己最后一笔预写日志的 term，接受；

V：倘若 candidate 的 lastLogTerm 小于自己最后一笔预写日志的 term，拒绝；

VI：倘若 candidate 的 lastLogTerm 等于自己最后一笔预写日志的 term，且 candidate 的 lastLogIndex 大于等于自己最后一笔预写日志的 index，接受；

VII：倘若 candidate 的 lastLogTerm 等于自己最后一笔预写日志的 term，且 candidate 的 lastLogIndex 小于自己最后一笔预写日志的 index，拒绝.



终点3：candidate

I：倘若 candidate 的竞选任期小于等于自己的竞选任期，拒绝；

II：倘若 candidate 的竞选任期大于自己的竞选任期，退回 follower，按照 follower 的模式处理.
