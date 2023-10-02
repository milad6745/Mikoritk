برای ایجاد اتصال VPN در دستگاه MikroTik، می‌توانید از پروتکل PPTP، L2TP/IPsec یا SSTP استفاده کنید. در اینجا نحوه‌ی ایجاد اتصال PPTP و L2TP/IPsec آمده است:

### ایجاد اتصال PPTP:

1. **ورود به دستگاه MikroTik**:

   وارد پنل مدیریت MikroTik شوید.

2. **ایجاد کانفیگ PPTP Server**:

   در قسمت PPP > Interface، یک اتصال PPTP Server بسازید:

   ```
   /interface pptp-server server
   set authentication=mschap1,mschap2 default-profile=default-encryption enabled=yes
   ```

3. **اضافه کردن کلاینت‌ها**:

   سپس کلاینت‌ها را به سرویس PPTP اضافه کنید:

   ```
   /ppp secret add name=myuser password=mypassword profile=default-encryption service=pptp
   ```

   در اینجا، `myuser` و `mypassword` اطلاعات ورود کلاینت هستند.

4. **تنظیم مسیردهی**:

   برای اطمینان از دسترسی کلاینت‌ها به شبکه‌های محلی، باید مسیردهی را تنظیم کنید:

   ```
   /ip route add distance=1 dst-address=192.168.88.0/24 gateway=pptp-out1
   ```

   در اینجا، `192.168.88.0/24` شبکه محلی شماست.

5. **دریافت وضعیت PPTP**:

   در قسمت PPP > PPTP Server، می‌توانید وضعیت اتصالات PPTP را ببینید.

   ```
   /interface pptp-server server print
   /ppp secret print
   ```

### ایجاد اتصال L2TP/IPsec:

1. **ورود به دستگاه MikroTik**:

   وارد پنل مدیریت MikroTik شوید.

2. **ایجاد پروفایل IPsec**:

   در قسمت IP > IPsec، یک پروفایل IPsec بسازید.

   ```
   /ip ipsec profile add name=l2tp-profile
   ```

3. **تنظیمات IPsec Peer**:

   حالا یک IPsec peer بسازید.

   ```
   /ip ipsec peer add address=0.0.0.0/0 exchange-mode=main-l2tp nat-traversal=yes profile=l2tp-profile
   ```

4. **تنظیمات L2TP Server**:

   در قسمت PPP > Interface، یک اتصال L2TP Server بسازید.

   ```
   /interface l2tp-server server
   set authentication=mschap2 default-profile=default-encryption enabled=yes max-mru=1460 max-mtu=1460 mrru=disabled
   ```

5. **اضافه کردن کلاینت‌ها**:

   سپس کلاینت‌ها را به سرویس L2TP اضافه کنید.

   ```
   /ppp secret add name=myuser password=mypassword profile=default-encryption service=l2tp
   ```

   در اینجا، `myuser` و `mypassword` اطلاعات ورود کلاینت هستند.

6. **تنظیم مسیردهی**:

   برای اطمینان از دسترسی کلاینت‌ها به شبکه‌های محلی، باید مسیردهی را تنظیم کنید.

   ```
   /ip route add distance=1 dst-address=192.168.88.0/24 gateway=l2tp-out1
   ```

   در اینجا، `192.168.88.0/24` شبکه محلی شماست.

7. **دریافت وضعیت L2TP**:

   در قسمت PPP > L2TP Server، می‌توانید وضعیت اتصالات L2TP را ببینید.

   ```
   /interface l2tp-server server print
   /ppp secret print
   ```

در نهایت، حالا کلاینت‌های شما می‌توانند از طریق نرم‌افزار مشخص بر روی دستگاه‌های خود به سرور MikroTik متصل شوند. اطمینان حاصل کنید که کلاینت‌ها در تنظیمات خود از گواهی نامه‌های CA و سرور استفاده می‌کنند.
