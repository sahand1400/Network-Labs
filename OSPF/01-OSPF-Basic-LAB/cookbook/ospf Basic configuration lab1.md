# This is cookbook for Lab ospf Basic configuration lab1
<div dir="rtl">

## میتوان مستقیماً  متریک را در اینترفیس ها تنظیم کرد.ابتدا پهنای باندی که به صورت دستی رو ی اینترفیس ها تنطیم کرده ایم را برمیداریم سپس با تنظیم دستی متریک انتخاب مسیر انجام گرفته و برای سابت های سرور ها مسیر با متریک کمتر انتخاب شده است
</div>

```cisco

RTR-B1(config)#interface ethernet 1/1
RTR-B1(config-if)#no bandwidth
RTR-B1(config-if)#ip ospf cost 100

RTR-B1(config)#interface ethernet 1/2
RTR-B1(config-if)#no bandwidth
RTR-B1(config-if)#ip ospf cost 200

```

<div dir="rtl">

## حالا با دستور میتوان کاست هر اینترفیس را مشاهده کرد نکته: پهنای باند پیشفرض پورت های اترنت 10 مگ می باشد  
</div>

```cisco

RTR-B1#sh ip ospf interface ethernet 1/1
 Ethernet1/1 is up, line protocol is up
  Internet Address 192.168.1.2/30, Area 1, Attached via Interface Enable
  Process ID 1, Router ID 4.4.4.4, Network Type BROADCAST, Cost: 100

RTR-B1#sh ip ospf interface ethernet 1/2
Ethernet1/2 is up, line protocol is up
  Internet Address 192.168.2.2/30, Area 1, Attached via Interface Enable
  Process ID 1, Router ID 4.4.4.4, Network Type BROADCAST, Cost: 200

```
# Redistribute

<div dir="rtl">

## میتوان روت های OSPF را Redistribute کرد و آورد تو EIGRP و بالعکس روت های EIGRP را Redistribute کرد و آورد تو OSPF، حتی روت های static هم Redistribute میشن وقتی روت های EIGRP یا استاتیک به دنیای OSPF میرن تبدیل به روت EXTERNAL میشن(EX)

</div>

```cisco

RTR-B1(config)#ip route 100.100.100.0 255.255.255.0 null 0

RTR-B1#show ip route
S        100.100.100.0 is directly connected, Null0

```
<div dir="rtl">

## حالا روترهای دیگر باید این استاتیک روت من را یاد بگیرند پس این روت استاتیک را وارد دنیای ospf میکنیم.
</div>

```cisco
RTR-B1(config)#router ospf 1
RTR-B1(config-router)#redistribute static subnets

```
<div dir="rtl">

##  به روتری که از دنیای بیرون روت Redistribute کند میگویند Autonomous System Border Router  (ASBR) میگویند.  
## حالا در روترهای دیگر این روت اکسترنال را باید یاد گرفته باشند
</div>

```cisco

RTR-B2#show ip route

O E2     100.100.100.0 [110/20] via 192.168.4.1, 00:08:47, Serial2/2
      
                 [110/20] via 192.168.3.1, 00:08:47, Serial2/1
				 
```

<div dir="rtl">

##این متریک 20 را عوض کرد در ospf روت ها وقتی از دنیای بیرون وارد میشوند متریکشان 20 است.
</div>

```cisco

RTR-B1(config)#router ospf 1
RTR-B1(config-router)#redistribute static metric 50

```

<div dir="rtl">

## نکته: روت هایredistribute شده باType2 وارد میشن در این تایپ با متریک دنیای ospf جمع نمیشودمتریک در همه جا 20 است اگر از 40 تا روتر هم رد شود و در ذهن به نظر میاد که روتر core چون از هردو طرف با متریک یکسان روت را یاد گرفته پس لود بالانس میکند ولی روتر مسیر مناسب برای رسیدن به این ASBR کجاست اونو در نظر میگیرد با تبدیل به تایپ 1 متریک دنیای OSPF توش اضافه میشود.میتوان تایپ 2 را عوض کرد ولی این کار هیچ تاثیری در انتخاب مسیر ندارد فقط از لحاظ قکری و روانی است.
</div>

```cisco

RTR-B1(config-router)#redistribute static metric 50 metric-type 1

```
<div dir="rtl">

# تغییر تایمرهای پروتکل OSPF

## Hello هر 10 ثانیه یکبار ارسال می شودHOLD Time ها برابر 40 ثانیه است میتوان این مقدار را کمتر کنیم که خرابی را سریعتر تشخیص داده می شودتغییر در داخل اینترفیس ها انجام میشود و در لینک بکاپ تغییر تایمر را لازم نداریم 

</div>

```cisco

RTR-B1(config)#inter e1/1
RTR-B1(config-if)#ip os
RTR-B1(config-if)#ip ospf he
RTR-B1(config-if)#ip ospf hello-interval 3
RTR-B1(config-if)#do sh ip os inter e1/1
 Timer intervals configured, Hello 3, Dead 12, Wait 12, Retransmit 5

RTR-B1(config-if)#ip ospf dead-interval 10
RTR-B1(config-if)#do sh ip os inter e1/1
 Timer intervals configured, Hello 3, Dead 10, Wait 10, Retransmit 5

```
<div dir="rtl">

##  حتما باید در طرف دیگر هم زده شود چون به شدت به یکسان بودن تایمرها گیره
</div>

```cisco

RTR-HQ-1(config)#inter e1/1
RTR-HQ-1(config-if)#ip ospf hello-interval 3
RTR-HQ-1(config-if)#ip ospf dead-interval 10

```

# Authentication

<div dir="rtl">

## روترها توی روتینگ پروتکل همدیگر راAuth میکنند. روترها وقتی به همدیگر هلو مسیج میدن داخلش هش پسورد را بزاره و چک کند که پسورد هم یکسان باشه تا رابطه همسایگی شکل بگیرد و دو مدل داریم یکی روش clear Text و دیگریMD5.
</div>

```cisco

RTR-B1(config)#interface ethernet 1/1
RTR-B1(config-if)#ip ospf authentication
RTR-B1(config-if)#ip ospf authentication-key CISCO

RTR-HQ-1(config)#interface ethernet 1/1
RTR-HQ-1(config-if)#ip ospf authentication
RTR-HQ-1(config-if)#ip ospf authentication-key CISCO

```

<div dir="rtl">

# روش MD5
</div>

```cisco

RTR-B1(config)#interface ethernet 1/2
RTR-B1(config-if)#ip ospf authentication message-digest
RTR-B1(config-if)#ip ospf message-digest-key 1 md5 CISCO

RTR-HQ-2(config)#interface ethernet 1/2
RTR-HQ-2(config-if)#ip ospf authentication message-digest
RTR-HQ-2(config-if)#ip ospf message-digest-key 1 md5 CISCO

RTR-HQ-2#show ip ospf interface ethernet 1/2

 Cryptographic authentication enabled
    Youngest key id is 1

```



