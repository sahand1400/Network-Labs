#  OSPF-Default-Route

## Advertize کردن دیفالت روت به وسیله OSPF موقعی بیشتر مورد استفاده قرار می گیرد که ما به اینترنت وصل باشیم اگر یک طرف شبکه ما و طرف دیگر اینترنت باشد.
![pic LSA Type1](./topology/Lab-07-OSPF-Default-Route-diagram)
### ما در روتر NATبه سمت اینترنت دیفالت روت می نویسیم برای اینکه روتر تمام آدرس هایی را که نداره به سمت اینترنت بفرستد.دیفالت روت: در آخرین خط روتینگ تیبل ما قرار میگیرد  و اگر با هیج روتی مچ نشد با این مج می شود و بهسمت بیرون ارسال می شود.در این شبکه بین تمام تجهیزات لایه 3 حتی نصقه روتر نت OSPF پیاده سازی شده است در نتیجه روتر NAT ما روت های داخل شبکه را بلد است.اگر قرار باشد کاربرهای داخل VLAN ها اینترنت داشته باشد باید دیفالت روتی که تو روتر NAT نوشته ایم باید در همه روترها نوشته شده باشد که نوشتن دستی سخته برای همین دیفالت روت را میدهیم به OSPF و OSPF این روت را ADVERTIZE کند برای بقیه روتر ها.

---
```cisco
R2#sh ip os database

            OSPF Router with ID (10.2.2.1) (Process ID 1)

                Router Link States (Area 2)

Link ID         ADV Router      Age         Seq#       Checksum Link count
1.4.4.1         1.4.4.1         127         0x80000002 0x005D1A 2
10.2.2.1        10.2.2.1        122         0x80000002 0x00B1A1 3


```

### برای دیدن جزئیات بیشتر

```cisco

RTR-NAT(config)#ip route 0.0.0.0 0.0.0.0 10.1.1.1

RTR-NAT(config)#router ospf 1
RTR-NAT(config-router)#default-information originate

```
### حالا در بقیه تجهیزات لایه3 دیفالت روت را به عنوان روت EXternal type2 باید Advertize  شده باشد.

```cisco
      
SW-Branch#sh ip route

O*E2  0.0.0.0/0 [110/1] via 192.168.3.2, 00:02:12, Ethernet0/0



```
###  یعنی دیفالت روت با متریک 1 advertize شده و در همه جا متریکش یک است. میتوان با اضافه کردن دستور زیر تایپ متریک را یک کرد.

```cisco
RTR-NAT(config)#router ospf 1
RTR-NAT(config-router)#default-information originate metric-type 1

SW-Branch#sh ip route

O*E1  0.0.0.0/0 [110/41] via 192.168.3.2, 00:00:28, Ethernet0/0

```
###  حتی میتوان متریک را دستی تنظیم کرد : این تنظیم دستی متریک به درد زمانی میخوره که ما دو تا اینترنت داشته باشیم به OSPF میگیم که در روتر اصلی دیفالت روت را به شبکه ما بفرستد با متریک کمتر و مسیر پشتیبان متریکش بیشتر باشد. پس همه روترها از اینترنت اصلی استفاده می کنند اگر اینترنت اصلی قطع شود IP SLA و Track می نویسیم که دیفالت روت را برداره و دیگه اون دیفالت روت Advertize نمیشه و ترافیک از اینترنت پشتیبان می ره و اگر وصل بشه دوباره از مسیر اصلی میرود.

```
RTR-NAT(config)#router ospf 1
RTR-NAT(config-router)#default-information originate metric-type 1 metric 50

SW-Branch#sh ip route

O*E1  0.0.0.0/0 [110/90] via 192.168.3.2, 00:00:34, Ethernet0/0

```
### در صورتی دیفالت روت Advertize میشود که دیفالت روت وجود داشته باشد مثلا اگر ما دیفالت روت را برداریم دیگر در روتر Branch دیفالت روت وجود ندارد مه میتوان این را مورد را عوض کرد

```cisco
RTR-NAT(config)#no ip route 0.0.0.0 0.0.0.0 10.1.1.1

SW-Branch#sh ip route

**No defaualt route**

RTR-NAT(config)#ip route 0.0.0.0 0.0.0.0 10.1.1.1
RTR-NAT(config)#router ospf 1
RTR-NAT(config-router)#default-information originate metric-type 1 metric 50 always

```


### حالا همه دیفالت روت دارن و مسیر یابی اش درست انتخاب می شود یعنی در روتر برنچ پون پهنای باند بالا 10 مگ است دیفالت روت داره به مسیر که پهنای باند بیشتر دارد اشاره می کند. اگر یک قسمتی از شبکه نمیخوان از دیفالت روت استفاده کنند این امکان وجود دارد که دیفالت روت را فیلتر کرد.

