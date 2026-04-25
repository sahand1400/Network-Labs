#This is cookbook for Lab ospf Basic configuration lab1
<div dir="rtl">

##میتوان مستقیماً  متریک را در اینترفیس ها تنظیم کرد 
##  ابتدا پهنای باندی که به صورت دستی رو ی اینترفیس ها تنطیم کرده ایم را برمیداریم سپس با تنظیم دستی متریک انتخاب مسیر انجام گرفته و برای سابت های سرور ها مسیر با متریک کمتر انتخاب شده است
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
##  حالا با دستور میتوان کاست هر اینترفیس را مشاهده کرد نکته: پهنای باند پیشفرض پورت های اترنت 10 مگ می باشد  
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
## میشن  Redistributeهم STATIC  میشن حتی روت های EXTERNAL OSPF  میرن تبدیل به روت ospf  به دنیای EIGRP باشن وقتی روت های ospf که این روت ها بیان تو ospf کرد تو Redistribute را EIGRP  و بالعکس روت های EIGRP  کرد و آورد تو Redistribute  را ospf  می‏ توان روت های 

##  راه اندازی کنیم به همین دلیل ارتباط این دو روتر با استاتیک روت استOSPF وصل بوده و در این روتر نمی توانیم روتینگ پروتکل RTR-B1اگر تصور کنیم روتری به این روتر 
## مثلا من وص شدم به سازمانی که تو اون سازمان نمیتوتم روتیتگ پروتکل رآن کنم 
</div>
```cisco

RTR-B1(config)#ip route 100.100.100.0 255.255.255.0 null 0

RTR-B1#show ip route
S        100.100.100.0 is directly connected, Null0

```
<div dir="rtl">
## میکنیمospf  حالا روترهای دیگر باید این استاتیک روت من را یاد بگیرند پس این روت استاتیک را وارد دنیای 
</div>
```cisco
RTR-B1(config)#router ospf 1
RTR-B1(config-router)#redistribute static subnets

```
<div dir="rtl">
# میگویند Autonomous System Border Router  (ASBR) کند Redistribute به روتری که از دنیای بیرون روت 
## حالا در روترهای دیگر این روت اکسترنال را باید یاد گرفته باشند
</div>
```cisco

RTR-B2#show ip route

O E2     100.100.100.0 [110/20] via 192.168.4.1, 00:08:47, Serial2/2
                       [110/20] via 192.168.3.1, 00:08:47, Serial2/1
```
<div dir="rtl">
## میشوند با متریک 20 وارد میشوند میتوان این متریک 20 را غوض کردospf  روت ها وقتی از دنیای بیرون وارد 
</div>
```cisco

RTR-B1(config)#router ospf 1
RTR-B1(config-router)#redistribute static metric 50

```
<div dir="rtl">

## کجاست؟ اول اونو در نظر میگیرد ASBRدفتر مرکزی لودبالانس میکند ولی نه روتر مسیر مناسب برای رسیدن به این core  جمع نمی شوند و با متریک محل تولدش دیده میشود ولی در نظر میاد چون مساوی است مثلا روتر ospf وارد میشوند و در این تایپ با متریک دنیای Type2 شده با redistribute نکته: روت های 
##  میتوان تایپ 2 را عوض کرد ولی این کار هیچ تاثیری در انتخاب مسیر ندارد فقط از لحاظ قکری و روانی است
</div>
```cisco

RTR-B1(config-router)#redistribute static metric 50 metric-type 1

```
<div dir="rtl">
# OSPFتغییر تایمرهای پروتکل 
## برابر 40 ثانیه است کمتر کنیم خرابی را سریعتر تشخیص داده می شودHOLD Time ها هر 10 ثانیه یکبار ارسال می 
شوند ومقدار  Hello
##تغییر در داخل اینترفیس ها انجام میشود  و در لینک بکاپ تغییر تایمر را لازم نداریم
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

#Authentication
<div dir="rtl">
## کنند روترها وقتی به همدیگر هلو مسیج میدن داخلش هش پسورد را بزاره و چک کند که پسورد هم یکسان باشه تا رابطه همسایگی شکل  بگیردAuth روترها توی روتینگ پروتکل همدیگر را 
## MD5و دیگری روش clear Text دو مدل داریم یکی 
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



