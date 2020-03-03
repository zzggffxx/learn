1、有则更新，无则增加
          Transaction1                                        Transaction2 
t1      update where id=1(id not exist)
t2      gap lock  1到正无穷                                 update where id=2(id not exist)
t3                                                                gap lock 2到正无穷
t4      insert id = 1(wait for gap lock)         
t5                                                        insert id=2(wait for gap lock)
t6      deadlock and rollback

2、主键索引和自建索引，索引name为非唯一索引
          Transaction1                                        Transaction2 
t1      update where id=1
t2      id=1的主键行锁                                     update where name='aa'
t3                                                      name='aa'的自建索引间隙锁，等待id=1的主键行锁  
t4      update where name='aa'         
t5     等待name='aa'的自建索引间隙锁，死锁                     

(1)对于主键索引则只对主键加锁，对于自建索引，需要对自建索引和主键都加锁，加锁顺序固定，因此一般不会死锁
(2)对唯一索引加行锁，非唯一索引加间隙锁

3、update 条件不是主键时，且条件没有索引，即使update成功也会间隙锁
