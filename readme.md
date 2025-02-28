# Order Something!

基础网络定义:

**Participant**
`Person`

**Asset**
`Customer`
`Merchant`

**Transaction**
`Order`
`Deliver`
`Pay`
`logistics`

To test this Business Network Definition in the **Test** tab:

Create two `Person` participants:

```
{
    "$class": "org.example.empty.Person",
    "ID": "1",
    "Name": "Toby"
}
{
    "$class": "org.example.empty.Person",
    "ID": "2",
    "Name": "Bob"
}
```

Create a`Customer`asset:

```
{
    "$class": "org.example.empty.Customer",
    "ID": "1",
    "deposit": 0,
    "commodities": [],
    "index": 0,
    "owner": "resource:org.example.empty.Person#1"
}
```

Create a`Merchant`asset:

```
{
    "$class": "org.example.empty.Merchant",
    "ID": "2",
    "owner": "resource:org.example.empty.Person#2",
    "deposit": "original value",
    "x": 0,
    "y": 0,
    "commodities": [],
    "comname": [],
    "index":0,
    "cust": [],
    "plat": [],
    "commidToDeliver": [],
    "owner": "resource:org.example.empty.Person#2"
}
```

Create a`Platform`asset:

```
{
    "$class": "org.example.empty.Platform",
    "ID": "3822",
    "Name": "",
    "deposit": 0,
    "commidToPay": [],
    "index": 0,
    "Total": 0,
    "Finish": 0,
    "domer": [],
    "docus": [],
    "state": [],
    "merc": []
}
```


Submit a `Order` transaction:

```
{
    "$class": "org.example.empty.Order",
    "mc": "resource:org.example.empty.Merchant#2",
    "ct": "resource:org.example.empty.Customer#1",
    "pt": "resource:org.example.empty.Platform#3",
    "num": 4,
    "x": 1,
    "y": 1
    
}
```

Submit a `Deliver` transaction:

```
{
    "$class": "org.example.empty.Deliver",
    "mc": "resource:org.example.empty.Merchant#ID:2",

}
```

Submit a `Pay` transaction:

```
{
    "$class": "org.example.empty.Pay",
    "pt": "resource:org.example.empty.Platform#3"

}
```

Submit a `logistics` transaction:

```
{
    "$class": "org.example.empty.logistics",
    "from": "resource:org.example.empty.Merchant#2",
    "to": "resource:org.example.empty.Customer#1",
    "num": 4
}
```

Submit a `query_customer` transaction:

```
{
    "$class": "org.example.empty.query_customer",
    "id": ""
}
```

在“Order”阶段，首先系统根据客户的坐标确定运费。之后客户可对我们的外卖平台提交订单（指定一个平台、商品编号、商家）。商家将用户记录在cust数组中，将订单存入对应平台数组plat中，将对应商品编号存入commidToDeliver，并将“指针”变量“index”+1。将通过commodities数组用商品编号索引出商品价值，平台根据商品价格和运费对客户进行收款，此时，客户的存款减去相应的金额，平台存款增加。同时，平台将此时相关联的客户、商家计入平台维护的数组docus和domer中，将该单状态存储为“unfinished”状态。平台正在执行的订单总数“Total”+1。

"Deliver"阶段，在商家asset的定义中，我们使用“index”变量记录从0位置到index位置为商家此刻还没有进行发货的订单。当商家发起“Deliver”操作时，系统自动为商家根据cust数组中记录的客户顺序和commidToDeliver中记录的商品顺序发出缓冲列表中的第一单，即最早进行的订单。后续的客户和商品依次向数组头移动一位，后续收到的订单被记录在此时的“index”之后。平台此时在merc数组和commidToPay记录待付款的商家和对应商家编号。存储对应客户所拥有的商品的commodities数组从商家comname数组中用商品id索引出商品名称，记录在commodities中。

在对商家付款的“Pay”阶段，平台首先从缓冲表commidToPay[0]和merc[0]位置取出当前要支付的商品编号和相应的商家。平台的存款减去商品的金额，为对应商家存款t增加金额。根据“index”将两个数组中后续的商品编号和商家前移，后续收到的订单被记录在此时的“index”之后。付款完成后，该订单的状态在平台中被更新为“finished”。平台已完成的订单总数“Finished”+1。

“logistics”可为我们提示一系列交易已成功进行。

“query_customer”可以实现查看指定客户实时的存款金额。

进行完上述一系列操作，客户commodities中新增商品编号为4的商品名称，客户存款中扣除运费和该商品的价格。平台z中记录了此次进行交易的双方Customer1&Merchant2，Total+1，Finished+1。平台deposit+运费，商家deposit+商品价格。


