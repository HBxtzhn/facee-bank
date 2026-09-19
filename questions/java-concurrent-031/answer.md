参数: corePoolSize、maximumPoolSize、keepAliveTime、workQueue、threadFactory、handler。
拒绝策略: AbortPolicy(抛异常，默认)、CallerRunsPolicy(调用者线程执行)、DiscardPolicy(静默丢弃)、DiscardOldestPolicy(丢弃最老任务)。
