# OSPF Layer2 WAN P2MP

## توضیح LAB

### در این لابراتوار DMVPN Fhase2 راه اندازی شده است و میخواهیم OSPF را ه اندازی کنیم.

---


## طراحی شبکه

### فعلا بحث Multi Area نیست و مینیمم کانفیگ OSPF در همه روترها کانفیگ میکنیم

```cisco
RTR-HQ(config)#router ospf 1
RTR-HQ(config-router)#network 10.20.30.0 0.0.0.255 area 0
RTR-HQ(config-router)#passive-interface ethernet 0/0
RTR-HQ(config-router)#exit

RTR-B1(config)#router ospf 1
RTR-B1(config-router)#network 172.16.0.0 0.0.0.255 area 0
RTR-B1(config-router)#network 192.168.0.0 0.0.0.255 area 0
RTR-B1(config-router)#passive-interface ethernet 0/0

RTR-B2(config)#router ospf 1

RTR-B2(config-router)#network 172.17.0.0 0.0.255.255 area 0
RTR-B2(config-router)#network 192.168.0.0 0.0.0.255 area 0

```

### این دستورات آخر جهت همسایه شدن روتر B2 با روتر HQ باعث خطا می شود
** Apr 30 11:52:22.251: %OSPF-5-ADJCHG: Process 1, Nbr 192.168.0.1 on Tunnel0 from LOADING to FULL, Loading Done **

```cisco

RTR-B2(config-router)#no network 192.168.0.0 0.0.0.255 area 0

```
## چون به صورت دیفالت تانل ها نتورک تایپشون Point-to-Point است و فقط دو تا روتر میتواند حضور داشته باشد زمانی که B2 را کانفیگ میکنیم اون Hello میفرستد و HQ رابطه اش را با B1 به هم میزند و با B2 همسایه می شود و مجدداً این کار تکرار می شود.

### موقع راه اندازی DMVPN حتما باید Network Type را عوض کرد.در DMVPN Fhase2 ترافیک مالتی کست از اسپوک به سمت هاب و از هاب به سمت اسپوک های دیگر رد می شود اگر از این دید نگاه کنیم ما DR نمیخواهیم چون اسپوک ها ترافیک مالتی کست را برای هم ارسال نمی کنند

```cisco
RTR-HQ(config)#inter tun0
RTR-HQ(config-if)#ip ospf network point-to-multipoint

RTR-B1(config)#inter tun 0
RTR-B1(config-if)#ip ospf network point-to-multipoint

RTR-B2(config)#inter tunnel 0
RTR-B2(config-if)#ip ospf network point-to-multipoint
RTR-B2(config-router)#network 192.168.0.0 0.0.0.255 area 0

```
### حالا خطای قبلی را نداریم و روتینگ تیبل را نگاه میکنیم.
```cisco
RTR-B1#show ip route ospf
Gateway of last resort is not set

      10.0.0.0/8 is variably subnetted, 5 subnets, 3 masks
O        10.20.30.0/24 [110/1010] via 192.168.0.1, 00:15:38, Tunnel0
O     172.17.0.0/16 [110/2010] via 192.168.0.1, 00:02:29, Tunnel0
      192.168.0.0/24 is variably subnetted, 4 subnets, 2 masks
O        192.168.0.1/32 [110/1000] via 192.168.0.1, 00:05:41, Tunnel0
O        192.168.0.22/32 [110/2000] via 192.168.0.1, 00:02:29, Tunnel0

```
### ما DMVPN  فاز2 راه انداختیم ولی NEXT Hub ها به سمت دفتر مرکزی است.راهکا راین است که نتورک تایپ را برادکست کنیم تا NEXT HUB ها حفظ بشوند. قبل از این کار حتما باید دقت کرد که شعبه ها نباید DR شوند چون وظیفه DR دست به دست کردن آپدیت هاست و شعبه B1 اگر DR شود چظور به B2 آپدیت بدهد چون مالتی کست را عبور نمی دهد.پس باید پرایوریتی شعبه ها را صفر کرد.

```
RTR-B1(config)#inter tun 0
RTR-B1(config-if)#ip ospf priority 0

RTR-B2(config)#inter tunnel 0
RTR-B2(config-if)#ip ospf priority 0

```
### حالا نتورک تایپ را عوض می کنیم
```cisco

RTR-HQ(config)#inter tunnel 0
RTR-HQ(config-if)#ip ospf network broadcast

RTR-B1(config)#interface tunnel 0
RTR-B1(config-if)#ip ospf network broadcast

RTR-B2(config)#inter tunnel 0
RTR-B2(config-if)#ip ospf network broadcast

RTR-B1#show ip route ospf
      10.0.0.0/8 is variably subnetted, 5 subnets, 3 masks
O        10.20.30.0/24 [110/1010] via 192.168.0.1, 00:10:43, Tunnel0
O     172.17.0.0/16 [110/1010] via 192.168.0.22, 00:02:58, Tunnel0

RTR-B2#show ip route ospf
      10.0.0.0/8 is variably subnetted, 5 subnets, 3 masks
O        10.20.30.0/24 [110/1010] via 192.168.0.1, 00:05:48, Tunnel0
O     172.16.0.0/16 [110/1010] via 192.168.0.11, 00:00:21, Tunnel0

```

### مشکل NEXT HUB روترها حل شد.

