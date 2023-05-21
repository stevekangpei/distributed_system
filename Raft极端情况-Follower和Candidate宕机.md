## Raft极端情况：Follower 和 Candidate宕机


如果Follower 和 Candidate 崩溃了，那么后序发送给他们的RequestVote 和 AppendEntries Rpc
都会失败。

* （1）Raft 处理这种问题的时候，通过无限的重试来处理这种失败。如果崩溃的机器重启了。
那么这些RPC 就会成功的完成


* （2）如果一个服务器在完成了一个RPC，但是还没有响应的时候就宕机了，那么它重启的时候，就会受到
再次相同的请求，因为RAFT的RPC 都是幂等的。


## Raft的时间和可用性限制：

只要raft的整个系统满足一下的时间要求，Raft就可以选举出，并维持一个稳定的leader。

`广播时间(broadcast time ) << 选举超时时间 (election time out) << 平均故障时间`

广播时间和平均故障时间是由系统决定的，但是选举超时时间使我们自己设置的，Raft需要将
信息接收并落盘，所以广播时间大约是，0.5ms ~ 20ms。因此选举的超时时间可能在20ms ~ 500ms

