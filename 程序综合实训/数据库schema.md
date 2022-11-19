# Schema

11.9更新：order加入时间属性：YY:MM:HH MM:SS酱什的

```mysql
create database takeaway;# 创建数据库

create table user(# 将所有的员工全部集合在了这一个表中
    type tinyint comment '1=manager,2=delivery,3=consumer',
    ID char(20),
    password char(20),
    addID char(20),
    foreign key (addID)references address(ID),
    constraint checkaddr check ( type=3&addID is not null )
);

create table dish(
    type char(10) comment '菜的类型，是甜品或是蛋糕之类',
    name char(10) comment '菜名',
    ID char(20) primary key auto_increment,
    money int comment '价格',
    introduction char(50) comment '简短的介绍'
);

create table address(
    ID char(20) primary key ,
    phonenum char(11),
    building char(5)comment '哪栋楼',
    floor tinyint comment '哪层'
);# 对于phonenum要设置为有且仅有11位，从java程序上写。

create table order(
    ID char(20),
    addID char(20),
    dishID char(20),
    dishAmount tinyint,
    money int,
    state tinyint,
    deliveryID char(20),
    orderTime datetime,
    foreign key (addID)references address(ID),
    foreign key (dishID)references dish(ID)
);

#联系集
create table consumer_order(
  consumerID char(20),
  orderID char(20),
  foreign key (consumerID)references user(ID),
  foreign key (orderID) references order(ID)
);
```

